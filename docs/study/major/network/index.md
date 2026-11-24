---
title: "네트워크구조및설계"
---

# 네트워크구조및설계

3학년 2학기에 수강한 네트워크구조및설계 수업의 노트입니다. 인터넷의 구성 요소와 패킷 교환에서 시작해 Application Layer(HTTP, 메일, DNS, CDN), Transport Layer(UDP, rdt, TCP의 흐름 제어와 혼잡 제어), Socket 프로그래밍, Network Layer, Link Layer까지 Top-down 순서로 다룹니다.

**네트워크 개요**

- [Network의 기본 구성요소](01-network-basic-components.md): Host, Router, Packet, Protocol, Hop의 개념, Network Edge와 Access Network, Network Core, 패킷 전송 지연
- [Media Example & Network Core](02-media-example-network-core.md): 유선·무선 전송 매체, Circuit Switching(FDM, TDM)과 Packet Switching 비교, ISP 구조
- [Delay, Loss, Throughput & Protocol Layers & Security](03-delay-loss-throughput-protocol-layers-security.md): 네 가지 지연과 Caravan 비유, Throughput, 인터넷 프로토콜 스택과 OSI 모델, 캡슐화, Malware·DoS·Sniffing·IP Spoofing

**Application Layer**

- [Application Layer & Processes Communicating](04-application-layer-processes-communicating.md): Client-Server와 P2P 구조, Socket, 프로세스 주소 지정과 Port Number, 앱이 필요로 하는 전송 서비스
- [Web and HTTP](05-web-and-http.md): Non-persistent와 Persistent 연결, 요청·응답 메시지 형식, Cookie, Web Cache(Proxy Server), Conditional GET
- [Electronic Mail](06-electronic-mail.md): User Agent, Mail Server, SMTP의 메일 전송 과정, HTTP와의 비교, Mail Access Protocol
- [DNS & CDN](07-dns-cdn.md): Root·TLD·Authoritative 서버의 계층 구조와 이름 해석 과정, DNS 메시지 형식, 비디오 스트리밍과 CDN, Netflix 사례

**Transport Layer**

- [Transport Layer](08-transport-layer.md): Network Layer와의 차이, Multiplexing과 Demultiplexing, UDP와 Checksum
- [rdt in TCP](09-rdt-in-tcp.md): FSM 표기법, rdt 1.0·2.0·2.1·2.2의 송신자와 수신자 동작, 타이머를 더한 rdt 3.0
- [TCP Pipelining](10-tcp-pipelining.md): rdt 3.0의 Utilization, Go-Back-N의 FSM과 동작 예시, Selective Repeat의 윈도우 동작과 딜레마
- [TCP Flow Control & Congestion Control](11-tcp-flow-control-congestion-control.md): Sequence Number, RTT와 Timeout, Fast Retransmit, 3-way Handshake, AIMD, Slow Start, TCP Fairness
- [Socket Programming](12-socket-programming.md): Socket 타입과 bind·listen·accept·connect·send·recv, Blocking 문제, select() 기반 다중 접속 서버, Thread

**Network Layer와 Link Layer**

- [Network Layer](13-network-layer.md): Forwarding과 Routing, 라우터 내부 구조, Longest Prefix Matching, IP Datagram과 Fragmentation, Subnet, CIDR, DHCP, NAT, IPv6
- [Link Layer](14-link-layer.md): Multiple Access 문제, ALOHA·Slotted ALOHA·CSMA·CSMA/CD, Exponential Backoff, Taking Turns, MAC Address와 ARP
