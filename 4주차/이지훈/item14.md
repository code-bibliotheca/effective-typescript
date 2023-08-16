## item14 : 타입 연산과 제너릭 사용으로 반복 줄이기

**DRY**(Don't Repeat Yourself)

시스템 내에서 특정한 기능이나 로직은 단 한곳에서 명확하고 신뢰할 수 있게 존재해야 한다는 뜻

비슷한 코드가 반복된다면, 수정이 일어날 경우 반복되는 모든 곳에서 동일하게 수정을 해줘야 하고 실수할 확률이 올라감

<hr>

### 타입에 이름 붙이기

타입에 이름을 붙여주면 반복이 준다!

```typescript
function distance(a: { x: number; y: number }, b: { x: number; y: number }) {
  return Math.sqrt(Math.pow(a.x - b.x, 2) + Math.pow(a.y - b.y, 2));
}
```

```typescript
interface Point2D {
  x: number;
  y: number;
}

function distance(a: Point2D, b: Point2D) {
  return Math.sqrt(Math.pow(a.x - b.x, 2) + Math.pow(a.y - b.y, 2));
}
```

중복된 타입은 문법에 의해 잘 안 보이기도함

```typescript
function get(url: string, opts: Options): Promise<Response> {
  /* ... */
}
function post(url: string, opts: Options): Promise<Response> {
  /* ... */
}
```

```typescript
type HTTPFunction = (url: string, opts: Object) => Promise<Response>;
const get: HTTPFunction = (url, opts) => {
  /* ... */
};
const post: HTTPFunction = (url, opts) => {
  /* ... */
};
```

> 같은 타입 시그니처를 공유한다면 해당 시그니처를 명명된 타입으로 분리

</br>

## 타입의 확장

```typescript
interface Person {
  firstName: string;
  lastName: string;
}

interface PersonWithBirthDate {
  firstName: string;
  lastName: string;
  birth: Date;
}
```

```typescript
interface Person {
  firstName: string;
  lastName: string;
}

interface PersonWithBirthDate extends Person {
  birth: Date;
}
```

> 한 인터페이스가 다른 인터페이스를 확장하여 반복을 제거

```typescript
type PersonWithBirthDate = Person & { birth: Date };
```

> 이미 존재하는 타입을 확장할 때, 일반적이지는 않지만 인터섹션 연산자를 사용할 수 있음

</br>

## Pick 의 사용

```typescript
interface State {
  userId: string;
  pageTitle: string;
  recentFiles: string[];
  pageContents: string;
}

interface TopNavState {
  userId: string;
  pageTitle: string;
  recentFiles: string[];
}
```

State 인덱싱

```typescript
interface TopNavState {
  userId: State["userId"];
  pageTitle: State["pageTitle"];
  recentFiles: State["recentFiles"];
}
```

매핑된 타입을 사용하면 좀 더 코드가 깔끔해짐

```typescript
type TopNavState = {
  [k in "userId" | "pageTitle" | "recentFiles"]: State[k];
};
```

마우스를 올리면 기존의 type 과 동일한 것을 알 수 있음

<img width="647" alt="스크린샷 2022-10-05 오전 1 34 57" src="https://user-images.githubusercontent.com/76726411/193876277-83d5e431-d691-43b1-a73e-187f74ab618f.png">

매핑된 타입은 배열의 필드를 루프 도는 것과 같은 방식, 이 패턴은 표준 라이브러리에서도 일반적으로 찾을 수 있으며, PICK 이라고 함

```typescript
// 완벽한 정의는 아님
type Pick<T, K> = {
  [k in K]: T[k];
};
```

```typescript
type TopNavState = Pick<State, "userId" | "pageTitle" | "recentFiles">;
```

> 여기서 Pick은 제너릭 타입 => 함수에서 두 개의 매개변수 값을 받아서 결괏값을 반환하는 것처럼, Pick은 T,K 두 가지 타입을 받아서 결과 타입을 반환

</br>

## Partial의 사용

생성하고 난 다음에 업데이트가 되는 클래스를 정의한다면, update 메서드 매개변수 타입은 생성자와 동일한 매개변수이면서, 타입 대부분이 선택적 필드가 됨

```typescript
interface Options {
  width: number;
  height: number;
  color: string;
  label: string;
}
interface OptionUpdate {
  width?: number;
  height?: number;
  color?: string;
  label?: string;
}
class UIwidget {
  constructor(init: Options) {...}
  update(options: OptionUpdate){...}
}
```

매핑된 타입과 `keyof`를 사용해 Options로부터 OptionsUpdate를 만드는 방법

```typescript
type OptionsUpdate = { [k in keyof Options]?: Options[k] };
```

`keyof`는 타입을 받아서 속성 타입의 유니온을 반환

```typescript
type OptionsKeys = keyof Options;
// 타입이 "width" | "height" | "color" | "label"
```

</br>

## 제너릭 타입에서 매개변수를 제한하는 방법

제네릭 타입은 타입을 위한 함수와 같음

함수는 코드에 대한 DRY 원칙을 지킬 때 유용하게 사용됨

함수에서 매개변수로 매핑할 수 있는 값을 제한하기 위해 타입 시스템을 사용

extends 를 사용해 제네릭 타입에서 매개변수를 제한할 수 있음

```typescript
interface Name {
  first: string;
  last: string;
}
type DancingDuo<T extends Name> = [T, T];

const couple1: DancingDuo<Name> = [
  { first: "Fred", last: "Astaire" },
  { first: "Ginger", last: "Rogers" },
];

const couple2: DancingDuo<{ first: string }> = [
  // ~~~ 'Name' 타입에 필요한 'last' 속성이  {first: string} 타입에 없습니다.
  { first: "Fred" },
  { first: "Ginger" },
];
```

앞에서 나온 Pick의 정의도 extends를 사용해서 완성할 수 있습니다.

```typescript
type Pick<T, K> = {
  [k in K]: T[k];
};
```

기존 정의에서 K는 T와 무관하고 범위도 너무 넓음

K는 실제로 T의 key의 부분집합, 즉 keyof T 가 되어야 함

```typescript
type Pick<T, K extends keyof T> = {
  [k in K]: T[K];
};
```
