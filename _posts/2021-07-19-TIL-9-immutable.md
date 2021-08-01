---
title:  "TIL 9: 함수형 JS를 위한 Immutable"
last_modified_at: 2021-07-19:T14:25:52-05:00
categories:
  - immutable
  - functional programming
  - 함수형 프로그래밍
tags:
  - 자바스크립트  
  - 함수형
  - Immutable.js
---

# Immutable이란?

Imutable은 불변성을 의미한다. 즉, 한번 생성하면 변경할 수 없다는 의미이다.

함수형 프로그래밍에서 순수함수는 입력값에 대한 출력값이 매번 같으며, 출력값을 계산하는 것 외에 다른 작업을 하지 않는 함수를 의미한다. 이러한 작업들을 부수 효과(Side effect)라고 한다. 어떤 변수의 값을 변경하는 작업도 부수 효과라고 볼 수 있다. 이러한 부수 효과를 지양하는 함수형 프로그래밍에서 불변성을 지니는 데이터 구조를 사용하여 부수 효과를 최대한 피하고자 한다.

# Immutable 데이터 구조란?

- 한번 생성하면 변경할 수 없는 데이터 구조를 의미한다.
- 일반적으로 Immutable한 데이터 구조를 사용한다면, 데이터를 변경(데이터 추가, 삭제, 업데이트)를 하고자 할때는 해당 데이터를 전부 복사하여, 변경될 데이터만 바꾸는 방식으로 한다.
    - 예를 들어, 배열에서 첫번째 값을 바꾸고자 한다면 해당 배열을 전부 새롭게 복사한 후, 첫 번째 값만 수정하여 반환한다.
- 이는 시간적으로도, 공간적으로도 비효율적이다.
- Immutable 데이터 구조는 이러한 작업들을 빠르게 하기 위한 자료구조라 할 수 있다.
- 데이터를 트리로 구현하며, 데이터 변경시 필요한 부분만 변경하여 변경되지 않은 부분을 재사용하는 방식으로 구현한다.
    - 예를 들어 배열[1, 2, 3, 4, 5, 6, 7, 8]을 트리로 표현한다면 다음과 같다
    - 

        ![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/5ae58161-916a-43d1-b6f7-908a1dc648ba/KakaoTalk_Photo_2021-07-05-17-44-25.jpeg](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/5ae58161-916a-43d1-b6f7-908a1dc648ba/KakaoTalk_Photo_2021-07-05-17-44-25.jpeg)

    - 그 후, 2를 9로 변경한다면 다음과 같이 변경된다.

        ![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/5aa83bfe-2748-4780-83fe-a54f2f41bd0a/KakaoTalk_Photo_2021-07-05-17-44-28.jpeg](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/5aa83bfe-2748-4780-83fe-a54f2f41bd0a/KakaoTalk_Photo_2021-07-05-17-44-28.jpeg)

    - 이를 path copying이라고 한다
    - 배열 전체를 복사할 필요가 없어진다.
    - 복잡도를 linear에서 logrithmic하게 변경되었다
- 이러한 데이터 구조를 TRIE라고 부른다.
    - from retrieval
    - `트라이` 라고 부른다

# Immutable.js

Immutable.js는 Immutable 데이터 구조를 제공하는 라이브러리이다. 

List, Stack, Map, OrderedMap, Set, OrderedSet 그리고 Record를 제공한다.

이러한 데이터 구조들은 hash maps tries와 vector tries를 사용하는 모던 자바스크립트 VM에서 매우 효율적이다. 이러한 tries들은 데이터를 복사 혹은 캐싱하는 것을 최소화해준다.

Immutable.js는 lazy `Seq`을 제공한다. 이는 체이닝 메서드를 사용할 때 중간 결과값을 실제로 생성하지 않도록 하여 효율적으로 체이닝할 수 있도록 도와준다.

Immutable.js는 기존 자바스크리트의 Array, Stack, Map, Set 등을 대체할 수 있는 새로운 객체들을 제공한다.

이들은 추가, 수정 삭제시마다 매번 새로운 객체를 반환하는데, 이를 Trie 구조로 구현하여 매우 효율적이다. 

또한 이 데이터 구조들은 자바스크립트 데이터 구조로부터/ 데이터 구조로의 변환이 용이하다.

### Immutable.js의 장점

- 불변 객체를 쉽게 생성하고 관리할 수 있게 도와준다.
- Trie 자료구조를 통해 객체 생성이 효율적으로 이루어진다.
- 자바스크립트 기존 객체로 쉽게 변환이 가능하다.
- 자바스크립트의 기존 메소드들을 대부분 지원한다.