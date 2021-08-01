---
title:  "TIL 8: Functional Programming"
last_modified_at: 2021-07-17:T14:25:52-05:00
categories:
  - functional programming
  - 함수형 프로그래밍
tags:
  - 자바스크립트
  - 함수형
---
__함수형 자바스크립트 입문 2/e 기반으로 학습한 내용을 작성한 글입니다.__

# 함수형 프로그래밍

## 함수형 프로그래밍이란?

- 함수형 프로그래밍 기술은 수학에서의 함수와 그 아이디어에서 왔다.
    - 수학적 의미에서의 함수:
        - 인자를 가져야 한다
        - 값을 반환한다
        - 외부가 아닌 자체 인자를 받아서만 동작한다
        - 주어진 X 하나에 Y는 오직 하나다.
- 참조 투명성: 동일한 입력에 대한 동일한 값은 반환
    - 함수를 결과값으로 치환 가능하다.
    - 병렬 코드와 캐시를 가능케 한다.
    - 선언형, 추상화 함수
        - 함수형 프로그래밍은 선언가능하고 추상화된 코드 작성에 관한 것이다.
            - 명령형이란 컴파일러에게 특정작업을 어떻게 해야 하는지 알려주는 것
            - 선언형은 컴파일러가 어떻게 작업해야 하는지보다 어떤 것이 필요한지가 중요하다.

## 함수형 프로그래밍의 장점

- 순수함수란?
    - 주어진 입력에 대해 동일한 출력을 반환하는 함수
    - 참조 투명성을 만족한다
    - 테스트하기 편한 코드이다.
    - 코드상 부수효과가 없기 때문에 읽고 이해하기 쉽다.
    - 병렬로 코드를 실행할 수 있다
        - 환경을 전혀 변동시키지 않으므로 동기화를 걱저할 필요가 없다.
    - 캐시 가능하다.
    - 파이프라인, 컴포저블하다

### 자바스크립트는 함수형 프로그래밍 언어인가?

자바스크립트는 멀티 패러다임 언어이다. 하지만 함수형 프로그래밍 기술과 장점이 적용될 수 있기 때문에 함수형 패러다임에 매우 적합하다고 할 수 있다. 

자바스크립트는 함수를 인자로 취하고 다른 함수에 전달하는 등 일급 객체로 함수를 다루기 때문에 함수형 패러다임에서 많은 장점을 얻을 수 있다.

# 고차함수

- 고차함수란
    - 인자로 다른 함수를 전달받는 함수
    - 고차 함수는 일반적인 문제를 추출하고자 작성된다. 즉, 추상화를 정의한다.

    ```jsx
    const forEach = (array, fn) => {
    	for(let i=0; i<array.length; i++)
    		fn(array[i])
    }
    ```

    배열을 순회하는 문제를 추상화했다. forEach API 사용자는 forEach 함수에서 순회 부분이 어떻게 구현되었는지 알 필요가 없다. 

## 현실에서의 고차 함수

```jsx
const every = (arr, fn) => {
	let result = true;
	for (const value of arr){
			result = result && fn(value)
	}
	return result;
}

const some = (arr, fn) => {
	let result = false;
	for (const value of arr){
			result = result || fn(value)
	}
	return result;
}
```

# 커링

- 단항함수
    - 함수 인자를 하나만 취하는 함수
- 이항함수
    - 두개의 인자를 취하는 함수
- 가변 인자 함수
    - 다양한 개수의 인자를 취하는 함수.
    - 이전의 자바스크립트 버전에서 `arguments`를 사용해 인자의 가변 개수를 알 수 있었다.

    ```jsx
    function variadic(a) {
    	console.log(a);
    	console.log(arguments)
    }

    // variadic(1,2,3)
    // => 1
    // => [1,2,3]
    ```

    - ES6 버전에서부터 전개 연산자(spread operator)라는 연산자를 사용하여 동일한 결과를 얻을 수 있다.

    ```jsx
    const variadic = (a, ...variadic) => {
    	console.log(a)
    	console.log(variadic)
    }

    // variadic(1,2,3)
    // => 1
    // => [2,3]
    ```

## 커링

커링은 n개의 인자의 함수를 중첩된 단항 함수로 변화시키는 과정이다.

```jsx
const curry = (fn) => {
    if (typeof fn!== 'function') {
        throw Error('No function provided')
    }
    return function curriedFn(...args) {
        // args를 통해 전달된 인자 길이가 함수 인자 리스트보다 작다면 if 블록 진입
        if (args.length < fn.length) {
            return function() {
                // 
                return curriedFn.apply(null, args.concat([].slice.call(arguments)));
            }
        }
        return fn.apply(null, args);
    }
}

const multiply = (x,y,z) => x * y * z;

curry(multiply)(1)(2)(3)
// => 6
curry(multply)(1)(2)(3)
// => 0
```

## 부분 적용

- 커링은 가장 왼쪽에서 가장 오른쪽의 리스트로 인자를 적용하기 때문에 함수의 인자들을 왼쪽부터 채워서 넘겨줘야 한다.
    - 즉, 왼쪽에 현재 값을 모르는 인자가 존재한다면, 해당 함수는 커링할 수 없다.
- 부분 적용은 이러한 커링의 한계를 해결해주는 방식이다.

```jsx
const partial = function(fn, ...partialArgs) {
    let args = partialArgs;
    return function(...fullArguments) {
        let arg = 0;
        for (let i =0; i<args.length && arg < fullArguments.length; i++) {
            if (args[i] === undefined) {
                args[i] = fullArguments[arg++];
            }
        }
        return fn.apply(null, args);
    }
}

let delayTenMs = partial(setTimeout, undefined, 10);

delayTenMs(() => console.log('Hello');
```

- 현재 알고 있는 인자들을 먼저 넘겨주고 모르는 인자는 undefined를 넘긴다.
- 나중에 인자들을 넘겨서 undefined를 대체한다.
- 왜 커링을 사용할까?
    - 재사용성
        - 커링은 함수를 인자로 받고, 함수를 반환하는 함수이다.
            - 유사한 함수들이 필요할 때 모든 함수들을 각자 구현하는 것이 아니라 동일한 부분을 추상화할 수 있다.
        - `부분 적용`과 함께 사용하여, 인자들 중 현재 가지고 있는 값들을 먼저 넘겨줘서 함수를 생성하는 방식으로 사용할 수 있다.
            - 현재 알고 있는 값들을 먼저 넘겨주어서 함수를 만들어 놓고, 추후에 나머지 값들을 알게되면 그 값들을 인자로 넘겨 함수를 실행시킨다.
            - 이는 반복을 줄여준다.

## 컴포지션

- 유닉스 철학
    - "각 프로그램이 한 가지 작업을 잘하게 하라. 새로운 작업을 하려면 새로운 '기능'을 추가해 오래된 프로그램을 복잡하게 하지 말고 새로 만들어라."
    - "모든 프로그램의 출력이 아직 알려지지 않은 다른 프로그램의 입력이 될 것으로 예상한다."
    - 이는 함수에서 함수를 구성하는 아이디어와 유사하다.
- 함수형 컴포지션

    ```jsx
    const compose = (a,b) => (c) => a(b(c))
    ```

    - `compose` 함수는 두개의 함수를 하나의 함수로 합성하는 함수이다.
    - 두 함수 a, b를 받아 하나의 인자 c를 갖는 함수를 반환한다. c에 값을 넣어 compose 함수를 호출하면 c의 입력을 갖는 함수 b를 호출하고 함수 b의 출력은 함수 a의 입력으로 간다.
        - 두 함수 a,b, 모두 하나의 인자만 받는다
- n개의 인자를 받는 함수를 합성하기 위해서는 커링 또는 부분적용의 도움을 받아서 할 수 있다.
- n개 이상의 함수를 함수를 합성하는 composeN 함수

    ```jsx
    const composeN = (...fns) => 
    	(value) =>	reduce(fns.reverse(), (acc,fn) => fn(acc), value)
    ```

### 파이프라인

- 컴포지션과 동일하지만, 데이터 플로우가 왼쪽에서 오른쪽

```jsx
const pipe = (...fns) => 
	(value) => reduce(fns, (acc, fn) => fn(acc), value)
```

- 파이프라인 연산자:  기본 함수를 함성하고 연결하는 방법으로 파이프라인 연산자 `|>`가 ES2017에 추가되었다.
    - 좌변을 평가해 우변에 좌변의 값을 단항 함수 호출로 적용한다.
    - 단항 함수에서만 동작한다.

    ```jsx
    5 |> double |> double |> increment
    // 5 => 5*2 => 10*2 => 20+1 => 21
    ```

### 컴포지션의 특이점

- 결합 법칙이 성립한다.

    ```jsx
    compose(f, compose(g,h)) == compose(compose(f,g), h)
    ```

    - 따라서 함수형 컴포지션은 결합이 가능하다.