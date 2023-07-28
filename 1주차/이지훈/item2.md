# 타입스크립트 설정 이해하기

### tsconfig.json 을 이용해서 설정

<br/>

1. 커맨드 라인

```bash
tsc --noImplicitAny program.ts
```

2. tsconfig.json

```json
{
  "compilerOptions": {
    "noImplicitAny": true
  }
}
```

> `tsc --init`을 통해 기본 `tsconfig.json` 파일을 생성할 수 있다.

### Strictness (엄격한 타입)

[내가 사용하는 tsconfig.json](https://github.com/code-bibliotheca/effective-typescript/issues/2)

<hr>

## 요약

1. 자바스크립트 프로젝트를 타입스크립트로 전환하는 것이 아니라면 `noImplicitAny`, `strictNullChecks`를 설정하기

2. 타입스크립트에서 엄격한 검사를 하고 싶다면 strict 설정을 고려
