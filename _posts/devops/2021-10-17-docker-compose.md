---
layout: post
title: Docker - Docker Compose
category: devOps
permalink: /devops/:year/:month/:day/:title/
tags: [devOps]
comments: true
---

---

## Docker - Docker Compose 작성

---

### 1. Docker Compose란?

- Docker Composes는 여러 컨테이너를 모아서 관리하기 위한 툴
- FE, BE, DB를 각각 docker 컨테이너로 작성하고, 연결하여 동작하게 하려면 컨테이너 관리 툴이 필요하다
- Docker Compose는 docker-compose.yml 파일을 작성하여 실행한다
- docker-compose.yml 파일은 **YAML(야멜)** 형식으로 작성한다
  - YAML 문법
  - key, value, 들여쓰기를 중심으로 하는 문법
  - `---`  : 문서의 시작을 나타냄
  - `...` : 문서의 끝을 나타냄
  - `key:value`
  - int, string, boolean 타입 지원

---

### 2. Docker Compose 포맷

* 기본적으로 **version, services, volumes, networks** 4가지 큰 카테고리로 작성

* volumes는 컨테이너 설정에서 volumes로 선언이 가능

* networks는 컨테이너간 네트워크 분리를 위한 추가 설정

* version

  * Docker Compose 파일 포맷 버전 지정
  * docker 버전에 따라 지원하는 Docker Compose 버전이 있으며 기본적으로 3 사용

* services

  * 여러개 또는 하나의 컨테이너를 설정

  * `build` : 이미지를 Dockerfile 기반으로 작성 시 사용

    * `context` : Dockerfile이 있는 폴더
    * dockerfile: Dockerfile 파일명

  * `links` : 컨테이너 내부에서 다른 컨테이너를 접속하고 싶을때 사용

  * `container_name` : 생성될 컨테이너 이름 지정

  * `depends_on` : 먼저 실행되어야하는 컨테이너 지정

  * `image` : 베이스 이미지 설정

  * `restart` : 컨테이너 다운 시, 재시작하는 설정

  * `volumes` : docker run 옵션의 -v 옵션과 동일

  * `environment` : Dockerfile의 ENV 옵션과 동일

  * `env_file` : 환경변수를 담은 파일을 읽어들일 수도 있다

  * `ports` : docker run 옵션의 -p 옵션과 동일

    * YAML에서는 `aa:bb`를 시간으로 해석하기에 `"aa:bb"`로 적는다

  * ```yaml
    version: "3"
    
    services:
      app:
        build:
          context: ./경로
          dockerfile: Dockerfile
        links:
          # db 컨테이너를 app 컨테이너에서 사용 (mysqldb 이름으로)
          - "db:mysqldb"
        ports:
          - "80:8080"
        container_name: appcontainer
        depends_on:
          # db 컨테이너 생성 후 app 컨테이너 실행
          -db
      db:
        image: mysql:5.7
    	restart: always
    	volumes:
    	  - ./mysqldata:/var/lib/mysql
    	environment:
    	  - MYSQL_ROOT_PASSWORD=123123
    	  - MYSQL_DATABASE=mywebdatabase
        env_file:
    	  - ./mysql_env.env
    	ports:
    	  - "3306:3306"
        container_name: dbcontainer
    ```

---

### 3. Docker Compose 주요 명령어

* `docker-compose up` : docker 이미지를 만들어 실행
  * `-d` : 백그라운드로 실행
  * `--build` : 재빌드 시 옵션 추가, 안적으면 이미 작성된 이미지를 사용
* `docker-compose stop` : 중지
* `docker-compose down` : up 명령으로 생성된 컨테이너 삭제
* `docker-compose logs` : 각 컨테이너의 모든 로그 출략
* `docker-compose config` : 실행중인 Docker Compose의 docker-compose.yml 설정 확인
* `docker-compose exec 컨테이너명 ` : 실행중인 컨테이너에 명령어 실행

---

### 4. dockerignore

* `.dockerignore` 파일을 작성하면 그 폴더를 COPY 할 시, 특정 파일/폴더를 제외하고 복사할 수 있게 해준다

<br>

---

## 참고자료

---

인프런 - 풀스택을 위한 도커와 최신 서버 기술(리눅스, nginx, AWS, HTTPS, flask 배포) [풀스택 Part3]
