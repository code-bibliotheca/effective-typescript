# 아이템 43. 몽키 패치보다는 안전한 타입을 사용하기

> ***몽키패치*** : 
> 
> 이슈 링크 : https://github.com/code-bibliotheca/effective-typescript/issues/11

자바스크립트는 매우 유연한 언어로 객체(일반 객체부터 DOM까지)나 프로토타입 등에 원하는 속성을 추가할 수 있는 특징이 있으나, 
이 방법으로 코드를 설계하는 것은 좋지 않음

-> 프로그램 내에서 서로 멀리 떨어진 코드 간의 의존성을 만들게 되기 때문!!(side effect)

여기에 타입스크립트까지 더해지면 타입을 체크하지 못해 문제가 발생(any로 타입 체커를 무효화 시킬 수 있으나 이러면 안돼!)

## interface의 augmentation(보강) 활용

```ts
document.monkey = 'Tamarin'; // ~ 'Document' 유형에 'monkey' 속성이 없습니다.
```

예를 들어 위와 같이 document에 monkey라는 속성을 추가하면, Document 타입에 monkey가 없다는 타입스크립트 에러를 볼 수 있다.
이를 interface를 활용해 다음과 같이 수정 가능하다.

```ts
interface Document {
    monkey: string;
}

document.monkey = 'Tamarin';
```

혹시라도 해당 타입이 변하지 않으면 ts 설정이 순정이 아니라 그럴 수 있으니 [playground](https://www.typescriptlang.org/play?#code/JYOwLgpgTgZghgYwgAgCIHsEFcC2FzIDeAUMmcjuiANYQCeAXMgM5hSgDmA3MQL7HEAJplz4wAOko16yALzIA5ABU4OOOxAKuQA)에서 확인해보길 바란다.

타입 보강을 사용한 경우 다음 장점들이 있다.

- any보다 안전한 타입 사용 가능
- 속성에 주석, 자동완성 등의 ts 기능 사용 가능
- 명시적 몽키패치 구현

반면, *Document 타입이 전역으로 바뀌기 때문에 모듈 단위의 타입 구별이 되지 않는 단점*이 있다.

## 더 구체적인 타입 단언문 사용하기

```ts
interface MonkeyDocument extends Document {
    monkey: string;
}

(document as MonkeyDocument).monkey = 'Macaque';
```

이렇게 사용할 경우,

- 안전한 타입 사용 가능
- Document 타입은 그대로 유지되기 때문에 모듈 영역 문제도 해결

> 위의 두 가지 방식으로 몽키 패치를 이용할 수 있으나 패치를 남용하지는 말자! 
