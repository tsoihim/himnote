> **WakeOnLAN (WOL)**
> 
> WakeOnLAN is the protocol name given to the so-called Magic Packet technology, developed by AMD and Hewlett Packard for remotely waking up a remote host that may have been automatically powered-down because of its power management features.

### WOL이란
WOL이란 절전모드나 셧다운 상태에 들어간 호스트를 원격으로 부팅시키기 위한 프로토콜
Ethernet, UDP 프로토콜에 의존적이며 여기서는 Ethernet frame으로만 구성된 WOL에 대하여 서술

### 패킷 포맷

| **Synchronization Stream** | **Target MAC** | **Password (optional)** |
| -------------------------- | -------------- | ----------------------- |
| 6                          | 96             | 0, 4 or 6               |
Synchronization Stream은 연속된 FFh의 모음 (6B)
Target MAC은 타겟 호스트 MAC 주소를 16번 반복한 것 (96B)
Password 필드는 옵션. 있다면 4바이트(IP) 혹은 6바이트(MAC)로 구성
*dl_type = 0x0842

*아래는 필자가 sdn controller로 직접 TLV 조작하여 내보낸 wol 패킷 (openflow encapsulated)
![[Pasted image 20250524235251.png|500]]

### **WOL 사용방법**
wol을 보낼 수 있는 공유기 등에서 wol 등록
호스트 머신에서 OS 및 BIOS쪽 세팅 (절전이나 종료 시 네트워크 어댑터까지 종료되면 WOL 동작이 안되므로)


**References**
- [https://wiki.wireshark.org/WakeOnLAN](https://wiki.wireshark.org/WakeOnLAN)