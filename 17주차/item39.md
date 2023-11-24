# 아이템 39. any를 구체적으로 변형해서 사용하기

any는 자바스크립트의 모든 값을 아우르는 매우 큰 범위의 타입이기 때문에 단순히 any를 쓰기보단 좀 더 구체적인 any를 쓰는 것을 권장한다.

```ts
function getLengthBad(array: any) {
  // 1
  return array.length;
}

function getLength(array: any[]) {
  // 2
  return array.length;
}

// 사실 타입이 정말 중요하다면 제너릭을 쓰겠지만... length에는 의미가 없을 듯?
function getLength<T>(array: T[]) {
  return array.length;
}
```

2번의 경우 array.length 타입이 체크되고, 반환타입이 any 대신 length인 number로 추론되며 함수 호출 시 매개변수가 배열인지 체크되기 때문에 2번과 같이 쓰는 것이 더 좋다.

함수의 매개변수를 구체화하는 경우,

> 요소의 타입과 관계없이 배열의 배열 형태라면 => any[ ][ ]

> 객체이지만 값을 알 수 없다면 => { [key: string]: any }

```ts
function hasTwelveLetterKey(o: { [key: string]: any }) {
  for (const key in o) {
    if (key.length === 12) {
      return true;
    }
  }
  return false;
}
```

인덱스 시그니처 대신 **모든 비기본형(non-primitive) 타입을 포함하는 object 타입**을 사용할 수도 있지만, **_object 타입의 경우 속성에 접근할 수 없음!_**

```ts
function hasTwelveLetterKey(o: object) {
  for (const key in o) {
    if (key.length === 12) {
      console.log(key, o[key]); // ~ '{}' 형식에 인덱스 시그니처가 없으므로 요소에 암시적으로 any 형식이 있습니다.
      return true;
    }
  }
  return false;
}
```
