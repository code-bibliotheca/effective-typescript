# 아이템 33. string 타입보다 더 구체적인 타입 사용하기

string 타입은 모든 문자열의 집합이기 때문에 any처럼 매우 넓은 범위의 타입이다. 따라서 string으로 정의하려 할 때 해당 타입이 좀 더 좁은 타입에 맞는 값이 아닌지 생각해볼 필요가 있다.

```ts
interface Album {
    artist: string;
    title: string;
    releaseDate: string;    // YYYY-MM-DD
    recordingType: string;  // 'live' or 'studio'
}
```

위와 같이 string 타입을 남발해 타입을 정할 경우(stringly typed🤣) 다음과 같은 문제가 생길 수 있다.

> 1. releaseDate에 잘못된 형식의 날짜가 들어가도 타입체커가 알 수 없음
> 2. Album 값을 받아서 전달할 때 string으로 받는다면 releaseDate를 전달하든 recordingType으로 전달하든 타입 체크를 통과하기 때문에 불필요한 디버깅이 필요할 수 있음

따라서 string 타입의 경우 의미에 맞게 타입을 명시적으로 지정해줄 필요가 있다. artist와 title은 동일한 대상을 부르는 문자열이 서로 다를 수 있기 때문에 string으로 받는 것이 맞으나,
releaseDate의 경우 Date가, recordingType의 경우 주석으로 명시된 live, studio 타입으로 좁힐 수 있다.

```ts
type RecordingType = 'live' | 'studio';

interface Album {
    artist: string;
    title: string;
    releaseDate: Date;
    recordingType: RecordingType;
}
```

위와 같이 명시적인 타입을 지정해줄 수 있으며 이렇게 작성하는 경우 세 가지 장점이 있다고 한다.

### 1. 다른 곳에 전달할 때 타입 정보가 유지됨

다음과 같이 함수를 정의하면 호출하는 곳에서 임의의 문자열을 넣을 수 있는데, Album 타입의 recordingType에는 'live', 'studio'가 명시적으로 지정되어있기 때문에 내부에서만 해당 타입을 확인하고 걸러내는 과정을 작성할 수 있다.

```ts
function getAlbumsOfType(recordingType: string): Album[] { // ...
}
```

### 2. 타입의 의미를 설명하는 주석을 붙여 넣을 수 있음



### 3. keyof 연산자로 더욱 세밀하게 객체의 속성 체크가 가능

**어떤 배열에서 한 필드의 값만 추출하는 함수**를 작성하는 예시를 확인해보자.

```ts
function plunk(records: any[], key: string): any[] {
    return records.map(r => r[key]);
}
```

위의 anyScript는 다음과 같이 제너릭 타입을 도입해 records의 값 타입을 받아올 수 있다.

```ts
function plunk<T>(records: Array<T>, key: string): any[] {
    return records.map(r => r[key]);
}
```

위의 key는 여전히 string으로 넓은 타입을 갖는데, keyof 키워드를 활용해 이를 아래와 같이 좁힐 수 있다.

```ts
function plunk<T>(records: Array<T>, key: keyof T): Array<T[keyof T]> {
    return records.map(r => r[key]);
}
```

하지만 위와 같은 경우 T[keyof T] 타입은 T의 모든 key를 가질 수 있기 때문에 "한 필드의 값"만 추출하는 함수에는 여전히 넓은 범위의 값이 된다. 따라서 keyof T의 부분집합으로 두 번째 제너릭 매개변수를 다음과 같이 사용할 수 있다.

```ts
function plunk<T, K extends keyof T>(records: Array<T>, key: K): Array<T[K]> {
    return records.map(r => r[key]);
}
```

위의 함수를 통해 배열로부터 원하는 특정 필드의 값을 추출할 수 있고, 잘못된 속성 값이 들어온 경우 타입 에러를 통해 확인할 수 있다!!!

```ts
const albums: Album[] = [
    {artist: '지훈', title: '사막에서 꽃을 피우듯', releaseDate: new Date(2023, 7, 13), recordingType: 'studio'},
    {artist: '세영', title: 'Hype Boy', releaseDate: new Date(2022, 7, 1), recordingType: 'studio'},
    {artist: '유정', title: 'Baddie', releaseDate: new Date(2023, 9, 13), recordingType: 'studio'},
    {artist: '지현', title: '이름에게', releaseDate: new Date(2017, 3, 21), recordingType: 'live'},
]

plunk(albums, 'releaseDate');   // type Date[]
plunk(albums, 'artist');        // type string
plunk(albums, 'recordingType'); // type RecordingType[]
plunk(albums, 'recordingDate'); // type error!
```
