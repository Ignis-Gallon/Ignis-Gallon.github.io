---
title: "3.7 Icons and Transforms"
date: 2023-02-28
last_modified_at: 2023-03-04
category: study
tags: [Flutter, 노마드코더, Transform, Icon]
---

# 3.7 Icons and Transforms

- Row에서는 주축이 MainAxis가 되고, 부축이 CrossAxis이다. 반대로, Column에서는 주축이 CrossAxis가 되고, 부축이 MainAxis가 된다. (따라서 Height, Width도 바뀌게 된다.)
- Transform.scale : 내가 지정한 Widget이 다른 Widget과의 영향을 무시하고 크기를 바꾸거나 이동시키는 등의 동작을 수행할 수 있음.
- Transform.translate : 지정한 Widget을 이동시킬 수 있음.
- clipBehavior : 어떤 아이템이 overflow가 되었을 때, 카드와 같은 Container로 어떻게 동작하게끔 할건지 알려주는 옵션이다. (Container에서 사용됨)
