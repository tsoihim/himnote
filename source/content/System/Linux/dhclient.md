> **dhclient** - Dynamic Host Configuration Protocol Client

dhclient 이용하여 리눅스 환경에서 dhcp로 ip 할당받을 수 있음

### Usage
- dhclient \[Intf\]
    - 입력한 intf에 IP 할당 받기
    - 인터페이스 지정안한 경우 dhclient는 non-broadcast 인터페이스를 제외한 모든 인터페이스를 인식, 각 인터페이스에 설정 시작

\* DHCP 서버의 경우 dnsmasq, dhcpd 등을 사용 가능