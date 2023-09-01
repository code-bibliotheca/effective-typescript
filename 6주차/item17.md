## item17 : 변경 관련된 오류 방지를 위해 readonly 사용하기

### 데이터의 불변성(Immutability)

데이터의 불변성은 데이터가 한 번 생성된 후에 변경되지 않는 특성을 의미함. 불변성을 지키는 것은 데이터의 신뢰성과 안전성을 높이며, 유지보수와 가독성 좋은 코드를 작성할 수 있도록 함

- 데이터의 일관성 : 한 번 저장한 데이터는 변하지 않기 때문에 **일관성**을 유지할 수 있음
- 추적성과 신뢰성 : 데이터의 변경 내역을 추적하기 쉬워 데이터의 신뢰성이 높아짐
- 부수효과(side effect) 제거 : 데이터를 조작하는 경우 기존의 데이터가 유지된 채로 새로운 데이터를 생성하기 때문에 부수효과를 일으키지 않고, **예측 가능한 코드**를 작성할 수 있음

### readonly 타입의 매개변수 선언

TS에서는 readonly 키워드를 타입에 명시적으로 붙여 해당 변수에 담긴 **데이터를 직접 변경할 수 없도록** 설정 가능

- tmi : 삼각수란 삼각형을 만드는데 필요한 돌의 숫자를 말하며, 1, 3(1 + 2), 6(1 + 2 + 3), 10, ... 이런 식으로 증가

```ts
// before(mutable parameter)
function arraySum(arr: number[]) {
  let sum = 0,
    num;
  while ((num = arr.pop()) !== undefined) {
    sum += num;
  }

  return sum;
}
```

```ts
// after(immutable parameter)
function arraySum(arr: readonly number[]) {
  let sum = 0;
  for (const num of arr) {
    sum += num;
  }

  return sum;
}

function printTriangles(n: number) {
  const nums: number[] = [];
  for (let i = 0; i < n; i++) {
    nums.push(i);
    console.log(arraySum(nums));
  }
}
```

readonly 타입을 반환하는 함수를 호출하는 경우 다른 함수도 readonly로 선언해야 하는 경우가 생길 수 있는데, 이는 인터페이스를 명확히 하고, 지역 변수와 관련된 모든 종류의 변경 오류를 방지할 수 있어 타입 안전성을 높일 수 있음

```ts
function getItems(): readonly string[] {
  return ["apple", "banana"];
}

function append(item: string): readonly string[] {
  const items = getItems();
  // items.push(item) ~~ Property 'push' does not exist on type 'readonly string[]'
  return [...items, item];
}

const items = append("peach");
items; // ['apple', 'banana', 'peach']
```

```ts
const str = `Frankenstein; or, The Modern Prometheus by Mary Shelley

You will rejoice to hear that no disaster has accompanied the commencement of an enterprise which you have regarded with such evil forebodings. I arrived here yesterday, and my first task is to assure my dear sister of my welfare and increasing confidence in the success of my undertaking.

I am already far north of London, and as I walk in the streets of Petersburgh, I feel a cold northern breeze play upon my cheeks, which braces my nerves and fills me with delight.`;

function parseTaggedText(lines: string[]): string[][] {
  const paragraphs: string[][] = [];
  const currPara: string[] = [];

  const addParagraph = () => {
    if (currPara.length) {
      paragraphs.push(currPara);
      currPara.length = 0; // 여기서 동일한 currPara를 참조하며 비워버려서 문제!
    }
  };

  for (const line of lines) {
    if (!line) {
      addParagraph();
    } else {
      currPara.push(line);
    }
  }
  addParagraph();
  return paragraphs;
}

const lines = str.split("\n");
lines;
const taggedText = parseTaggedText(lines);
console.log(taggedText); // [ [], [], [] ]
```

이를 해결하기 위해 readonly 타입을 사용하는 것이 한 가지 방법이 될 수 있음

```ts
const currPara: readonly string[] = [];
```

currPara를 readonly로 두면 length = 0으로 설정하는 방식의 코드를 ts단에서 잡을 수 있기 때문

### readonly는 얕게 동작

readonly는 1-depth의 프로퍼티에 대해서만 작동하기 때문에 그 이상의 깊이의 데이터는 변경이 가능해짐

```ts
const dates: readonly Date[] = [new Date()];
// dates.push(new Date()); // ~~ 배열이 readonly기 때문에 내부 객체들은 readonly가 아님
dates[0].setFullYear(2037);
```

Readonly 제너릭은 객체에 사용할 수 있는 타입으로, 해당 객체가 readonly 속성을 띄도록 하지만 이 또한 얕게 동작하기 때문에 프로퍼티로 갖는 객체들에 대해선 readonly가 작동하지 않음

```ts
interface Outer {
  inner: {
    x: number;
  };
}

const o: Readonly<Outer> = { inner: { x: 0 } };
o.inner = { x: 1 };
o.inner.x = 1; // 정상!
```

깊은 readonly를 사용하려 한다면 [ts-essentials](https://github.com/ts-essentials/ts-essentials)의 DeepReadonly제너릭을 사용할 것!
