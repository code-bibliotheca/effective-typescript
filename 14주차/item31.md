# 아이템 31. 타입 주변에 null 값 배치하기

## tl;dr : 어떤 객체의 프로퍼티가 null일 수 있다면, 해당 객체의 return 값 자체를 null | 특정 타입 으로 지정하자!

데이터를 다루다보면 여러 값을 동시에 다뤄야 하는 경우가 많은데, 이 때 각각의 타입을 nullish 하게 작성하면(특정 타입 | undefined or 특정 타입 | null) 각각의 값을 가져다 사용할 때 분기가 많아져 이후 코드를 작성하기 어려워진다. 따라서 한꺼면에 null로 처리하는 방식을 사용하는 것이 좋다!

```ts
function extent(nums: number[]) {
  let min, max;
  for (const num of nums) {
    if (!min) {
      min = num;
      max = num;
    } else {
      min = Math.min(num, min);
      max = Math.max(num, max);
    }
  }

  return [min, max];
}
```

위의 함수는 [0, 1, 2]를 인수로 받았을 때 0에 대한 체크가 if문에 대해 일어나게 되며 min값을 0이 아닌 1로 설정해버리는 문제가 있으며(**해당 함수의 논리적 오류**), 입력받은 배열이 비어있을 때(**사용자의 오류**)도 동작하여 정확한 min, max 값을 반환하지 못하는 문제가 있다. 이를 해결하는 방법으로 두 가지 정도가 있을 수 있다.

> 1. strictNullChecks 설정 켜기
> 2. min, max를 한 객체 안에 넣고 해당 객체를 null이거나 null이 아니도록 작성하기

첫 번째 방법은 오류의 형태로 나타나기 때문에 만약 이미 해당 함수를 다른 곳에서 많이 사용하고 있다면 코드 수정에 많은 시간을 들일 수밖에 없어 두 번째 방법으로 **_정확한 min/max를 반환하는 함수로 만드는_** 방식이 좀 더 나은 방법이다.

```ts
function extent(nums: number[]) {
  let result: [number, number] | null = null;
  for (const num of nums) {
    if (!result) {
      result = [num, num];
    } else {
      result = [Math.min(num, result[0]), Math.max(num, result[1])];
    }
  }

  return result;
}
```

애초에 왜 이따구로 작성했는지 잘 모르겠지만, 해당 코드에서는 **의도적으로** null을 반환할 수 있기 때문에 사용하는 입장에서 아래와 같이 null 아님 단언을 사용해 min, max 값을 얻어낼 수 있게 된다.

```ts
const [min, max] = extent([0, 1, 2])!; // !를 사용하지 않으면 return이 null 일 수 있어 min, max의 타입이 null 가능 타입이 되버림
```

또 다른 예시를 보자.

```ts
class UserPosts {
  user: UserInfo | null;
  posts: Posts | null;

  constructor() {
    this.user = null;
    this.posts = null;
  }

  async init(userId: string) {
    return Promise.all([
      async () => (this.user = await fetchUser(userId)), // ????
      async () => (this.posts = await fetchPostsForUser(userId)),
    ]);
  }

  getUserName() {
    return this.user?.name || ""; // ??????
  }
}
```

위의 코드는 userPosts가 init되기 전에 자신의 값을 null로 갖고 있기 때문에 다른 메서드에서 매번 null 체크를 해줘야 하며, 이로 인해 불필요한 타입 체크와 예상치 못한 오류가 발생할 수 있다. 따라서 아래와 같이 값을 가진 채로 다른 메서드를 작성하는 것이 더 좋은 방식이다.

```ts
class UserPosts {
  user: UserInfo;
  posts: Posts;

  constructor(user: UserInfo, posts: Posts) {
    this.user = user;
    this.posts = posts;
  }

  async init(userId: string) {
    const [user, posts] = await Promise.all([
      fetchUser(userId),
      fetchPostsForUser(userId),
    ]);
    return new UserPosts(user, posts); // ??? 이건 왜 또 return하는지 모르겠지만...
  }

  getUserName() {
    return this.user.name;
  }
}
```
