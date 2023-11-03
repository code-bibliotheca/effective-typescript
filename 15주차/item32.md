# 아이템 32. 유니온의 인터페이스보다는 인터페이스의 유니온을 사용하기

```ts
interface Layer {
    layout: FillLayout | LineLayout | PointLayout;
    paint: FillPaint | LinePaint | PointPaint;
}
```

- 선 도구를 선택했는데 채우기 방식으로 색을 칠하는 것은 조금 어색함이 있음.
- 위와 같이 타입을 정한 경우 만들었을 때 어색한 조합이 생길 수 있기 때문에, 함께 사용하는 타입이 있다면 해당 타입끼리 묶어서 하나의 인터페이스로 만들어주는 것이 좋음

```ts
interface FillLayer {
    layout: FillLayout;
    paint: FillPaint;
}
interface LineLayer {
    layout: LineLayout;
    paint: LinePaint;
}
// ...
type Layer = FillLayer | LineLayer | ...
```

위의 경우 사용하는 쪽에서 레이어의 타입을 구별하기 위해(타입 좁히기) 태그를 다음과 같이 추가할 수 있다.

```ts
interface FillLayer {
    type: 'fill';
    layout: FillLayout;
    paint: FillPaint;
}
// ...
```

태그를 추가한 경우 아래와 같이 태그를 통해 Layer 타입의 범위를 좁히는 것도 할 수 있다.

```ts
function drawLayer(layer: Layer) {
    
    if (layer.type === 'fill') {
        const {layout, paint} = layer;
        // layout : FillLayout, paint: FillPaint        
    } else if (layer.type === 'line') {
        const [layout, paint] = layer;
        // layout : LineLayout, paint: LinePaint
    }
}
```


## 태그된 유니온 사용하기

> 어떤 데이터를 태그된 유니온으로 표현할 수 있다면 보통은 그렇게 하는 것이 좋다.

또는 여러 개의 선택적 값이 동시에 있어야 하거나 동시에 undefined인 경우에도 태그된 유니온 패턴을 쓸 수 있다.

```ts
interface Person {
    name: string;
    placeOfBirth?: string;
    dateOfBirth?: Date;
}
```

위와 같은 타입을 지정하는 경우 placeOfBirth와 dateOfBirth가 생일에 대한 동일 속성이며 값이 함께 있거나 없게 되는데, 이런 경우 다음과 같이 작성하는 것이 더 좋다.

```ts
interface Person {
    name: string;
    birth?: {
        place: string;
        date: Date;
    }
}
```

두 번째와 같이 작성하는 경우 사용하는 쪽에서 birth의 존재 유무만 체크하면 되기 때문에 더욱 편리하게 사용할 수 있다. 반면, API를 통해서 온 값이 첫 번째와 같이 들어와 타입의 구조를 손 댈 수 없는 상황이라면 인터페이스의 유니온을 사용해 속성 사이의 관계를 클라이언트가 모델링 할 수 있다.

```ts
interface Name {
    name: string;
}

interface PersonWithBirth extends Name {
    placeOfBirth: string;
    dateOfBirth: Date;
}

type Person = Name | PersonWithBirth;
```

아래는 서로 다른 타입의 데이터가 들어왔을 때 정상 작동하는 것을 확인할 수 있는 예시 코드이다.

```ts
// fetched data
const data1 = {
    name: "유정",
    dateOfBirth: new Date(new Date(1993, 10 - 1, 26)),
    placeOfBirth: "안동"
}

const data2 = {
    name: '세영',
}

function sayHello(person: Person) {
    const {name} = person;

    if ('placeOfBirth' in person) {
        const {dateOfBirth} = person;
        if (dateOfBirth.getDate() === new Date().getDate()) console.log(`Hello ${name}. Happy birthday~🎉`);
        else console.log(`Hello ${name}`);
    }
    else {
        console.log(`Hello ${name}`);
    }
}

sayHello(data1);
sayHello(data2);
```

왜 매번 예시가 구린지 모르겠는데 eulogize는 고인이 되신 분을 기리기 위한 찬사나 칭찬의 말을 공개적으로 하는 것으로, "찬양하다, 칭송하다"의 뜻으로 해석하면 된다고 한다.
