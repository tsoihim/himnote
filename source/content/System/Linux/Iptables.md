> **iptables/ip6tables** — administration tool for IPv4/IPv6 packet filtering and NAT
> 
> Iptables and ip6tables are used to set up, maintain, and inspect the tables of IPv4 and IPv6 packet filter rules in the Linux kernel. Several different tables may be defined. Each table contains a number of built-in chains and may also contain user-defined chains.
> 
> Each chain is a list of rules which can match a set of packets. Each rule specifies what to do with a packet that matches. This is called a `target', which may be a jump to a user-defined chain in the same table

리눅스 환경에서 iptables를 이용해 패킷 필터링하여 트래픽 확인, 차단 등의 다양한 기능을 구현 가능

iptables에는 패킷이 매칭되는 룰들의 리스트인 chain이라는 개념이 있고, 룰이란 매칭되는 패킷마다 어떤 작업이 수행되어야하는지 정의된 것임. 유저는 각 체인에 여러가지 룰을 추가하여 상황과 서버의 용도에 맞게 트래픽을 제어할 수 있음

또, iptables에는 여러 체인들을 가지는 table들(filter, nat, mangle, raw)이 존재.
filter 테이블의 경우에는 세 가지 체인(INPUT, OUTPUT, FORWARD)이 있고, 각 체인의 룰에 매칭 정보를 추가하고 매칭된 패킷이 원하는 액션(ACCEPT, REJECT, DROP)을 지정하면 매우 기본적인 firewall 세팅이 가능

가장 기본적인 명령어 예제는 아래와 같음
- iptables -A INPUT -p tcp --dport 6633 -j ACCEPT (서버에 전송된 openflow 트래픽 허용하는 룰 추가)
- iptables -D INPUT -p tcp --dport 6633 -j ACCEPT (서버에 전송된 openflow 트래픽 허용하는 룰 삭제)
- iptables -t nat -A PREROUTING -p tcp --dport 443 -j REDIRECT --to-port 8443 (포트포워딩)
```bash
root@nara_dev:~ iptables --list -n -v
Chain INPUT (policy ACCEPT 14017 packets, 2537K bytes)
 pkts bytes target     prot opt in     out     source               destination
  190 67148 ACCEPT     tcp  --  *      *       0.0.0.0/0            0.0.0.0/0            tcp dpt:6633
```