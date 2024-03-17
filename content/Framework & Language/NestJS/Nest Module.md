---
title: Nest Module
tags:
  - nestjs
---
## Module

모듈은 `@Module()` 데코레이터가 사용된 클래스이다. `@Module()` 데코레이터는 Nest를 구성하기 위한 어플리케이션 구조의 메타데이터를 제공한다. 각 어플리케이션은 적어도 하나의 루트 모듈(root module)을 갖고 있다. 

![[Pasted image 20240304213256.png]]

데코레이터는 다음의 속성들을 갖는다.

- providers : 이 모듈 전체에서 사용할 수 있게 해주고, Nest 주입자를 통해 인스턴스화된다.
- controllers : 인스턴스화된 컨트롤러의 정의들의 집합
- imports : 이 모듈에서 필요한 provider를 제공하는 다른 모듈을 주입한다.
- exports : provider의 서브집합이다. 이를 통해 다른 모듈에 주입하여 사용할 수 있게 한다.

## Controller

컨트롤러는 애플리케이션의 특정한 요청을 받는데 그 목적을 갖는다. 라우팅 시스템으로 각각의 컨트롤러에 들어온 요청을 처리할 수 있다. 클래스와 데코레이터를 사용하여 다양한 컨트롤러를 생성할 수 있다. 

![[Pasted image 20240304225714.png]]

### Routing

`@Contoller('{path}')` 를 통해 컨트롤러 전체의 end-point를 설정한다. CRUD를 구성하는 데코레이터는 `@Get()`, `@Post()`, `@Delete()`, `@Patch()`, 등이 있다. Http 요청 데코레이터들은 속성 값을 통해 특정한 값을 요청 받을 수 있다. 

| @Request(), @Req()      | req                             |
|:----------------------- |:------------------------------- |
| @Response(), @Res()     | res                             |
| @Param(key?: string)    | req.param / req.params[key]     |
| @Body(key?: string)     | req.body / req.body(key)        |
| @Query(key?: string)    | req.query / req,query(key)      |
| @Headers(name?: string) | req.headers / req.headers[name] |

아래는 예시 코드이다.

```ts
// sample code
```

## Provider

프로바이더(Provider)는 Nest의 근본적인 중요한 개념이다. Nest의 대부분의 기본적인 클래스들(service, repository, factory, helper ... )은 프로바이더를 통해 관리된다. 의존성을 통해 주입된다는 것이 프로바이더의 주 원리이다.

![[Pasted image 20240304230700.png]]

## Middleware

미들웨어는 요청이 들어올 때 라우트 핸들러(Route Handler)가 동작하기 전에 호출되는 함수를 말한다. 미들웨어는 response와 request 에 접근할 수 있다.

![[Pasted image 20240304230930.png]]