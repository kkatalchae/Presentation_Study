OSI(Open Systems Interconnection) 7 계층은?
- 국제표준화 기구에서 정의한 모델
- 상이한 컴퓨터 시스템이 서로 통신할 수 있는 표준을 제공

OSI 모델이 알아야 할까?
- 통신상 프로토콜에 따라 영역을 나눈다면 정보전달, 의사소통, 문제해결에 용의함
- TCP/IP등 관점에 따라 더 선호되는 모델이 존재할수 있지만 OSI는 여전히 통용되는 모델

특징
- 각 레이어는 하위 레이어에서 제공하는 기능을 기반으로 동작
---
![응용계층.png](..%2Fimages%2Fnetwork%2Fosi%2F%EC%9D%91%EC%9A%A9%EA%B3%84%EC%B8%B5.png)
7계층 응용 계층 (Application Layer)
- 소프트웨어가 사용자에게 의미있는 데이터를 제공하기 위해 의존하는 프로토콜과 데이터를 조작

프로토콜 || 표준 (일부)
  - HTTP (Hypertext Transfer Protocol)
  - SMTP (Simple Mail Tansfer Protocol)
  - DNS (Domain Name System)
  - FTP (File Transfer Protocol)
  
---
![표현계층.png](..%2Fimages%2Fnetwork%2Fosi%2F%ED%91%9C%ED%98%84%EA%B3%84%EC%B8%B5.png)
6계층 표현계층 (Presentation Layer)
- 데이터를 표현한다. -> 데이터의 변환, 암호화, 압축을 담당

---

5계층 세션계층 (Session Layer)
- 두 기기 사이의 통신을 시작하고 종료하는 일을 담당하는 계층

프로토콜 || 표준 (일부)
- TLS (Transport Layer Security)

---
![전송계층.png](..%2Fimages%2Fnetwork%2Fosi%2F%EC%A0%84%EC%86%A1%EA%B3%84%EC%B8%B5.png)
4계층 전송계층 (Transport Layer)
- 세션계층의 데이터를 세그먼트로, 세그먼트를 세션계층의 데이터로 재조립하여 전송
- 흐름제어, 오류 제어 역할도 담당

프로토콜 || 표준 (일부)
- TCP (Transmission Control Protocol)
- UDP (User Datagram Protocol)

---
![네트워크계층.png](..%2Fimages%2Fnetwork%2Fosi%2F%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC%EA%B3%84%EC%B8%B5.png)
3계층 네트워크계층 (Natwork Layer)
- 동일한 네트워크에서는 필요하지 않은 계층
- 세그먼트를 패킷으로 세분화하여 전달 수신자는 패킷을 다시 세그먼트로 재조립

프로토콜 || 표준 (일부)
- IP (Internet Protocol)
- ICMP (Internet Control Message Protocol)
- IGMP (Interget Group Management Protocol)
- IPsec (Internet Protocol Security)

라우팅이란?
- 네트워크 계층에서 데이터가 표적에 도달하기 위한 최상의 물리적 경로를 찾는다는 의미

---

2계층 데이터 연결 계층 (Data Link Layer)
- 패킷을 프레임으로 세분화
- 인트라 네트워크 콩신에서 흐름 제어, 오류 제어 담당

---

1계층 물리적 계층 (Physical Layer)
- 1과 0의 문자열인 bit stream 으로 변환
- 신호 규첵의 의해 1과 0을 구별할 수 있음

---

기타 자료
![기타자료.png](..%2Fimages%2Fnetwork%2Fosi%2F%EA%B8%B0%ED%83%80%EC%9E%90%EB%A3%8C.png)
- 밝은초록색을 3계층이라 했을때 라우터는 3계층까지
- 영상이 재생중인 화면은 사용자와 상호작용중이기에 7계층까지
- ALB, NLB, Layer4 Firewall.. 생각해보기

