# 아이템 38. any 타입은 가능한 한 좁은 범위에서만 사용하기

## 함수와 관련된 any 타입 사용법

```ts
function processBar(b: Bar) { /* ... */ }

function f() {
    const x = expressionReturningFoo();
    processBar(x);
}
```

x라는 변수가 동시에 Foo 타입과 Bar 타입에 할당 가능하기 위해 다음 두 가지 타입 오류 제거 방식을 생각해볼 수 있다.

```ts
function f1() {
    const x: any = expressionReturningFoo(); // ...?
    processBar(x);
}

function f2() {
    const x = expressionReturningFoo();
    processBar(x as any); // 이게 차라리 낫다...
}
```

f1과 같은 코드는 변수 x를 사용하는 아래의 다른 코드에까지 영향을 미칠 수 있기 때문에 권장되지 않으며, f2와 같이 타입체커를 통과하기 위해
매개변수에서만 사용되도록 하는 것이 차라리 낫다. 더욱이 f가 x를 반환하는 함수라면 any 타입의 값을 반환하게 되므로 바깥에서 또다른 문제를 
야기할 수 있다. 따라서 any를 굳이 써야 한다면 좁혀 쓰되, 반환까지 한다면 return 타입을 명시해주자.

또 한 가지 방법으로 ***//@ts-ignore*** 주석을 사용하여 타입 체커가 이를 무시하도록 할 수도 있다. 하지만 타입체커를 통과하지 못했다는 것은
타입과 관련한 근본적인 문제가 있다는 것이므로 시간이 허락하는 한 해당 문제를 제대로 해결해야 나중에 생길 수 있는 더 큰 문제를 예방할 수 있다.

## 객체와 관련한 any 사용법

```ts
const config: Config = {
    a: 1,
    b: 2,
    c: {
        key: value
    }
};
```
만약 key 부분만 타입 체커를 통과하지 못하는 경우 any로 상황을 회피하려 한다면, 다음 두 가지 방식을 사용할 수 있다.(그냥 optional 속성으로 설정하면 안되나??)

```ts
const config1: Config = {
    ...,
    c: {
        key: value
    }
} as any; // ...?

const config2: Config = {
    ...,
    c: {
        key: value as any // 진짜 그나마 이게 낫다...
    }
};
```

> any 쓰지 맙시다.

