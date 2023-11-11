# 아이템 36. 해당 분야의 용어로 타입 이름 짓기

> "There are only two hard things in Computer Science: cache invalidation and naming things." - Phil Karlton

(넷스케이프 개발자)

변수, 함수, 타입의 이름 붙이기는 언제나 어렵다. 예를 들어 동물의 데이터베이스를 구축하기 위해 타입을 지정한다고 가정해보자.

```ts
interface Animal {
    name: string;
    endangered: boolean;
    habitat: string;
}

const leopard: Animal = {
    name: 'Snow Leopard',
    endangered: false,
    habitat: 'tundra',
}
```

이 코드는 나름대로 프로퍼티 명을 잘 지었다고 생각했으나 다음 4가지 문제가 있다고 한다.

1. name은 너무 일반적인 용어이므로 동물의 학명인지 일반적인 명칭인지 알 수 없음
2. endangered 속성이 멸종된/멸종 위기의/멸종 위기가 아닌을 표현하기에 부족함
3. habitat 속성은 string 타입으로 너무 넓은 범위의 불분명한 타입을 가짐
4. 객체의 이름과 속성값인 name이 서로 다른 값을 가져 각각이 어떤 의도로 쓰일 것인지 의도가 불분명함

이렇게 작성한 경우 해당 코드를 작성한 사람이 아니라면 객체가 사용된 코드를 보며 개발자가 번거롭게 로직을 보며 다시 이해해야 하는 상황이 생기게 될 것이다.
따라서 다음 타입처럼 의미가 분명하게 보이도록 타입을 지정해줘야 한다.

```ts
interface Animal{
    commonName: string;
    genus: string;
    species: string;
    status: ConservationStatus;
    climates: KoppenClimate[];
}

type ConservationStatus = 'EX' | 'EW' | ... | 'LC'; // 동물 보호 등급에 대한 표준 분류 체계
type KoppenClimate = 'Af' | 'Am' | ... | 'ET'; // 쾨펜 기후 분류를 사용해 기후 표기
```

위의 타입은 일반적인 용어인 name을 commonName, genus, species로 상세히 니눴고, status와 climates를 표준 체계를 활용해 명시함으로써 타입만 봐도(인터넷 검색 찬스를 쓰긴 하겠지만)
어떤 값인지, 어떻게 쓰일지에 대해 명확히 알 수 있도록 작성됐다.

이렇듯 타입 명은 전문 분야가 있다면 해당 용어를 정확히 사용하고, 그렇지 않다면 적절한 추상화된 이름을 통해 타입만 봐도 이해할 수 있는 코드를 작성하는 것이 중요하다.
