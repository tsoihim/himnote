> **vconfig** - VLAN (802.1q) configuration program
> 
> The vconfig program allows you to create and remove vlan-devices on a vlan enabled kernel. Vlan-devices are virtual ethernet devices which represents the virtual lans on the physical lan.

vconfig는 인터페이스에 VLAN을 설정하기 위한 프로그램

### Usage
- vconfig add \[intf\] \[vlan-id\]
    - 입력한 intf에 vlan-id를 매칭, 서브 인터페이스를 생성하여줌