---
title: "Image DB"
date: 2022-11-18
last_modified_at: 2024-01-12
category: study
tags: [Node.js, MySQL, BLOB, Canvas]
---

# Image DB

- `Image를 DB에 어떻게 저장할 것인가?`

    1. Image는 file로, path만 DB에 저장 (가장 많이 사용된다.)

        장점 : 속도, 확장성

        단점 : image 관리

        → disk(directory)에 image를 저장. (이미지 그 자체를 저장) 그리고 DB에는 path를 저장.

        server에서 요청이 들어오면, DB로가서 path를 확인한다. 그 path를 기반으로 directory에

        가서 이미지를 가져다가 Client에게 전달해준다.

        > 과거의 데이터는 대체로 non-binary 형태 (text)가 주를 이루었기 때문에, 그냥 DB에 곧바로 저장해둬도 문제가 없었다. 하지만 점차 binary 형태의 데이터를 저장하게 되면서 1번과 같은 방식을 채택해서 데이터를 저장하게 되었다.

    2. Image를 DB에 저장

        장점 : image 관리

        단점 : 속도, 확장성

        > 2번은 Image가 DB 내에 존재하므로, 직접 접근이 가능하기 때문에 image 제거와 같은 image 관리가 매우 용이하다. 단, 대규모 사용자를 위한 서비스의 경우에는 굉장히 많은 resource가 필요할 수 있다. 즉, 반대로 생각하면, 대규모 서비스는 1번은 채택하는 것이 유리하고, 소규모는 2번을 채택해도 큰 문제가 없다.

- BLOB : Binary Large OBject → MySQL에서 binary data를 저장하는데 사용하는 형식

    1. TINYBLOB, TINYTEXT `(256 bytes)`
    2. BLOB, TEXT `(64 KB)`
    3. MEDIUMBLOB, MEDIUMTEXT `(16 MB)`
    4. LONGBLOB, LONGTEXT `(4 GB)`

- `Canvas`

    Canvas 안에 있는 Context라는 객체를 잡아서 동작에 대한 명령을 내릴 수 있다.

    ```javascript
    const my_canvas = document.getElementById('my_canvas')
    const ctx = my_canvas.getContext('2d')
    ```
