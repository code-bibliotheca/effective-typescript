# 아이템 45. devDependencies에 typescript와 @types 추가하기

타입스크립트를 프로젝트에서 사용하려 할 때 타입스크립트 자체 의존성을 고려해야 함

## typescript

시스템 레벨로 설치할 수도 있지만,
- 팀원들 모두가 동일한 버전을 사용한다는 보장 X
- 프로젝트별로 서로 다른 셋업

의 두 가지 이유로 devDependency에 추가해 프로젝트를 공유하는 사람들끼리 동일한 버전, 동일한 세팅을 사용하는 것을 권장함


## @types

> [DefinitelyTyped](https://github.com/DefinitelyTyped/DefinitelyTyped) : js 라이브러리의 타입 정의 모음

라이브러리 내에 타입 선언이 포함되어있지 않더라도 @types/library-name 의존성을 추가함으로써 타입 의존성 추가 가능!

e.g.
```text
{
    "devDependencies": {
        "@types/react": "^16.8.19",
        "typescript": "^3.5.3",
    },
    "dependencies": {
        "react": "^16.8.6"
    }
}
```

위와 같이 의존성을 추가한 경우엔 타입스크립트를 개발 시 사용하지만 런타임에 의존하지 않겠다는 뜻
