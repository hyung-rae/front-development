# javascript time

## Runtime
- javascript 가 실행되는 시간
- 브라우저에서의 동작 / node.js로 javascript 파일 실행 순간
  
## Compile Time
- 여러 도구를 통해서 실행가능한 javascript 파일로 번역하는 순간
  - babel 를 통해서 js ES6 코드들을 구버전 으로 변환
  - typescript 로 작성된 파일을 번역하여 js로 변환 (typescript 에러는 그래서 컴파일 단게에서 발생한다.)
 
## Build Time
- 앱 전체를 배포하거나 실행 가능하게 만드는 것
- 이때 javascript 파일들은 vite, webpack 등의 도구를 통해 번들링 된다.
- Compile Time도 이 Build Time 안에서 실행된다.

### javascript는 인터프리터 언어이다
script 가 실행되면서 순차적으로 코드를 실행하는 언어지만,   
Babel, Typescript 같은 도구를 사용하면서 컴파일 타임이 생겼다.

###  브라우저는 html / css / javscript 파일만 받아서 실행한다.
즉 브라우저에서는 javascript 런타임만 존재 한다.

### Typescript 를 사용하는 이유
순수 js로 작성해서 바로 브라우저에게 건내면 런타임시 발견되는 타입 에러들이 존재할 수 있다.   
이걸 Compile 단계로 가져오면서 에러를 미리 처리해 결론적으로 좀 더 견고한 js를 만들수 있는것
