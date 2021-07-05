---
title:  "TIL 5: Jest 환경설정 방법"
last_modified_at: 2021-06-27T14:25:52-05:00
categories:
  - etc
tags:
  - jest
  - testing
---

# Jest 환경설정 방법

Jest 테스트의 환경설정을 하는 방법을 공부하면서 알게된 여러 가지 방법들과 그 방법들의 차이를 설명하고자 한다.

환경을 설정하는 방법에는 크게 3가지가 있다.

1. beforeAll, beforeEach 는 테스트 파일내에서 모든 테스트케이스 이전에 혹은 각 테스트 케이스 이전에 설정하기 위해 사용한다.
2. globalSetup은 모든 테스트 이전에 딱 한번 실행된다.
3. setupFiles, setupFilesAfterEnv는 각 테스트 파일 실행이전에 실행된다.


## 1. beforeAll, beforeEach

하나의 describe block, 혹은 하나의 파일 내에서 테스트들이 실행되기 전에(beforeAll의 경우 모든 테스트, beforeEach의 경우 각 테스트) 함수를 실행하여 필요한 설정을 할 수 있게 해준다. 

만약 함수에서 promise를 반환한다면, 해당 promise가 resolve될 때까지 기다린다.

`timeout`을 설정해서 abort하기까지 얼마나 기다릴 것인지 설정할 수도 있다

테스트에서 사용해야할 전역 상태를 설정하기에 용이하다.

```js
// sampe.test.js

const globalDatabase = makeGlobalDatabase();

beforeAll(() => {
  // 데이터베이스를 정리하고 필요한 데이터를 집어 넣는다
  // Promise가 resolve될때까지 기다린다
  return globalDatabase.clear().then(() => {
    return globalDatabase.insert({testData: 'foo'});
  });
});

test('can find things', () => {
  return globalDatabase.find('thing', {}, results => {
    expect(results.length).toBeGreaterThan(0);
  });
});


```



## 2. globalSetup

모든 테스트가 실행되기 전에 환경 설정하기 위해서 사용할 수 있다.
```js
// setup.js
module.exports = async () => {
  // Set reference to mongod in order to close the server during teardown.
  global.__MONGOD__ = mongod;
};
```
```js
//jest.config.js
module.exports = {
    //...,
    globalSetup: './setup.js'
}
```


## 3. setupFiles, setupFilesAfterEnv

테스트 파일이 실행되기 전에 환경을 설정하기 위해 사용한다. setupFiles는 test framework가 환경에 설치되기 전에 실행되며, setupFilesAfterEnv는 설치된 이후에 실행된다고 한다. 그러나 둘의 차이가 무엇인지 명확하게 파악하기는 어려웠다.

```js
// jest.setup.js
jest.setTimeout(10000); // in milliseconds
```

```js
//jest.config.js
module.exports = {
  setupFilesAfterEnv: ['./jest.setup.js'],
};
```


