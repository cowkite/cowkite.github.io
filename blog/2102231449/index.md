---
layout: post
title: if only VS if else
subtitle: 내 코드를 방해하는 else를 찾아서 🐠
type: Develop
blog: true
text: true
header-img: "img/about.png"
post-header: true
date: 2021-02-22 21:36
comments: true
---



요즘 알고리즘 공부 차 매일 릿코드 문제를 랜덤으로 풀고 있는데, 문제를 풀던 중 흥미로운 점을 발견했다.

```java
// 1번 코드
if (condition) {
    var += A;
}
var += B;
```

```java
// 2번 코드
if (condition) {
    var += A + B;
} else {
    var += B;
}
```

1번 코드와 2번 코드는 룩은 달라도 동일한 로직을 수행하고 동일한 결과를 제공한다. `condition`이 `true`일 때는 `var` 변수에 `A`와 `B`가 추가되고, `false`일 때는 `var`변수에 `B`만 추가된다. 난 대게 [Return elary pattern](https://stackoverflow.com/questions/36707/should-a-function-have-only-one-return-statement), [Guard clauses refactoring](https://refactoring.guru/replace-nested-conditional-with-guard-clauses) 등의 패턴에 따라 1번 코드를 많이 봐왔고 또 1번 코드로 많이 짜왔지만, 사실 상 2번코드로 짠다고해서 큰 차이는 없다고 생각했기 때문에 코드리뷰 등의 과정에서 해당 패턴을 유심히 본 적은 없다.

릿코드의 [824. Goat Latin](https://leetcode.com/problems/goat-latin/) 문제를 A개발자와 각각 풀면서 둘이 거의 유사한 로직을 구현했는데, 코드의 time complexity가 같음에도 불구하고 희한하게 두 코드에서 6ms 내외의 run time 차이가 있었다. 한줄 한줄 분석하며 원인을 찾다보니 한명은 1번 코드로 짰고 한명은 2번 코드로 짰는데 그 부분에서 run time이 차이난다는 것을 발견하게 되었다.]

# else 구문의 비용?

참 희한한 일이다. `else if`도 아니고 단지 `else`일 뿐인데 이걸 사용하느냐 아니냐로 차이가 난다고?

처음에는 변수에 값이 assign 되는 시점의 차이인가하고 여러 테스트 케이스를 만들어 돌려봤는데, 변수 할당이나 공간 복잡도 등의 문제는 아닌 것 같다는 결론을 내렸다.

`else coast`, `early return time complexity` 등의 키워드로 검색을 했으나 대체로 코딩 스타일과 가독성에서 1번 코드로 짜는 것이 좋다는 글들 말고는 딱히 속도와 비용에 대해서 이야기하는 글을 찾지 못했다.

그러던 중 [If statement vs if-else statement, which is faster?](https://stackoverflow.com/questions/43202012/if-statement-vs-if-else-statement-which-is-faster)라는 은혜로운 글을 발견하게 되었다!

# if only VS if else

[accepted 된 답변](https://stackoverflow.com/a/43202221/4427613)에서는 if only 케이스와 if else 두 케이스를 비교해서 컴파일 결과를 비교한다.

* if only:

  ```cpp
   mov     DWORD PTR [rbp-8], 5
   cmp     BYTE PTR [rbp-4], 0
   je      .L2
   mov     DWORD PTR [rbp-8], 6
  .L2:
   mov     eax, DWORD PTR [rbp-8]
  ```

* if else:

  ```cpp
   cmp     BYTE PTR [rbp-4], 0
   je      .L5
   mov     DWORD PTR [rbp-8], 6
   jmp     .L6
  .L5:
   mov     DWORD PTR [rbp-8], 5
  .L6:
   mov     eax, DWORD PTR [rbp-8]
  ```

컴파일러의 최적화 옵션이 적용되어 있지 않은 경우에는 어쨌든 if only가 더 효율적이라고는 한다. 그러나 이는 굉장히 미미함을 강조하고 있다.

> The latter looks slightly less efficient because it has an extra jump but both have at least two and at most three assignments so unless you really need to squeeze every last drop of performance (hint: unless you are working on a space shuttle you don't, and even then you *probably* don't) the difference won't be noticeable.

# Return Early Pattern

호기심 때문에 if only VS if else를 비교하기는 했지만, 사실 요즘 컴파일러는 최적화 되어있을 뿐더러 유연하게 동작하기 때문에 차이라고 말하기엔 의미 없는 것으로 보인다. 그리고 속도/공간 복잡도를 이야기한다쳐도, 어느 정도의 의미있는 차이가 아니고서는 상호간의 이해와 유지보수가 편안한 코드를 작성하는 것이 중요하다고 생각한다.

추후 만약 누군가가 2번 코드로 작성한 것을 리뷰하게 되더라도 딱히 denied 하진 않겠지만, 가독성 및 유지보수 측면에서 1번 코드로 작성하도록 suggestion 정도는 할 것 같다. 1번 코드는 **Return Early Pattern**으로 통용되며, 이 패턴으로 작성했을 때의 장점은 하기 항목으로 정리할 수 있다.

* 들여쓰기의 깊이가 1레벨이기 때문에 선형적으로 코드를 읽을 수 있다.
* positive result를 함수 끝에서 빠르게 찾을 수 있다.
* 예외를 먼저 처리한 뒤 비즈니스 로직을 구현하기 때문에 불필요한 버그를 피할 수 있다.
* TDD 방식과 유사하므로 코드를 테스트하기가 더 용이하다.
* 오류가 발생되면 즉시 종료되므로 더 많은 코드가 실행될 가능성이 없다.

관련 내용은 관련 패턴인 [Else Considered Smelly](https://proxy.c2.com/cgi/fullSearch?search=ElseConsideredSmelly)와 [Arrow Anti Pattern](https://proxy.c2.com/cgi/fullSearch?search=ArrowAntiPattern)의 내용을 읽어보면 도움이 많이 된다. 또한 구글링 중에 발견한 [Return Early Pattern](https://medium.com/swlh/return-early-pattern-3d18a41bba8) 글에 정리가 잘 되어있어서 생각을 정리하는데 크게 도움이 되었다.
