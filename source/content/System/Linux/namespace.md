
네임스페이스란 전체 시스템 자원을 추상화하여 특정 네임스페이스 내 프로세스들이 그들만의 격리된 자원을 가지고 있는 것 처럼 보이게하는 기능. 격리된 자원이 변경되면 네임스페이스 내 프로세스들만 그 사실을 알 수 있음. 컨테이너 구현 등에 사용됨.

네임스페이스의 타입은 자원 별로 아래와 같이 나뉨

| Namespace | Isolates                             |
| --------- | ------------------------------------ |
| Cgroup    | Cgroup root directory                |
| IPC       | System V IPC, POSIX message queues   |
| Network   | Network devices, stacks, ports, etc. |
| Mount     | Mount points                         |
| PID       | Process IDs                          |
| Time      | Boot and monotonic clocks            |
| User      | User and group IDs                   |
| UTS       | Hostname and NIS domain name         |

## Practice
namespace를 사용하면 아래와 같은 일을 할 수 있음
### **mininet 호스트 생성 기능 따라해보기**
- 아래 그림과 같이 두 네트워크 네임스페이스로 호스트 인스턴스 생성 후 ovs로 두 호스트 연결할 것임
![[Pasted image 20250505125919.png|500]]
- Red와 Green 네트워크 네임스페이스 생성
```bash
root@nara_dev:~ ip netns add red
root@nara_dev:~ ip netns add green
root@nara_dev:~ ip netns
green
red
root@nara_dev:~ ls /var/run/netns
green  red
```
- ovs 세팅 (on root ns)
```bash
root@nara_dev:~ sudo ovs-vsctl add-br OVS1
root@nara_dev:~ sudo ovs-vsctl show
0195ce24-4083-4c3c-8f64-49e239a50df7
    Bridge "OVS1"
        Port "OVS1"
            Interface "OVS1"
                type: internal
    ovs_version: "2.5.9"

root@nara_dev:~ sudo ip link add eth0-r type veth peer name veth-r
root@nara_dev:~
root@nara_dev:~
root@nara_dev:~
root@nara_dev:~ ip link
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP mode DEFAULT group default qlen 1000
    link/ether 52:54:00:ca:9a:41 brd ff:ff:ff:ff:ff:ff
9: ovs-system: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 1
    link/ether ea:61:c2:08:cb:fc brd ff:ff:ff:ff:ff:ff
8492: OVS1: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 1
    link/ether 4e:23:1e:57:63:40 brd ff:ff:ff:ff:ff:ff
8493: veth-r@eth0-r: <BROADCAST,MULTICAST,M-DOWN> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 1000
    link/ether 72:25:82:b4:34:35 brd ff:ff:ff:ff:ff:ff
8494: eth0-r@veth-r: <BROADCAST,MULTICAST,M-DOWN> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 1000
    link/ether c2:c7:f5:bc:60:05 brd ff:ff:ff:ff:ff:ff
```
![[Pasted image 20250505125951.png|500]]
- Red 호스트 세팅
```bash
# 링크 세팅
root@nara_dev:~ sudo ip link set eth0-r netns red
root@nara_dev:~ sudo ip netns exec red ip link
1: lo: <LOOPBACK> mtu 65536 qdisc noop state DOWN mode DEFAULT group default qlen 1
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
8494: eth0-r@if8493: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 1000
    link/ether c2:c7:f5:bc:60:05 brd ff:ff:ff:ff:ff:ff link-netnsid 0
root@nara_dev:~ sudo ovs-vsctl add-port OVS1 veth-r

# IP 세팅
root@nara_dev:~ ip netns exec red ip link set dev lo up
root@nara_dev:~ ip netns exec red ip link set dev eth0-r up
root@nara_dev:~ ip netns exec red ip address add 10.0.0.1/24 dev eth0-r
```
→ 동일한 방식으로 green도 세팅하면 완료


**References**
- [https://www.youtube.com/watch?v=_WgUwUf1d34](https://www.youtube.com/watch?v=_WgUwUf1d34)