- QinQ란 (802.1ad)
    - Stacked VLAN, Double tagged, …
        - tagged 프레임에 추가로 태그를 붙여 private VLAN 패킷 터널링 등에 사용
        - 아래 그림과 같이 프레임의 기존 tag (inner) 앞에 추가로 tag (outer) 삽입
- Tag frame format - 4 bytes
    - TPID (Tag Protocol Identifier) - 2 bytes
        - VLAN 태그 존재를 알리는 식별자
        - 0x8100 -> 802.1q
    - TCI (Tag Control Information) - 2 bytes
        - PCP (Priority Code Point) - 3 bit
        - CFI (Canonical Format Identifier) - 1 bit
        - VID (VLAN Identifier) - 12 bit
            - 0과 0xFF를 제외한 4094개 VLAN 식별
![[Pasted image 20250524234808.png|500]]

### Practice
- 아래와 같은 토폴로지로 테스트 환경 구축
  ![[Pasted image 20250524234836.png|300]]
- Mininet 사용한 python2 스크립트로 dp 및 QinQ 호스트 생성
```python

def myNetwork():
    net = Mininet(topo=None, build=False)

    info( '*** Adding controller\\n' )
    net.addController(name='c0', controller=RemoteController, ip='127.0.0.1', port=6633)

    info( '*** Add switches\\n')
    s1 = net.addSwitch('s1', dpid='1')

    info( '*** Add hosts\\n')
    h1 = net.addHost('h1', ip='10.0.10.11', mac='22:33:ff:2a:de:a1')
    h2 = net.addHost('h2', ip='10.0.10.12', mac='22:33:ff:2a:de:a2')

    info( '*** Add links\\n')
    net.addLink(h1, s1, 1, 1)
    net.addLink(h2, s1, 1, 2)

    info( '*** Starting network\\n')
    net.start()

    h1.cmdPrint('ip link add link h1-eth1 h1-eth1.24 type vlan proto 802.1Q id 24')
    h1.cmdPrint('ip link add link h1-eth1.24 h1-eth1.24.36 type vlan proto 802.1Q id 36')
    h1.cmdPrint('ip link set h1-eth1 up')
    h1.cmdPrint('ip link set h1-eth1.24 up')
    h1.cmdPrint('ip addr add 10.0.1.11/24 dev h1-eth1.24.36')

    h2.cmdPrint('ip link add link h2-eth1 h2-eth1.25 type vlan proto 802.1Q id 25')
    h2.cmdPrint('ip link add link h2-eth1.25 h2-eth1.25.36 type vlan proto 802.1Q id 36')
    h2.cmdPrint('ip link set h2-eth1 up')
    h2.cmdPrint('ip link set h2-eth1.25 up')
    h2.cmdPrint('ip addr add 10.0.1.12/24 dev h2-eth1.25.36')

    CLI(net)
    net.stop()

if __name__ == '__main__':
    setLogLevel( 'info' )
    myNetwork()
```
- tcpdump로 호스트가 전송하는 패킷이 double tagged 인지 확인
![[Pasted image 20250524234902.png|500]]
- ovs에 rule 설치하여 outer tag 조작 후 호스트 간 통신이 되는지 확인
  ![[Pasted image 20250524234919.png|500]]


**References**
- http://www.microhowto.info/howto/configure_an_ethernet_interface_as_a_qinq_vlan_trunk.html

from 22.05.09