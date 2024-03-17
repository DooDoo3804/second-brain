---
title: NestJS introduction
tags:
  - nestjs
---
## NestJS 란

Node.js를 사용하여 효율적으로 server-side-application을 구축할 수 있게 해주는 프레임워크다. 보통 [[TypeScript]]를 사용하여 구축한다. OOP (Object Oriented Programming), FP (Functional Programming), and FRP (Functional Reactive Programming)를 결합하여 구축한다는 특징을 갖고 있다.

> [!note]
>모든 내용은 [NestJs 공식문서](https://docs.nestjs.com/)를 참고하여 작성된다.

## NestJS 설치

```bash
npm i -g @nestjs/cli
```

위 명령어를 입력해서 nestjs를 설치한다.

Nest의 구조는 크게 Controller, Module, Provider, middleware 로 나뉘고 자세한 내용은 [[Nest Module]] 페이지를 참고하면 된다. 아래 명령어로 nestjs 프로젝트를 생성하고, 각 요소들을 생성할 수 있다.

```bash
nest new [project-name]
nest g module [module-name]
nest g co [controller-name]
nest g s [service-nname]
```