---
title:  "Headless Browser"
last_modified_at: 2021-06-20T14:25:52-05:00
categories:
  - etc
tags:
  - headless
  - browser
  - testing
---

# Headless Browser란?

- Headless Browser란 GUI가 없는 브라우저를 의미한다.
- 일반 브러우저랑 마찬가지로 웹페이지들을 파싱하고 해석할 수 있다
- 주로 자동화된 테스팅, 서버환경에서의 브라우저 사용 등 UI가 필요없는 상황에서 많이 사용된다.

## Use Case

- 테스트 자동화
- 웹 페이지 스크린샷
- 웹 페이지 상호작용 자동화
- Web scrapping
- 자바스크립트 라이브러리 테스팅

# Headless Chrome

- CLI에서 headless chrome 실행하기
    - 우선 ~/.bash_profile에 alias를 생성한다.

    ```bash
    alias chrome="/Applications/Google\ Chrome.app/Contents/MacOS/Google\ Chrome"
    ```

    - Headless chrome 를 실행시킨다

    ```bash
    chrome \                           
    --headless \                       # 크롬을 headless 모드로 실행시킨다
    --remote-debugging-port=9222 \     # localhost:9222에 접속하면 현재 headless browser가 렌더링 중인 페이지를 볼 수 있다
    https://www.google.com             # 오픈할 URL 주소. default는 about:blank.
    ```

- 여러 기능들
    - `document.body.innerHtml`을 stdout으로 출력한다

    ```bash
    chrome --headless --disable-gpu --dump-dom https://www.chromestatus.com/
    ```

    - 현재 페이지를 pdf로 프린트한다.

    ```bash
    chrome --headless --disable-gpu --print-to-pdf https://www.chromestatus.com/
    ```

    - 현재 페이지의 스크린샷을 찍는다.

    ```bash
    chrome --headless --disable-gpu --screenshot https://www.chromestatus.com/
    ```

    - REPL(read-eval-print loop) 모드 : JS expression을 작성할 수 있게 해준다.

    ```bash
    $ chrome --headless --disable-gpu --repl --crash-dumps-dir=./tmp https://www.chromestatus.com/
    [0608/112805.245285:INFO:headless_shell.cc(278)] Type a Javascript expression to evaluate or "quit" to exit.
    >>> location.href
    {"result":{"type":"string","value":"https://www.chromestatus.com/features"}}
    >>> quit
    $
    ```

# 프로그래밍 방식으로 사용하기(Node)

# Puppeteer

Puppetter는 크롬을 제어하는 high-level API를 제공하는 노드 라이브러리이다.

![https://user-images.githubusercontent.com/81942/86137523-ab2ba080-baed-11ea-9d4b-30eda784585a.png](https://user-images.githubusercontent.com/81942/86137523-ab2ba080-baed-11ea-9d4b-30eda784585a.png)

- Puppetter는 DevTools Protocol을 사용해서 브라우저와 통신한다.
    - DevTools Protocol이란? 크롬을 조작, 검사, 디버그 등을 할 수 있도록 해주는 프로트콜이다.
- Browser instance는 여러개의 browser context를 가진다.
    - browser context란? 각자의 session을 지니는 브라우저를 의미한다.
- BrowserContext Instance는 browser 세션을 정의하고 여러개의 page를 가진다.
- Page는 최소 한개의 frame(main frame)을 가지며, iframe 혹은 frame tags로 생성된 다른 프레임들이 존재할 수 있다.

### Puppeteer.launch 설정

- 브라우저가 열어야할 페이지를 클라이언트가 지정할 수 있도록 한다.
- 실행할 테스트의 **네트워크 조건**을 클라이언트가 선택할 수 있도록 한다.
- [DevTools](https://pptr.dev/#?product=Puppeteer&version=v3.1.0&show=api-puppeteerlaunchoptions)의 실행 여부 등을 클라이언트가 지정할 수 있도록 한다.
- 네트워크 연결속도 : puppeteer.launch 실행 구문을 추상화했다면, 다음과 같이 네트워크 프리셋(NETWORK_PRESETS)을 사용해 테스트 환경을 변경할 수 있다.
    - [https://gist.github.com/trungpv1601/2ccd3cc998149a84ba80ed7a4c9ef562](https://www.notion.so/2ccd3cc998149a84ba80ed7a4c9ef562)

# Headless Testing

- Headless Browser를 사용하여 실제 디스플레이를 렌더링하는 프로세스없이 테스트를 진행하는 것을 의미.

## 왜 Headless Testing을 사용할까

### 1. 테스팅 속도

UI를 보여줄 필요가 없기 때문에 cross-browser test보다 빠르게 동작한다.

### 2. 비용, 인프라 사용 절감

테스트가 빠르게 진행되기 때문에 비용 및 인프라의 사용량을 줄일 수 있다

### 3.  안정성 : UI tests are Flaky

UI를 포함한 테스트는 같은 동작에도 다른 결과를 낼 수 있다.

### 4. 이른 테스팅을 가능하게 해준다

적은 비용과 인프라로 테스트를 할 수 있기 때문에 개발 사이클 초기에도 테스트를 실행할 수 있다. 

- QAOps : integrating Quality Assurance into software delivery process.
- 이른 시간에 문제를 발견하고 빠르게 해결할 수 있게 해준다.

## Headless Testing의 한계

- 실제 유저의 입장에서 완벽하게 테스트할 수 없다
    - 실제 브라우저에서 테스트를 실행해야 한다.
        - 브라우저 호환
        - 시각적인 로직
        - color, animation

참고자료
- [https://developers.google.com/web/updates/2017/04/headless-chrome?hl=en](https://developers.google.com/web/updates/2017/04/headless-chrome?hl=en)
- [https://developers.google.com/web/updates/2017/06/headless-karma-mocha-chai?hl=en](https://developers.google.com/web/updates/2017/06/headless-karma-mocha-chai?hl=en)
- [https://github.com/puppeteer/puppeteer](https://github.com/puppeteer/puppeteer)
- [https://saucelabs.com/blog/when-and-when-not-to-use-headless-testing](https://saucelabs.com/blog/when-and-when-not-to-use-headless-testing)
- [https://www.telerik.com/blogs/what-is-headless-browser-testing-when-and-why-use-it](https://www.telerik.com/blogs/what-is-headless-browser-testing-when-and-why-use-it)
- [https://saucelabs.com/blog/headless-browser-testing-101](https://saucelabs.com/blog/headless-browser-testing-101)
- [https://testguild.com/api-testing/](https://testguild.com/api-testing/)
- [https://martinfowler.com/bliki/TestPyramid.html](https://martinfowler.com/bliki/TestPyramid.html)