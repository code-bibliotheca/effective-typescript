# 아이템 44. 타입 커버리지를 추적하여 타입 안전성 유지하기

noImplicitAny를 설정해도 명시적 any 타입이나 서드파티 타입 선언에는 여전히 any 타입이 존재하며
코드 전반에 영향을 미칠 수 있다.

## [type-coverage](https://github.com/plantain-00/type-coverage)

타입 스크립트 프로젝트 내에서 사용된 모든 타입 중 any의 별칭이 아닌 타입의 비율을 %로 나타내주는 라이브러리로 다음 명령문을 통해 확인할 수 있다.

```text
$ npx type-coverage
$ npx type-coverage --detail // any 타입이 있는 곳을 모두 출력해줌
```

이렇게 찾을 수 있는 any 타입들과 수정법을 살펴보자.

### 1. 코드 내 return type이 any인 경우

일반적인 경우 적절한 타입을 지정해줌으로써 해결 가능

```ts
function getColumnInfo(name: string): any {
    return utils.buildColumnInfo(appState.dataSchema, name) // which returns any;
}
```

### 2. 서드 파티 라이브러리의 any 타입

라이브러리에 .d.ts 가 정의되어있지 않거나 declare module로 타입이 지정된 경우 import한 모든 심벌은 any 타입이 됨

이 경우 공식 타입 선언이 릴리즈 되었는지 확인해보고 없다면 직접 사용하려는 타입을 지정해 사용 가능

### 3. 라이브러리 타입에 버그가 있는 경우

함수가 반환하는 값과 타입이 실제로 일치하지 않는 경우 어쩔 수 없이 any 사용

하지만 이럴 때 issue를 등록해서 contributor가 되봅시다
