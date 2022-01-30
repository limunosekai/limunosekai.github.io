---
layout: post
title: React-Query 사용법
category: React
permalink: /react/:year/:month/:day/:title/
tags: [React]
comments: true
---

---

## React-Query 사용법

---

### 1. 기본 사용

- `staleTime` : re-fetch에 대한 trigger로 사용된다
- `cacheTime` : 컴포넌트 unmount 후에 데이터를 언제까지 들고 있을 것인지
- `isFetching`
- `isLoading`
- re-fetch되는 경우(<u>stale 일 때</u>)
  - query의 새 인스턴스 마운트 시
  - useQuery를 호출하는 컴포넌트 마운트 시
  - 윈도우 refocus 시
  - 네트워크가 다시 연결될 시
  - `refetchInterval`로 re-fetch 강제 실행
    - Automatic Polling
- queryClient의 옵션으로 re-fetch를 핸들링 할 수 있다
  - `refetchOnMount`
  - `refetchOnWindowFocus`
  - `refetchOnReconnect`

### 2. Query Key

* 쿼리를 캐싱하고 공유하기 위해 고유키를 사용한다
* 날짜, 국가, 유저 등 고유 식별 데이터를 쿼리키에 사용하여 개인화 하는것이 중요
* 사내 서비스가 국가별로 다른 도메인을 가지고 있는데 단일한 쿼리키를 사용해서 다른 국가에서도 캐싱된 이전 국가의 데이터가 불려와지는 이슈가 있었음
* 이 쿼리키로 QueryClient에 저장된 데이터를 외부에서 꺼내올 수도 있어서 마치 redux같이 사용이 가능하다

### 3. Pre-fetch

* **prefetchQuery** : queryClient의 메서드로 서버에서 데이터를 미리 받아서 캐시에 저장
  * useQueryClient 훅을 사용하여 생성된 queryClient에 접근할 수 있다
  * prefetch 커스텀 훅을 작성하여 사용
  * 사용자가 미리 들어갈만한 탭의 데이터를 초기에 캐시에 저장하여 그 탭에 들어갔을 시, 로딩을 기다릴 필요가 없다
  * cacheTime 안에 들어가면 캐싱된 데이터를 사용하지만 지나면 새로 re-fetch 해야함 (default : 5분) 

```react
import { useQuery, useQueryClient } from 'react-query';

async function getItems() {
    const { data } = await axios.get('/api');
    return data;
}

export function useItemsQuery() {
    const fallback = [];
    const { data = fallback } = useQuery('key', getItems);
    return data;
}

export function usePrefetchItems() {
    const queryClient = useQueryClient();
    queryClient.prefetchQuery('key', getItems);
}

// ---------------------------------------------------------

function App() {
    usePrefechtItems();
    
	return (
    	// ...
    )
}
```



* **setQueryData** : queryClient의 메서드로 클라이언트의 데이터를 캐시에 저장
* **placeholderData** : useQuery 옵션으로 initialData 옵션과 유사하게 데이터가 이미 있는것처럼 동작하지만 캐시에 저장 X
* **initialData** : useQuery 옵션으로 클라이언트의 데이터를 사용하며 캐시에 저장

### 4. Mutation

* **useMutation** : 
  * 서버의 데이터를 바꾸는 네트워크 호출을 생성한다 (mutate function)
  * Query Key가 필요하지 않음
  * isLoading은 있지만 isFetching이 없다
  * 실패 시, default로 3번 retry하는 useQuery와 다르게 <u>retry하지 않는게 default</u>

```react
import { useMutation } from 'react-query';

async function deletePost(postId) {
  const response = await fetch(
    `https://jsonplaceholder.typicode.com/postId/${postId}`,
    { method: 'DELETE' }
  );
  return response.json();
}

export function App() {
    const { mutate, isLoading, isError, isSuccess } = 
          useMutation((id) => deletePost(id));
    
    return (
    	<>
        	<button onClick={() => mutate(id)}>
        		Delete
        	</button>
        	{isLoading && <p>Loading...</p>}
			{isError && <p>Error...</p>}
			{isSuccess && <p>Success...</p>}
        </>
    )
}
```

<br>

* 데이터 업데이트 시, queryKey prefix를 사용해 invalidate할 수 있다
* 해당 prefix가 붙은 query들을 re-fetch하여 데이터를 다시 받아온다
* queryClient의 `invalidateQueries` 사용

```react
const queryClient = useQueryClient();

const { mutate } = useMutation(
	(id) => somethingUpdate(id),
    onSuccess: () => {
    	queryClient.invalidateQueries(['queryKey']);
	},
);
```

<br>

### 5. Infinite Scroll

* **useInfiniteQuery** :  data 안에 `pages`, `pageParams` 두 가지 속성을 가진다
* 각 쿼리의 페이지 데이터들은 `pages` 배열 안에 들어있다
* `pageParams`는 각 페이지를 가져오는데 사용하는 파라미터를 포함한다
* `pageParam`의 current value는 react-query가 관리한다
* `getNextPageParam` 옵션은 pageParam을 업데이트한다
* `fetchNextPage` 함수로 다음 데이터를 가져올 수 있다
* `hasNextPage` 은 `getNextPageParam` 함수의 return value를 base로 한다
* `isFetchingNextPage`으로 일반 fetching인지 다음 페이지를 가져오는 fetching인지 구별할 수 있다

```react
import { useInfiniteQuery } from 'react-query';
import InfiniteScroll from 'react-infinite-scroller';
import { Person } from './Person';

const initialUrl = 'https://swapi.dev/api/people/';
const fetchUrl = async (url) => {
    const res = await fetch(url);
    return res.json();
};

export function App() {
    const { data, fetchNextPage, hasNextPage, isLoading, isFetching } =
          useInfiniteQuery(
          	'sw-people',
            ({ pageParam = initialUrl }) => fetchUrl(pageParam),
            {
                getNextPageParam: (lastPage) => lastPage.next || undefined,
            }
          );
    
    if (isLoading) return <div>Loading...</div>;
    
    return (
    	<>
        	{isFetching && <div>Loading...</div>}
			<InfiniteScroll loadMore={fetchNextPage} hasMore={hasNextPage}>
				{data.pages.map((pageData) => {
        			return pageData.results.map((person) => {
                        return (
                        	<Person 
                            	key={person.name}
                                name={person.name}
                            />
                        )
                    })
    			})}
			</InfiniteScroll>
        </>
    )
}
```

* 페이지 렌더링 시, data가 없으므로 isLoading 처리 필수 (초기 data.pages = undefined)
* 인피니트 쿼리가 실행되며 처음엔 `pageParam`이 없으므로 `initialUrl`을 사용
* `getNextPageParam`에 의해 다음 `pageParam`이 이전 페이지의 next url로 세팅이 된다
* 어느 순간 lastPage.next가 없으면 `hasNextPage`가 undefined로 세팅된다
* react-infinite-scroller 패키지 사용

<br>

### 6. useIsFetching & useIsMutating

* 앱의 규모가 클 때, 여러곳에서 사용하는 react-query의 loading, fetching 상태를 하나의 hook으로 관리할 수 있다
* useIsFetching은 react-query에서 제공하는 hook으로 현재 loading, fetching 중인 쿼리의 수를 반환한다
* useMutation은 useIsMutating을 사용
* 이 값이 0이 되면 현재 돌아가고 있는 쿼리가 없다는 뜻이다
* 옵션으로 `queryKey`를 줘서 특정 쿼리만 트래킹하거나 `filters` 옵션으로 특정 쿼리를 필터링 할 수 있다

```react
import { useIsFetching, useIsMutating } from 'react-query';

export function App() {
    const isFetching = useIsFetching();
    const isMutating = useIsMutating();
    
    // isMutating, isFetching이 0이면 false이므로 활성화된다
    const disabled = isFetching || isMutating ? true : false;
    
    return (
        <div>
          <button disabled={disabled}>버튼</button>
        </div>
    );
}
```

<br>

### 7. Error 핸들링

* useQuery의 옵션인 `onError`로 에러 핸들링 할 수 있다

```react
import { useQuery } from 'react-query';

export function useItems() {
	const fallback = [];
	const { data = fallback } = useQuery('key', fetch, {
		onError: (err) => {
			const title = err instanceof Error ? err.message : 'ERROR!';
            // toast 등 에러처리
		},
	})
}
```

<br>

* useQuery 사용 시마다 각각 에러 핸들링을 하는 방법 외에 queryClient의 옵션을 사용해서 보다 중앙 집중적으로 사용할 수 있다

```react
import { QueryClient } from 'react-query';

function queryErrorHandler(err) {
    // 쿼리 에러 시, 실행할 코드
}

function mutationErrorHandler(err) {
    // 뮤테이션 에러 시, 실행할 코드
}

export const queryClient = new QueryClient({
    defaultOptions: {
        queries: {
            onError: queryErrorHandler, 
        },
        mutations: {
          	onError: mutationErrorHandler, 
        },
    }
})
```

<br>

* 이 밖에도 react의 useErrorBoundary와 함께 사용하는 방법 등이 있다

<br>

### 8. Custom Hook

* useQuery 커스텀 훅 만들어 사용하기

```react
import { useQuery } from 'react-query';

async function fetch() {
    const { data } = await axios.get('/api');
    return data;
}

export function useItems() {
    const fallback = [];
    const { data = fallback } = useQuery('key', fetch);
    return data;
}

// -------------------------------------------------------------------------

// 컴포넌트에서 사용하기
export function App() {
    const items = useItems();
    
    return (
        <>
        	{items.map((v) => <div key={v.id}>{v.name}</div>)}
        </>
    )
}

```

<br>

### 9. Filtering with the select option

* useQuery의 select 옵션으로 데이터를 가공할 수 있다
* select에는 함수를 적는데 fetching 후의 데이터를 인자로 받고 가공 후 반환하면 useQuery의 data로 다시 간다
* <u>데이터가 바뀌거나 함수가 바뀌면 select가 실행된다</u>
* select function 작성 시, react 동작방식에 의해 매번 새로운 function이 되므로, useCallback으로 감싸준다

<br>

### 10. Optimistic UI

* **Optimistic UI** : 서버 한 번 갔다 오면 UI 변화가 느려서 미리 바뀔 것이라 가정하고 UI부터 바꾸고 API 호출을 하는 것
* API 호출이 실패하면 낭패를 보므로 rollback을 위해 query를 cancel해야한다

* React-Query는 query를 cancel하기 위해 **AbortController**를 사용한다
* `AbortController`를 사용하기 위해서는 React-Query version `3.30.0` 이상이어야함
* `AbortSignal`을 사용하기 위해서는 axios version `0.22.0` 이상이어야 함

```javascript
async function getItems(signal) {
    const { data } = await axios.get('/api', {
        signal,
    });
    
    return data;
}

const { data } = useQuery(
    'queryKey',
    ({ signal }) => getItems(signal);
)
```

* query를 cancel하면 AbortController에 cancelEvent가 전달되고 signal을 구독하고 있는 모든 API 호출이 중단된다
* queryClient의 `cancelQueries`를 사용한다
* Optimistic Update Flow
  * useMutation 실행
  * onMutate로 일단 쿼리를 취소한다
  * 이전 데이터를 스냅샷한다
  * 새 데이터로 setQueryData를 사용하여 캐시 업데이트
  * 에러 발생 시 이전 데이터로 롤백
  * onSettled로 invalidate 실행 (onSuccess, onError 둘 다 실행된다)

```javascript
const { mutate } = useMutation(
	(newUserData) => patchSomething(newUserData);
    {
    	// context를 return하여 onError에게 전달
    	onMutate: async (newData) => {
    		// 나가는 쿼리를 우선 취소
    		queryClient.cancelQueries('queryKey');
    
   			// 이전 데이터 스냅샷
    		const previousUserData = queryClient.getQueryData('queryKey');
    
    		// 캐시 업데이트 (Optimistically)
    		queryClient.setQueryData('queryKey', newData);
    
    		return { previousUserData };
		},
      	onError: (err, newData, context) => {
            // 캐시 롤백
            if (context.previousUserData) {
                queryClient.setQueryData('queryKey', context.previousUserData);
            }
        },
        onSuccess: () => {},
        onSettled: () => {
          // invalidate
          queryClient.invalidateQueries('queryKey');
        },
    },
);
```

<br>

---

## 참고 자료

---

[React Query: Server State Management in React](https://www.udemy.com/course/learn-react-query/)
