# 아이템 37. 공식 명칭(Nominal Typing)에는 상표를 붙이기

> 값에 _brand 처럼 상표를 붙여 해당 값이 특정 타입임을 명시할 수 있다!!!

### Vec2D

다음과 같이 상표를 붙여 2차원 벡터를 다루는 함수를 작성할 수 있다.

```ts
interface Vector2D {
    _brand: '2d';
    x: number;
    y: number;
}

function vec2D(x: number, y: number): Vector2D {
    return {x, y, _brand: '2d'};
}

function calculateNorm(p: Vector2D) {
    return Math.sqrt(p.x * p.x + p.y * p.y);
}

calculateNorm(vec2D(3, 4));
const vec3D = {x: 3, y: 4, z: 1};
calculateNorm(vec3D); // ~ '_brand' 속성이 ... 형식에 없습니다.
```

이 경우 vec3D를 정의할 때 _brand를 넣어 사용할수도 있겠지만, 어느정도는 2d vector만 걸러낼 수 있는 기능으로 사용할 수 있다.

### 타입 가드로 사용하기

string 타입이면서 _brand 속성을 가지는 객체는 만들 수 없으나, 아래와 같이 타입을 지정함으로써 path를 문자열로 다룸과 동시에 절대경로인지를 파악할 수 있다!

```ts
type AbsolutePath = string & {_brand: 'abs'};
function isAbsolutePath(path: string) : path is AbsolutePath {
    return path.startsWith('/');
}
```

또 다른 예로 다음과 같이 배열이 정렬되어있는지 체크하는 타입가드를 작성할 수도 있다.

```ts
type SortedList<T> = T[] & {_brand: 'sorted'};
function isSorted<T>(xs: T[]) : xs is SortedList<T> {
    for (let i = 1; i < xs.length; i++) {
        if (xs[i] < xs[i - 1]) {
            return false;
        }
    }
    return true;
}
```

number 타입에도 다음과 같이 상표를 붙일 수 있으며 명시적인 숫자 표현을 할 수 있다. 아래 console.log의 결과와 같이 런타임엔 단순 number로 동작함을 확인할 수 있다.

```ts
type Meters = number & {_brand: 'meters'};
type Seconds = number & {_brand: 'seconds'};

const meters = (m: number) => m as Meters;
const seconds = (s: number) => s as Seconds;

const oneKm = meters(1000);
const oneMin = seconds(60);

console.log(oneKm); // 1000
console.log(typeof oneMin); // number
```

타입 시스템의 syntax sugar...!!
