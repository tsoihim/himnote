> **ip** - show / manipulate routing, devices, policy routing and tunnels

## ip utility
ip 유틸은 리눅스에서 네트워크 설정하기 위한 툴.
이 유틸을 활용하여 인터페이스 up/down 설정, ip 설정 등을 모두 수행 가능

## 명령어 구성
- ip [ OPTIONS ] OBJECT { COMMAND | help }
- OBJECT := { link | address | addrlabel | route | rule | neigh | ntable | tunnel | tuntap | maddress | mroute | mrule | monitor | xfrm | netns | l2tp | tcp_metrics }
- OPTIONS := { -V[ersion] | -h[uman-readable] | -s[tatistics] | -r[esolve] | -f[amily] { inet | inet6 | ipx | dnet | link } | -o[neline] | -n[etns] name | -a[ll] | -c[olor] }

ip 명령어는 위와 같이 ip 명령 뒤에 OBJECT와 COMMAND 등을 조합해서 사용하는 것으로 구성됨
- 예) ip link set eth0 down → eth0 링크를 down시켜라
- ip OBJECT 만 입력하면 조회결과 출력

## Object별 사용법
아래는 자주 사용될 수 있는 몇가지 object에 대한 사용법임
### Link
network device 설정 관리. 네트워크 인터페이스 설정하는데 사용
```bash
$ ip link
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP mode DEFAULT group default qlen 1000
    link/ether 52:54:00:ca:9a:41 brd ff:ff:ff:ff:ff:ff
```
- 명령어
    - ip link add : 가상 링크 추가
    - ip link set : 장치 속성 변경
    - ip link delete : 가상 링크 삭제
### Addr
network 주소 관리. 네트워크 인터페이스들의 주소를 설정하는데 사용
```bash
$ ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 52:54:00:ca:9a:41 brd ff:ff:ff:ff:ff:ff
    inet 10.71.52.7/16 brd 10.71.255.255 scope global eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::5054:ff:feca:9a41/64 scope link
       valid_lft forever preferred_lft forever
```
- 명령어
    - ip addr add : 주소 추가
    - ip addr delete : 주소 삭제
    - ip addr flush : 기준에 맞는 주소들 전부 삭제
### Route
라우팅 테이블 관리
```bash
$ ip route
default via 10.71.1.1 dev eth0 onlink
10.71.0.0/16 dev eth0  proto kernel  scope link  src 10.71.52.7
```
- 명령어
    - ip route add : route 추가
    - ip route change : route 변경
    - ip route replace : route 바꾸거나 추가
    - ip route delete : route 삭제
    - ip route flush : routing 테이블 flush
    - ip route get : 단일 route 조회
    - ip route save : 표준출력으로 routing table 정보 저장 (raw 형식, 추후 restore에 쓸 수 있도록)
    - ip route restore : 표춘입력으로 받은 routing table 정보 복원 (save와 연계)