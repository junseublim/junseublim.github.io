---
title:  "TIL 3: Webpack"
last_modified_at: 2021-06-22T14:25:52-05:00
categories:
  - Javascript
tags:
  - Webpack
---
# Webpack이란?

웹팩이란 모던 JavaScript 애플리케이션을 위한 `정적 모듈 번들러`이다. 모듈 번들러란 웹 애플리케이션을 구성하는 자원들을 하나(혹은 그 이상)의 번들으로 만드는 도구를 의미한다. 

## 모듈이란?
  - 분리된 각각의 기능 덩어리
  - 잘 작성된 모듈은
      - 견고한 추상화와 캡슐화의 경계를 만든다
      - 일관성 있는 설계를 가진다
      - 명확한 목적을 가진다
  - Webpack Modules
      - Node.js와는 다르게 의존성을 다양한 방식으로 표현 가능하다
          - ES6 import
          - CommonJs require()
          - AMD define and require
          - @import statement inside of a css/sass/less
          - image url in stylesheet or html file

## 웹팩의 등장 배경


## Core Concepts

- **Entry**
    - entry point는 어떤 모듈을 사용해서 내부적으로 의존 그래프를 만들어나갈지를 알려준다.
    - 이 모듈로부터 의존 관계들을 파악해서 의존 그래프를 생성한다.
    - default: `./src/index.js`
    - webpack.config.js에서 변경 가능하다

    ```jsx
    module.exports = {
      entry: './path/to/my/entry/file.js',
    };
    ```

    - multi-main entry : entry에 배열을 넘겨줄수도 있다. 이와 같이 엔트리를 여러개 만든다면 멀티 페이지 애플리케이션을 개발할 수 있다.

    ```jsx
    module.exports = {
      entry: ['./src/file_1.js', './src/file_2.js'],
      output: {
        filename: 'bundle.js',
      },
    };
    ```

- **Output**
    - 번들을 어디로 뱉을 것인지, 파일 이름을 어떻게 할 것인지를 설정한다.
    - default :
        - main output 파일 : `./dist/main.js`
        - 그 외 생성된 파일 : `./dist`
    - webpack.config.js에서 변경 가능하다.

    ```jsx
    const path = require('path');

    module.exports = {
      entry: './path/to/my/entry/file.js',
      output: {
        path: path.resolve(__dirname, 'dist'),
        filename: 'my-first-webpack.bundle.js',
      },
    };
    ```

- **Loaders**
    - Webpack은 기본적으로 Javascript와 JSON 파일밖에 이해하지 못한다. Loader는 웹팩이 다른 타입의 파일들을 모듈로 변환하도록 도와준다.
    - 로더는 webpack configuration에 2개의 property를 가진다.
        - test : 어떤 파일들이 변환되어야하는지
        - use : 어떤 loader를 사용할 것인지

    ```jsx
    const path = require('path');

    module.exports = {
      output: {
        filename: 'my-first-webpack.bundle.js',
      },
      module: {
        rules: [{ test: /\.txt$/, use: 'raw-loader' }],
      },
    };
    ```

- **Plugins**
    - 번들 최적화. 자산 관리, 환경 변수 주입 등 다양한 역할을 해준다.
    - plugin 사용하기 위해선 우선 require()로 가져온 후 plugins 배열안에 넣으면 된다.
    - 대부분의 plugin들은 option을 통해 커스터마이징이 가능하다.

    ```jsx
    const HtmlWebpackPlugin = require('html-webpack-plugin'); //installed via npm
    const webpack = require('webpack'); //to access built-in plugins

    module.exports = {
      module: {
        rules: [{ test: /\.txt$/, use: 'raw-loader' }],
      },
      plugins: [new HtmlWebpackPlugin({ template: './src/index.html' })],
    };
    ```

- **Mode**
    - development, production, none중 하나로 설정.
    - 각 환경에 맞는 웹팩의 built-in 최적화 기능을 사용할 수 있다
      - 개발 모드인 경우 개발자들이 좀 더 보기 편하게 웹팩 로그나 결과물이 보여지고, 배포 모드인 경우에는 성능 최적화를 위한 기본적인 파일 압축 등의 빌드 과정이 추가된다.
    - default : production
    - config 파일에서 설정하거나 CLI에서 옵션으로 넘겨줄 수 있다.

    ```jsx
    module.exports = {
      mode: 'development',
    };
    ```

    ```bash
    webpack --mode=development
    ```

- **Browser** **Compatability**
    - Webpack은 ES5-complliant 브라우저들은 다 지원한다.
    - 더 오래된 브라우저를 지원하고 싶다면, Polyfill을 사용해야 한다.

- Source Map
    - 여러개의 소스 파일들이 번들링이 된 후에는 어디서 에러나 경고가 발생했는지 알기 어렵다.
    - 소스 맵은 컴파일된 코드를 원래의 소스코드로 매핑해주어서 어디서 에러가 났는지 알려준다.
    - source-map :
        - 빌드, 리빌드 느림
        - production 가능
        - 가장 추천됨
    - inline-source-map
        - good for illustrative purpose
        - not good for production

- **Tree** **Shaking**
  - Tree Shaking이란 죽은 코드(dead code)를 제거하는 것을 의미한다. 즉, 디펜덴시 그래프에서 사용되지 않는 코드들을 제거해주는 것이다. 이를 통해 번들 크기를 최소화 할 수 있다.
  - 다만 트리쉐이킹은 ESM에서만 잘 작동하기 때문에, 만약 Babel을 사용한다면 ESM을 CommonJS로 트랜스파일링을 하는 기능을 제거해야 한다.
  ```js
  const presets = [
    '@babel/preset-env',
    {
      // ...
      modules: false
    }
  ]
  ```