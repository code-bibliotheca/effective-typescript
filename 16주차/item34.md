# 아이템 34. 부정확한 타입보다는 미완성 타입을 사용하기

타입을 명확하게 하겠다고 너무 좁은 타입을 선언하면(타입의 정밀도를 높이면) 문제가 생길수도 있다.(아니 타입 좁히라고 이전 단원에서 얘기해놓고 왜 말을 이렇게...)

> 좀 더 자세히 얘기하면, 타입을 항상 좁고 명확하게만 정하기보단 생길 수 있는 상황을 모두 포괄할 수 있는 **정확한** 타입을 지정해줘야 한다!!

타입을 명확히 하는 과정의 한 예시를 살펴보자. 책의 예시는 이해하기에 난해하여 임의로 의역한(?) 예시를 가져왔다. 예를 들어 아래와 같이 User 타입을 정의했다고 가정해보자.
이 때 간단한 서비스라면 유저의 정보로 id, phoneNumber 외에 나이나 이메일 등은 부가적인 정보일 수 있다. 그런데도 타입을 number, string 등으로 명시해놓으면 의도에 맞지 않을 뿐더러 
매번 값이 있어야 하기 때문에 코드를 작성하는데 어려움이 있을 수 있다. 따라서 그 아래 와 같이 옵셔널로 바꿈으로써 더 정확한 타입을 지정해줄 수 있다.

```ts
type User = {
  id: number;
  name: string;
  age: number;
  email: string;
  phoneNumber: string;
};

function getUser(id: number): User {
    // ...
}
```

```ts
type User = {
  id: number;
  phoneNumber: string;
  name?: string;
  age?: number;
  email?: string;
};
```

또 다른 예시를 들어보자. 아래와 같이 이벤트에 MouseEvent라는 타입을 지정하면 handlePointerDown 함수에서 개발자는 MouseEvent에 대한 값만 자동완성 혹은 유효한 타입으로
사용할 수 있게 된다. 하지만 웹 앱을 만든다고 가정할 때 해당 앱이 pc뿐만 아니라 패드나 모바일과 같은 다른 디바이스에서도 사용 가능할텐데 해당 디바이스에서의 이벤트 코드를 작성하기 힘들어진다.
따라서 MouseEvent 대신 그 아래 코드와 같이 상위의 UIEvent나 최상위의 Event 타입으로 좀 더 추상적으로 지정해줌으로써 실제 사용을 용이하게 할 수 있다. 

```ts
function handlePointerDown(event: MouseEvent): void {
  console.log(event.clientX, event.clientY, event.touches); // ~ 'MouseEvent' 형식에 'touches' 속성이 없습니다.
}
```

> 즉, 무조건 좁은 타입을 지정한다고 좋은 것은 아니다!
