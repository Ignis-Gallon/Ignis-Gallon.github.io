---
title: "6.9 Detail Screen"
date: 2023-03-01
last_modified_at: 2023-03-04
category: study
tags: [Flutter, 노마드코더, GestureDetector, Navigator, 화면 전환]
---

# 6.9 Detail Screen

작성일 2023-03-01 · 최종 수정일 2023-03-04
{ .post-date }

- GestureDetector : 대부분의 동작을 감지해주는 역할을 한다.

onTap : 유저가 버튼을 클릭할 때 발생하는 이벤트 (onTapDown, onTapUp)

- webtoon_widget에 GestureDetector를 달아주고, onTap에서 Navigator를 이용하자. Navigator는 context 정보가 필요하며, route를 가져서 DetailScreen 같은 StatelessWidget을 애니메이션 효과로 감싸서 스크린처럼 보이도록 한다.
- MaterialPageRoute : 또 다른 클래스이며, StatelessWidget을 route로 감싸서 다른 스크린처럼 보이게 만들어준다. builder는 route를 만드는 함수이다.
- Home 화면을 떠나게 되면, Home 화면에 귀속되어 있던 Scaffold가 사라지게 되므로 Scaffold를 다시 그려주어야 한다. 결국 detail_screen도 하나의 StatelessWidget이므로, 그 안에서 build로 AppBar를 다시 그려주도록 하자.

- Navigator.push를 사용하면 애니메이션을 통해 유저가 다른 페이지로 왔다고 느끼게끔 할 수 있다. (사실은 다른 StatelessWidget을 render했을 뿐이지만 말이다.)
- Home_screen에서 사용했던 이미지를 생성하는 Container를 떼와서 코드에 붙인다. (Widget으로 떼도 된다.)
- fullscreenDialog option을 사용하면, Widget이 옆에서 튀어나오는 것이 아니라 하나의 창이 새로 열리는 것처럼 아래서 나오게 할 수 있다. 이전에는 AppBar에 생성되는 화면 이동 Widget이 화살표였지만, fullscreenDialog option을 사용하면 X가 나오게 된다.
- 그런데, 안드로이드에서는 해당 애니메이션이 잘 적용되지 않는다. 이를 대체하려면 MaterialPageRoute  대신 PageRouteBuilder를 사용하면 된다.

[Animate a page route transition](https://docs.flutter.dev/cookbook/animation/page-route-animation)

PageRouteBuilder를 이용한 Animation 코드는 아래와 같다. (Tween과 Curve를 이용한 easeout 적용)

```dart
import 'package:flutter/material.dart';
import 'package:toonflix/screens/detail_screen.dart';

class Webtoon extends StatelessWidget {
  final String title, thumb, id;

  const Webtoon({
    super.key,
    required this.title,
    required this.thumb,
    required this.id,
  });

  @override
  Widget build(BuildContext context) {
    return GestureDetector(
      onTap: () {
        Navigator.of(context).push(_createRoute());
      },
      child: Column(
        children: [
          Container(
            clipBehavior: Clip.hardEdge,
            decoration: BoxDecoration(
                borderRadius: BorderRadius.circular(15),
                boxShadow: [
                  BoxShadow(
                    blurRadius: 3,
                    offset: const Offset(10, 10),
                    color: Colors.black.withOpacity(0.5),
                  )
                ]),
            width: 250,
            child: Image.network(
              thumb,
            ),
          ),
          const SizedBox(
            height: 10,
          ),
          Text(
            title,
            style: const TextStyle(
              fontSize: 18,
            ),
          ),
        ],
      ),
    );
  }

  Route _createRoute() {
    return PageRouteBuilder(
      pageBuilder: (context, animation, secondaryAnimation) =>
          DetailScreen(title: title, thumb: thumb, id: id),
      transitionsBuilder: (context, animation, secondaryAnimation, child) {
        const begin = Offset(0.0, 1.0);
        const end = Offset.zero;
        const curve = Curves.ease;

        var tween =
            Tween(begin: begin, end: end).chain(CurveTween(curve: curve));

        return SlideTransition(
          position: animation.drive(tween),
          child: child,
        );
      },
    );
  }
}
```
