---
title: "안정적인 토큰의 형태"
date: 2023-03-13
last_modified_at: 2023-03-13
category: study
tags: [Node.js, JWT, Access Token, Refresh Token, Redis]
---

# 안정적인 토큰의 형태

- JWT를 사용한 토큰의 종류는 크게 두 가지가 있다.

    1. Access Token (A/T)
    2. Refresh Token (R/T)

두 토큰은 모두 사용자가 로그인할 때 sign되고, Client에게 전달된다. A/T은 만료 기한이 짧게는 수 분에서 길게는 몇 시간까지 정도로 길고, R/T은 만료 기한이 짧게는 며칠부터 길게는 몇 주, 몇 달까지의 기간을 갖는다.

A/T는 사용자가 Client 단에서 원하는 동작을 수행할 수 있는 자격을 체크하는데 사용된다. A/T가 유효하다면 사용자는 원하는 동작을 수행할 수 있게 된다. 만약 A/T가 만료되면 서버단에서 R/T을 체크하게 되고, R/T가 유효하다면 새로운 A/T을 sign해서 클라이언트에게 전달해준다. 이를 통해 사용자 UX를 개선할 수 있다.

- 만약 R/T가 탈취되었다면 장기간에 걸쳐 계속해서 A/T을 Refresh 할 수 있으므로, R/T가 한 번 사용되면 R/T를 만료시키고 새로운 R/T를 전달해주는 것이 좋다.

이제 여기서 중요한 것이 A/T과 R/T를 어느 장소에 저장할 것인가? 이다. A/T은 일반적으로 jwt로 sign해서 client에게 보내주고, client에서는 이를 local storage에 저장한다. 이후, token을 API에 전송할 때마다 local storage로 부터 가져와 header에 담아서 보내준다. (cookie에 보관하는 경우에는 httpOnly, secure option을 설정해주면 이전보다는 안전하게 사용할 수 있게 된다.)

R/T도 마찬가지로 jwt로 sign해서 client에게 보내준다. 다만, R/T를 Redis 라는 in-memory DB에 저장한다는 데서 차이가 있다. (여기서 구현 방법에 차이가 있는데, 이 글에서는 화이트리스트를 사용한다.)

- 최초에 R/T을 발행할 때, R/T을 Redis에 보관하고 Client에 보내준다. 그리고 이후에 R/T이 사용되는 경우가 생긴다면 사전에 보관하고 있던 화이트리스트에서 R/T가 존재하는지 확인하고, 존재한다면 A/T을 refresh 해준 후, R/T도 R/T 해준 다음 Client에 보내준다. (이 방식이 Refresh Token Rotation이다. 만약 기존의 R/T이 재사용되면 토큰이 유출되었음을 판단할 수 있다.)

    이렇듯, R/T은 화이트리스트 방식으로 구현하여 유효한 R/T인지 체크할 수 있다. (블랙리스트는 접근할 수 없는 것을 기록해두었다가 체크하는 방식이고, 해당 방식은 유효한 것을 저장해두는 방식임.)

    Redis를 사용하는 주된 이유는, jwt의 단점을 커버할 수 있기 때문이다. Redis를 이용하여 R/T을 sign하면, 원할 경우 해당 토큰을 무효화시킬 수 있게 된다. 왜 이 과정이 필요할까?

    ⇒ A/T과 같이 만료 기한이 짧은 토큰은 탈취되더라도 정보가 노출될 기간 자체가 짧기 때문에 크게 염려하지 않아도 되지만, 만료 기한이 긴 R/T이 탈취된다면 문제가 생긴다. (R/T을 한 번 사용하면 refresh 하도록 하기는 했지만, 어쨌든 사용될 수는 있기 때문이다.) 그렇기 때문에 최우선적으로 XSS, CSRF 공격으로부터 안전하게 토큰을 보호하는 것이 최우선적이다.

    하지만 이것만으로는 토큰을 지키지 못할 수도 있는데, 그렇기 때문에 부가적인 안전 장치를 도입해야 한다. 가령, 평소에 사용자가 접속하던 IP가 아닌 다른 IP에서 접속되었다면 원래 사용자에게 알림 메시지를 보내고, 본인이 아니라고 한다면 R/T을 revoke (폐지) 시킬 수 있는 것이다.

물론 이와 같이 구현하면, JWT의 stateless라는 강점을 일부 잃게 되지만, 토큰 자체만으로는 구현할 수 있는 한계가 있기 때문에 프로젝트의 요구 사항과 상황에 맞추어 판단하는 것이 좋다.

---

[Revoking Access with a JWT Blacklist/Deny List](https://supertokens.com/blog/revoking-access-with-a-jwt-blacklist)

[opaque token을 사용하여 JWT 자체 만료시간 강제 무효화 하기](https://junghyungil.tistory.com/m/191)

[Access Tokens](https://auth0.com/docs/secure/tokens/access-tokens)

[JWT vs. Opaque Tokens](https://zitadel.com/blog/jwt-vs-opaque-tokens)

[Express에서 JWT로 인증시스템 구현하기 ( Access Token과 Refresh Token )](https://velog.io/@kshired/Express에서-JWT로-인증시스템-구현하기-Access-Token과-Refresh-Token)

[12 - Store Refresh Tokens in Redis](https://dev.to/jacobsngoodwin/12-store-refresh-tokens-in-redis-1k5d)

[Why should I store Refresh Token for JWT in the server database? Would storing access token instead be better?](https://stackoverflow.com/questions/69302873/why-should-i-store-refresh-token-for-jwt-in-the-server-database-would-storing-a/69332576#69332576)
