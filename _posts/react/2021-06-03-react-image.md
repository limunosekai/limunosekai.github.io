---
layout: post
title: react image download 구현
category: React
permalink: /react/:year/:month/:day/:title/
tags: [React]
comments: true
---

---

## react image download 구현

---

리액트로 짤방 검색기 프로젝트를 진행하면서 `google custom search API`로 검색한 짤 이미지를 다운로드하는 기능을 넣어야했다. 몇일 동안의 삽질과 수많은 시도 끝에 구현은 성공했다.

우선은 실패 사례부터 보자면  

1. a 태그에 download attribute를 추가하여 다운로드하는 방법
2. DOM 자체를 image로 변환하여 다운로드하는 방법
3. 이미지를 canvas에 옮겨 그리고 base64 형태로 변환하여 다운로드하는 방법
4. 이미지의 crossOrigin을 변경하여 canvas에 그리고 blob 형태로 변환하여 다운로드하는 방법

기억나는건 이정도인데 이 외에도 다양한 시도를 했던것 같다.

<br>

일반적으로 a 태그에 HTML5에서 지원하는 download attribute를 추가하면 된다고 하지만 내 프로젝트 같은 경우에는 이미지 링크가 외부 경로여서 이미지를 다운로드 하는 순간 그냥 브라우저에 이미지가 열리고 끝이 났었다.

코드는 다음과 같다.

```javascript
const el = document.createElement('a');
el.href = url;
el.download = 'example.png';
el.click();
```

a 태그를 생성하고 href를 달아 다운로드할 파일명을 추가한 뒤 강제로 a태그를 클릭하여 다운로드 하는 방식이다.

## 실패

<br>

다음은 `domtoimage` 라이브러리를 사용하여 DOM 자체를 image(blob)로 변환하여 다운로드하는 방법이다.

다운로드는 `file-saver` 라이브러리의 saveAs 메소드를 사용하였다.

```javascript
domtoimage
  .toBlob(document.querySelector('#id'))
  .then((blob) => {
    saveAs(blob, 'example.png');
   })
  .catch((err) => {
    console.log(err);
   });
```

img 태그가 아닌 다른 곳에서 사용하면 정상적으로 이미지가 다운로드되지만 외부경로가 달린 img 태그에서 사용하면 공포의 CORS 이슈가 터지고 말았다. 서버가 있다면 서버에서 이미지를 다운로드 받고 그 이미지를 클라이언트에서 다운로드 받으면 되지만 firebase를 이용한 serverless 프로젝트여서 CORS를 피해갈 순 없었다.

## 실패

<br>

html 태그를 canvas로 옮겨주는 `html2canvas` 라이브러리를 이용하여 이미지를 base64 형태로 변화 후 다운로드

```javascript
window.scrollTo(0, 0);
let url = '';
await html2canvas(document.getElementById('id')).then(
  async (canvas) => {
    url = await canvas.toDataURL('image/jpg').split(',')[1];
  }
);
```

이 방법도 역시 CORS 이슈 때문인지 변환한 이미지 데이터가 그냥 흰색뿐이었다.

## 실패

<br>

CORS 이슈를 피해가려면 이미지 자체에 crossOrigin attribute를 `anonymous`로 설정하면 된다고 한다.

```javascript
let img = new Image();
img.crossOrigin = 'Anonymous';
img.src = url;
let canvas = document.createElement('canvas');
canvas.width = 200;
canvas.height = 200;
let ctx = canvas.getContext('2d');
ctx.drawImage(img, 0, 0);
canvas.toBlob((blob) => {
  let link = document.createElement('a');
  link.download = 'example.png';
  link.href = URL.createObjectURL(blob);
  link.click();
  URL.revokeObjectURL(link.href);
}, 'image/png');
```

새 이미지를 만든 후 그 이미지를 이용한 캔버스를 그려 blob으로 다운로드 받는 방법이다.

CORS 이슈는 피해갈 수 있었지만 이미지가 출력되지 않고 빈 화면뿐이었다.

## 실패

<br>

서버가 없다면 서버의 역할을 대신할 이미지 호스팅 업체에 먼저 이미지를 올린 후 그곳의 이미지를 다운로드 받으면 되지 않을까? 라는 생각이 들었다.

이를 위해서 `cloudinary`라는 이미지 호스팅 서비스를 이용하기로 했다.

먼저 이미지 url로 form data 생성 후 cloudinary에 업로드를 실행한다.

업로드를 하게 되면 cloudinary에 올라간 이미지의 url을 반환받을 수 있는데, 이 때 그 url로 responseType을 blob으로 설정하여 다시 request를 요청한다. 

반환받은 결과로 이미지 다운로드를 진행한다.

반신반의로 시작했지만 어라? 되네

```javascript
const data = new FormData();
data.append('file', url);
data.append('upload_preset', UPLOAD_PRESET);
data.append('cloud_name', CLOUD_NAME);
fetch(`https://api.cloudinary.com/v1_1/${CLOUD_NAME}/image/upload`, {
  method: 'post',
  body: data,
})
  .then((resp) => resp.json())
  .then((temp) => {
    axios
      .get(temp.url, {
        responseType: 'blob',
      })
      .then((res) => saveAs(res.data, 'zzal.png'))
      .catch((err) => console.log(err));
  })
  .catch((err) => console.log(err));
```

한 번 우회를 한 작업이기 때문에 성능상으로는 떨어지지만 아무튼 성공해서 다행이다.

## 성공😊

<br>

cloudinary 용량 제한이 있어서 이 곳에 이미지를 올리고 다운로드 후에 해당 이미지를 지워야하지만 client-side의 delete는 보안상의 이슈로 구현이 까다롭다.

해당 부분은 추후 찾아봐야겠다.

<br>

---

## 참고 자료

---

[How to Upload Images to Cloudinary With a React App](https://medium.com/geekculture/how-to-upload-images-to-cloudinary-with-a-react-app-f0dcc357999c)

[axios blob request](https://codesandbox.io/s/confident-monad-pc6l9?file=/src/App.js:76-92)

[Deleting client-side uploaded assets](https://cloudinary.com/documentation/upload_images#deleting_client_side_uploaded_assets)
