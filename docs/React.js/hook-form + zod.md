---
title: hook form + zod
parent: React.js
nav_order: 2
layout: default
---

<p>
    <img alt="Static Badge" src="https://img.shields.io/badge/react--hook--form-7.43.9-%23EC5990?logo=reacthookform&logoColor=%23fff&labelColor=%23EC5990">
    <img alt="Static Badge" src="https://img.shields.io/badge/v3-%233E67B1?logo=zod&logoColor=%23fff&label=zod&labelColor=%233E67B1">
</p>

### 관련문서

- [zod docs]

<h1 style="color:#0c0c0c;font-weight:500;">react hook form + zod</h1>

서버와 data를 주고받는게 대부분인 작업에서   
가장 빈번하고 중요한 작업 중 하나가 request에 실려가는 값들의 유효성 검사다.   

기존에는 `react hook form` 만 활용해서 `form data` 를 핸들링 했다.   
(정규식, validation 함수 등등 직접 만들어서 사용했음)  

값들에 유효성 검사를 좀 더 쉽고 간결하게 하기위한 외부 라이브러리는 많이 존재한다.    
`react hook form` 에서도 외부 라이브러리를 편하게 사용하라고 함수 [resolver] 를 제공 해주고 있다.

여러 대표적인 라이브러리 중에서도 `zod`를 채택해서 현재 진행중인 프로젝트에서 유용하게 사용중이다.   
선택한 이유는  `typescript` 도입시 호환성, 그리고 러닝커브 및 코드의 간결성이 가장 좋다고 생각했다.

앞으로도 개발할때 가장 많이 이용할 것 같아서 그때그때 사용했던 패턴이나 코드를 정리 해보려고 한다.

## react hook form 과 zod 사용시 기본 컴포넌트 구조
``` text
    component
        - component.jsx
        - schema.js
        - useForm.js (hook form form x , custom hook)
        - ...
```

### component.jsx

- `custom hook` 에서 가져온 `form` 을 기반으로 필드 데이터 구성

```jsx
import { FormProvider } from 'react-hook-form'

const Component = () => {

    const {form, handleSubmit} = useCustomForm()
    ...

    return (
        <FormProvider {...form}>
            {/* fields UI ... */}
        </FormProvider>
    )
}
```

### useFrom.js (custom hook)

- `react hook form` 의 `useForm.js` 훅을 바탕으로 이루어진 커스텀 훅
- 여기서 form 초기화 및 submit 핸들링
- 서버와 통신도 여기서 모두 작업 함
- `useForm.js` 훅을 바탕으로 작업하기 때문에 `FormProvider` 즉 form 내부에서만 사용되어야 한다.

```js
import { initValues, schema } from '../_schema'

const useCustomForm = () => {
    const form = useForm({ mode: 'onChange', defaultValues: initValues, resolver: zodResolver(schema) })
    
    const { data } = useQuery('query-key', getApi)
    const { mutate: update, isLoading: updateLoading } = useMutation(postApi, {
        onSuccess: () => {},
    })

    const onSubmit = data => {
        update(data)
    }

    useEffect(() => {
        if (data) {
            form.reset({ ...initValues, ...data })
        }
    }, [data])

    return {
        form,
        handleSubmit: form.handleSubmit(onSubmit)
    }
}
```

### schema.js

- 필드 초기값과 각 필드값의 유효성 정의 (zod)
- 필요에 따라 정규식 패턴 + 유효성검증 callback 함수 정의

```js
import z from 'zod'

const initValues ={
    /** fields... */
}

const schema = z.object({
    key : z.string().trim()
    key2 : z.boolean()
    ...
})
```

[zod docs]: https://v3.zod.dev/
[resolver]: https://react-hook-form.com/docs/useform#resolver

