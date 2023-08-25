## item16 : number 인덱스 시그니처보다는 Array, 튜플, ArrayLike를 사용하기

JS에서 객체의 key는 number로 사용해도 기본적으로 string으로 암묵적 형변환이 일어남!

TS에서는 이런 형변환으로 인해 혼란이 생길 수 있다 판단해 key에 number 타입을 지정하면 구별이 가능함

```ts
const xs = [1, 2, 3];
const x0 = xs[0];
const x1 = xs["1"]; // 인덱스 식이 'number' 형식이 아니므로 빨간줄이 나와야 하는데 사실 저는 안생김

function get<T>(array: T[], k: string): T {
  return array[k];
}
```

### 반복문에서의 number key

이렇게 TS에서는 number key라는 개념이 존재하지만 반복문은 런타임에 실행되기 때문에 결국 자바스크립트의 형변환에 의해 key는 string 타입을 갖게 됨.

따라서 인덱스 시그니처에 number를 제공은 하지만 쓰지 말고 \***_(타입이 지정된) Array나 튜플, ArrayLike 타입을 사용하자._**
