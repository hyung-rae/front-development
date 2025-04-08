---
title: getServerSideProps with HOC
parent: Next.js
nav_order: 2
layout: default
---
<p>
    <img alt="Static Badge" src="https://img.shields.io/badge/Next.js-13.5.4-blue?logo=nextdotjs&logoColor=%23fff&logoSize=auto&labelColor=%23000000">
</p>

### 관련문서 

- [Next getServerSideProps]


<h1 style="color:#4caf50;font-weight:500;">getServerSideProps with HOC</h1>

프로젝트 특성상 각 페이지별 seo 최적화 및 페이지 랜더링 전 서버로 부터 필요한 data fetch 가 필요함  
`getServerSideProps`로 각 페이지별 필요 데이터를 page props 전달 코드 존재   

```jsx

const Page = (props) => {
  return (
    <CommonSeo {...props}>
      <Page {...props} />
    </CommonSeo>
   )
}

export const getServerSideProps = async (context) => {
  /** 필요한 data fetch 하는 곳 */
  /** 중복된 코드도 엄청 많음 */
  return { props }
}

```
이런식으로 `getServerSideProps` 가 사용되는 페이지가 다수 존재   
중복되는 코드를 수행하는 부분도 다수 존재했음   

### 문제 발생

각 페이지에 페이지 활성화를 체크하는 로직이 생김   
서버 요구사항은 `/site` api 호출후 return 되는 status 값에 따라   
HTTP 상태 코드를 `416 (Requested Range Not Satisfiable)` 변경해주고   
불필요한 랜더링 하지 말기 원했음   

AWS 에서 변경된 416 코드를 받고 안내 페이지로 리다이렉트 시키는 작업

문제는 `getServerSideProps` 페이지가 다수 존재했고 일일히 다 복붙 넣어야하는 상황 발생

### HOC 패턴으로 공통로직 추가 + 확장성 
`getInitialProps` 를 통해서 app.jsx 한번만 선언하고 말까 하다가
여러 글을 읽고 `getServerSideProps` 를 공통 HOC 로 만들어 보기로 결정   

- `getInitialProps` 와 `getServerSideProps` 차이
  
    | 항목 | `getInitialProps` | `getServerSideProps` |
    |------|-------------------|----------------------|
    | 사용 위치 | `Pages`, `_app.tsx`, `_document.tsx` | `Pages` only |
    | 실행 시점 | 서버 or 클라이언트 모두 | 서버에서만 실행 |
    | Static Optimization 영향 | ❌ 비활성화됨 | ✅ 필요 시 SSR만 동작 |
    | 데이터 노출 가능성 | 클라이언트에서도 실행되므로 API 경로 노출 위험 | 서버에서만 실행되어 안전함 |
    | 권장 여부 | ❌ (구버전, 비추천) | ✅ (Next.js 권장 방식) |

### withServerSideProps

`getServerSideProps`를 인자로 받아 각 페이지별 로직 + 공통 로직 실행하는 컴포넌트

```jsx
/** server side props 공통 */
const withServerSideProps = (getServerSideProps) => {
  return async (context) => {
    let props ={}

    /** page 별 추가 server side props */
    const pageProps =
      typeof getServerSideProps === 'function'
        ? await getServerSideProps(context)
        : {}

    /** 사이트 활성화 체크 + sites props */
    const site = await fetch(context, `/sites`)
    if (site?.publishStatus === 'inactive') {
      context.res.statusCode = 416
      context.res.end() // 응답 종료
      return { props: {} }
    }

    /** 그 밖에 중복으로 계속 작성되었던 로직들 */
    ...

    return { props: { ...props, ...pageProps } }
  }
}

```

### page 별 적용
```jsx
const Home = (props) => {
  return (
    <CommonSeo {...props}>
      <Page {...props}/>
    </CommonSeo>
  )
}

export const getServerSideProps = withServerSideProps()

export default Home
```

### 페이지별 추가 로직이 필요한 경우
  
```jsx

export const getServerSideProps = withServerSideProps(async (context: any) => {
  /** 추가 로직 후 props에 포함될 객체 리턴 */
  return { board: context.params }
})

export default Home
```

[Next getServerSideProps]: https://nextjs.org/docs/pages/building-your-application/data-fetching/get-server-side-props
