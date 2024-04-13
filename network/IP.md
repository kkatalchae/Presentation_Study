## IP란
인터넷 프로토콜(IP)은 데이터 패킷이 네트워크를 통해 이동하고 올바른 대상에 도착할 수 있도록 합니다.

## IP 주소란?
- IP 주소는 네트워크에서 장치를 식별하는 데 사용되는 고유한 숫자입니다.
- IPv4는 32비트 주소 체계를 사용하며 약 43억 개의 주소를 제공합니다.
- IPv6는 128비트 주소 체계를 사용하여 거의 무한한 수의 주소를 제공합니다.

## IP 헤더 정보
![Screenshot 2024-04-10 at 11.04.31 PM.png](..%2Fimages%2Fnetwork%2Fip%2FScreenshot%202024-04-10%20at%2011.04.31%20PM.png)
- Version - IP version
- IHL (Internet Header Length) - IP헤더의 블록(가로줄)수 (IP4기준) 최소 20byte -> 최대 60byte 
- Time To Live(TTL)(=패킷이 삭제되기 전에 만들 수 있는 네트워크 홉 수)
- DSCP (Differentiated Service Code Point) -  패킷이 받을 서비스의 종류 또는 등급, 홉 단위 동작(PHB)을 구분하는 의미를 지님 [위키피디아 바로가기](https://en.wikipedia.org/wiki/Differentiated_services)
- Total Length 
  - 패킷의 총길이
  - 패킷은 MTU를 넘지 않음[MTU](https://www.cloudflare.com/ko-kr/learning/network-layer/what-is-mtu/)(인터넷에 연결된 장치가 수용할 수 있는 최대 데이터 패킷 크기)  
- Identification - 패킷의 단편화를 식별하기 위한 필드
- Flags - 패킷의 단편화를 제어하는 플래그들을 포함
- Fragment Offset - 패킷의 단편화된 조각이 원래 패킷에서 어디에 위치하는지를 나타내는 오프셋(위치)
- Time To Live 
  - time to live(TTL)는 패킷이 라우터에 의해 폐기될 때까지 네트워크 내부에 존재하도록 설정된 시간 또는 "홉"의 양
  - 패킷화된 IP를 관리하는 순기능존재 ex) ICMP 프로토콜사용 하는경우 0이될경우 원래의 시작지점으로 패킷 반환, 만약 이런게 없다면 패킷은 무한히 돌아다닐수도있음
- Protocal - ICMP.. [더 알아보기](https://en.wikipedia.org/wiki/List_of_IP_protocol_numbers)
- Source IP Address - 시작주소
- Destination IP Address - 목적주소
- Option -> 20byte이외에 추가한 헤더들

## IP라우팅
- 목적지 IP 주소 확인: 라우터는 수신한 패킷의 목적지 IP 주소를 확인합니다.
- 라우팅 테이블 조회: 라우터는 목적지 IP 주소를 기반으로 라우팅 테이블을 조회하여 최적 경로를 결정합니다.
- 최적 경로 선택: 라우터는 라우팅 테이블에서 목적지까지의 최적 경로를 선택합니다.
- 패킷 전달: 선택된 경로에 따라 라우터는 패킷을 다음 라우터로 전달합니다.

