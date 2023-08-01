## item:10 : 객체 래퍼 타입 피하기

기본형 : string, number, boolean, null, undefined, symbol, bigint (불변이며 메서드를 가지지 않는 것이 객체와 차이점)

기본형의 객체타입: Number, Boolean, Symbol, BigInt (null, undefined 은 객체래퍼없음)

기본형의 string 은 메서드를 가지지 않지만, `'primitive'.chartAt(3)` 같은 경우 String 객체 타입으로 정의된다<br>
자바스크립트에서 기본형과 객체타입을 서로 자유롭게 변환

> ### 변환순서
>
> string 기본형에 메서드를 사용하면, js 는 기본형을 객체로 래핑<br>
> 메서드를 호출<br>
> 래핑한 객체를 버림<br>

### [몽키패치](https://github.com/code-bibliotheca/effective-typescript/issues/11)

### String 객체는 오직 자기 자신만이랑 일치

```typescript
console.log("hello" === new String("hello")); //false
console.log("hello" === "hello"); //true
console.log(new String("hello") === new String("hello")); //false
console.log(new String("hello") == new String("hello")); //false
```

### 기본형은 객체래퍼타입에 할당가능, 객체래퍼타입은 기본형에 할당 불가능한 이유

- 기본형은 원시 값(primitive value)이며, 값 자체가 불변적 => 값을 변경하거나 메서드를 호출할 수 없음, 객체래퍼타입은 객체이므로 값이 변경될 수 있고 메서드를 호출할 수 있다
- 기본형은 스택(stack) 메모리에 저장되는 반면, 객체는 힙(heap) 메모리에 저장. 할당 방식과 메모리 관리 방식이 다름
- 기본형과 객체래퍼타입은 서로 다른 타입으로 간주,정확히는 타입 호환성이 없어서 기본형 값을 객체래퍼타입에 할당할 수 없음 => 하지만 타입박싱이라는 변환가능을 통해 기본형값이 객체래퍼타입으로 감싸지는 것

### 런타입의 값은 객체가 아니고 기본형이다?

- 타입스크립트는 컴파일타임에 타입정보확인, 런타입에 해당 타입에 따라 값을 다룸
- 기본형은 값 자체를 런타임에 메모리에 직접 저장
- 객체는 참조를 다루므로 메모리에 해당 객체가 저장되어있는 주소를 가리킴

=> 런타임에서는 기본형은 값을 직접 다루고, 객체는 참조를 다룬다는 말을 의미
