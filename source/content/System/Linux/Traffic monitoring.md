### tshark
- Wireshark의 CLI 버전 툴
- 아래와 같이 CLI로 필터링을 이용하여 패킷 캡쳐 가능
```bash
$ tshark -q -z io,stat,0,"BYTES()tcp.port==6633","FRAMES()tcp.port==6633”
```
- TCP 필터링
    - [https://www.wireshark.org/docs/dfref/t/tcp.html](https://www.wireshark.org/docs/dfref/t/tcp.html)

### pktstat
- 인터페이스별 패킷 정보를 실시간으로 확인 가능 (가볍게 사용하기 좋다)
- 아래와 같이 CLI로 필터링을 이용하여 Interactive한 화면에서 정보 확인 가능
```bash
$ pktstat -i eno1 -cT "tcp port 6633”
# usage: pktstat [-BcFlnpPtT] [-i interface] [-k keeptime] [-m maxbps] [-w wait] [-a abbrev] [-A file] [filter-expr]
```
![[Pasted image 20250505130531.png|500]]
- 사용법 참고 문서
    - [http://www.packetinside.com/2011/10/pktstat.html](http://www.packetinside.com/2011/10/pktstat.html