---
title: "path"
date: 2022-11-25
last_modified_at: 2022-11-25
category: study
tags: [Node.js, path 모듈, 경로]
---

# path

작성일 2022-11-25
{ .post-date }

node.js 개발을 할 때는 ‘안전하게’ 경로를 설정하기 위해 path module에 있는 join(), resolve() 메서드를 꼭 사용하는 것이 좋다.

## path module 사용 방법

- 모듈 추출 방법

```javascript
const path = require('path');
```

- 자주 사용하는 메서드

    1. path.join(’경로’, ‘경로’, …)

        여러 개의 경로를 알아서 합쳐준다.

        상위 경로(..), 현재 경로(.)도 알아서 처리한다.

        중간에 ‘/’를 만나면 앞의 경로에 이어서 ‘상대 경로’로 처리한다.

        예를 들어, 현재 경로를 */Users/curryyou/dev/VS_Code_Projects/TestProject* 라고 하겠다.

        ```javascript
        console.log(path.join(__dirname, '/a/b', '..', './b', 'c', '/d'));
        ```

        중간에 겹치는 것은 앞에서부터 이어서 ‘상대경로’로 처리한다. 따라서 path.join()의 결과는 다음과 같다. */Users/curryyou/dev/VS_Code_Projects/TestProject/a/b/c/d*

    2. path.resolve(’경로’, ‘경로’, …)

        path.join과 거의 동일하지만, resolve는 중간에 ‘/’를 만나면 앞의 경로는 다 무시하고 절대경로로 처리한다.

> path를 static.join 과 합쳐서 잘 사용하면, 사용자가 접근할 때 해당 파일을 원하는 대로 바로바로 제공해주는 기능을 만들 수 있다.
