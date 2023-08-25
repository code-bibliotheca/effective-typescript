## item15 : 동적 데이터에 인덱스 시그니처 사용하기

JS는 문자열 키를 타입의 값에 관계없이 매핑하여 객체를 생성하는 문법이 간단한데, TS도 이에 맞춰 인덱스 시그니처를 활용해 동적으로 매핑을 표현할 수 있음

### 인덱스 시그니처

```ts
type Rocket = { [으어아: string]: string }; // 인덱스 시그니처!

const rocket: Rocket = {
  name: "Falcon 9",
  variant: "v1.0",
  thrust: "4,940 kN",
};
```

세 가지 요소로 구성됨

- key의 이름 : 키의 위치만 표시하는 용도, 타입체커에선 사용하지 않으며 아무거나 갖다붙여도 됨
- key의 타입 : string이나 number, symbol(객체의 키로 올 수 있는 식별자 타입)의 조합이어야 하지만 보통 string
- 값의 타입 : 어떤 것이든 올 수 있음

인덱스 시그니처는 동적으로 타입을 지정해줄 수 있다는 편리한 장점이 있지만 단점이 존재

#### 단점

- 잘못된 키를 포함해 모든 키를 허용. name 대신 Name이라 써도 오류가 나지 않음
- 특정 키가 필요하지 않아 {}도 유효한 Rocket 타입
- 키마다 다른 타입을 가질 수 없음
- 키 값으로 어떤 값이든 올 수 있기 때문에 자동완성 기능 제공 안됨

즉 "어떤 값이 올지 모르는 타입"을 지정해놨기 때문에 예측 불가능한 값이 올 수 있고, 이를 막을 수 없음

단점이 상당히 큰데... **언제 쓰는가?**

### 런타임 때까지 객체의 속성을 알 수 없는 경우

CSV 파일처럼 헤더 행에 열 이름이 있고, 데이터 행을 열 이름과 값으로 매핑하는 객체로 나타내고 싶은 경우, 실제 데이터가 들어오기 전까지 열 이름을 미리 알 방법이 없다면 임의로라도 타입을 지정해줘야 하는 경우가 생김

이 떄 아래처럼 인덱스 시그니처를 활용해 동적으로 타입을 지정해줄 수 있음

```ts
function parseCSV(input: string): { [columnName: string]: string }[] {
  const lines = input.split("\n");
  const [header, ...rows] = lines;
  const headerColumns = header.split(",");

  return rows.map((rowStr) => {
    const row: { [columnName: string]: string } = {};
    rowStr.split(",").forEach((cell, i) => {
      row[headerColumns[i]] = cell;
    });

    return row;
  });
}
```

이렇게 row data를 받은 다음에 타입 단언을 활용해 타입 지정하여 사용하면 됨

```ts
interface ProductRow {
  productId: string;
  name: string;
  price: string;
}

const csvData = `productId,name,price
550e8400-e29b-41d4-a716-446655440000,apple,3200
df6fdea1-10c3-474c-ae62-e63def80de0b,banana,4300`;

const products = parseCSV(csvData) as unknown as ProductRow[]; // 여기서 unknown 안쓰면 타입체커 에러
```

### 그래도 찝찝한데...

인덱스 시그니처는 너무 광범위한 타입을 허용하고, 그렇다고 가능한 모든 타입을 지정해주기엔 많은 번거로음이 있어 이런 경우에 두 가지 대안이 존재함

#### Record 사용하기

Record는 키 타입에 유연성을 제공하는 제너릭 타입

```ts
type Vec3D = Record<"x" | "y" | "z", number>;
// 위 코드는 아래와 동일!
// Type Vec3D = {
//   x: number;
//   y: number;
//   z: number;
// }
```

요런 짓도 가능함

```ts
type Vec4D = Record<keyof Vec3D | "t", number>;
// 위 코드는 아래와 동일!
// Type Vec4D = {
//   x: number;
//   y: number;
//   z: number;
//   t: number;
// }
```

tmi : Record의 정의는 다음과 같다.

```ts
type Record<K extends keyof any, T> = {
  [P in K]: T;
};
```

#### 매핑된 타입을 사용하기

매핑된 타입은 키마다 별도의 타입을 사용할 수 있어 훨씬 유연함!!!

```ts
type Vec3D = { [k in "x" | "y" | "z"]: number };
// Type Vec3D = {
//   x: number;
//   y: number;
//   z: number;
// }

type ABC = { [k in "a" | "b" | "c"]: k extends "b" ? string : number };
// type ABC = {
//   a: string;
//   b: number;
//   c: string;
// };
```

<hr>
오늘의 교훈 : 정 안되면 쓸 순 있겠지만, 가능하면 인터페이스, Record, 매핑된 타입 같은 인덱스 시그니처보다 정확한 타입을 쓰자.

tmi2 : 참고로 native에서 생성한 모듈을 react-native에서 사용하는 경우에도 인덱스 시그니처처럼 타입을 지정해 사용하는 부분에서 타입 캐스팅 해서 쓰는 용법이 있다.
