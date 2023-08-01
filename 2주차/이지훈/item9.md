## item 9: 타입단언보다는 타입 선언하기

1. 타입 선언

   ```typescript
   const alice: Person = { name: "Alice" };
   ```

   - 할당되는 값이 해당 인터페이스를 만족하는지 검사
   - 안정성 체크를 위해 타입 선언 사용을 권장
   - 화살표 함수를 사용할때 타입 선언 명시를 잘 해야됨
     > (name) : Person => name 의 타입은 없고, return 이 Person 타입<br>
     > (name : Person) => name 타입이 Person 이고, return 없음 => 오류

2. 타입 단언
   ```typescript
   const bob = { name: "bob" } as Person;
   ```
   - 강제로 타입을 지정 후 타입 체커에게 오류 무시하라고 전달
   - 단언문은 타입스크립트보다 타입 정보를 더 잘 알고 있는 상황에서 사용
     > 타입스크립트는 DOM 에 직접접근 x => ts 가 정보를 직접 모름 => 단언문 사용
   - null 아님 단언문을 사용
     > 변수의 접두사로 쓰인 ! 은 boolean의 부정문 / 접미사로 쓰인 !은 값이 null 이 아니라는 단언문
   - 단언문은 컴파일과정에 제거됨
   - 모든 타입은 unknown의 서브타입이기 때문에 unknown 이 포함된 단언문은 항상 동작(임의 타입을 변환시키기에 위험하니 사용권장 x)
