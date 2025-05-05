## 부팅 USB 제작

### **1. 우분투 이미지 다운로드**
- 원하는 버전, desktop 나 server 의 .iso 파일을 다운로드
### **2. Iso -> Img**
- 부팅디스크 작업시 img파일로 진행되므로 확장자 변환 필요
```bash
$ hdiutil convert -format UDRW -o [변환할 img 경로] [다운받은 iso 파일 경로]
```
- 이후 hdiutil 명령어를 못 읽는다면 `hdiutil` 을 `/usr/bin/hdiutil` 로 진행
- 실행 후 .img.dmg파일로 저장이 되는데, .dmg를 삭제해 .img파일로 만들어 줌
### **3. Disk number 확인**
- diskutil list 명령어로 현재 연결된 디바이스 장치들을 확인
    `$ diskutil list`
- 이후 부팅디스크를 만들 usb를 삽입 후 다시한번 dksiutil list를 통해 추가된 디스크를 확인
    `$ diskutil list`
- 이전과 비교해 추가된 디스크의 디스크 넘버를 확인 (그게 usb의 disk number)
### **4. 부팅USB 제작**
- USB의 마운트를 해제
  `$ diskutil unmountDisk /dev/disk5`
- unmount를 완료한 후 아래의 명령어를 사용해 설치
- _sudo dd if=[4번 작업으로 변환된 img파일의 경로] of=[7번 작업을 통해 알게된 USB disk ] bs=1m
  `$ sudo dd if=~/Downloads/ubuntu-18.04.1-desktop-amd64.img of=/dev/disk5 bs=1m`
- 위의 USB를 설치하려는 컴퓨터에 꽂아 설치 진행

## **부팅 USB 포맷**
- USB포맷을 진행해야 원래 USB로 사용 가능
### **1. Disk number 확인**
- 디스크 설치할 때의 방식과 동일
- 현재 USB의 disk number를 아래의 명령어로 확인 
  `$ disktutil list`
### **2. USB 포맷**
- _diskutil eraseDisk [파일 포맷] [포맷 후 disk 이름] [/dev/disk숫자]_ 
  `$ diskutil eraseDisk FAT32 SANDISK /dev/disk5`
- 파일 포맷에는 아래와 같은 형식을 사용 가능
    - **JHFS+** : Mac OS Extended Journaled (JHFS+)
    - **HFS+** : Mac OS Extended (HFS+)
    - **FAT32** : MS-DOS FAT32
    - **ExFAT** : ExFAT


**References**
- [](https://yeo0.github.io/com/2018/11/13/%EB%A7%A5\(Mac\)%EC%97%90%EC%84%9C-%EC%9A%B0%EB%B6%84%ED%88%AC\(Ubuntu\)-%EB%B6%80%ED%8C%85usb-%EB%A7%8C%EB%93%A4%EA%B8%B0/)[https://yeo0.github.io/com/2018/11/13/맥(Mac)에서-우분투(Ubuntu)-부팅usb-만들기/](https://yeo0.github.io/com/2018/11/13/%EB%A7%A5\(Mac\)%EC%97%90%EC%84%9C-%EC%9A%B0%EB%B6%84%ED%88%AC\(Ubuntu\)-%EB%B6%80%ED%8C%85usb-%EB%A7%8C%EB%93%A4%EA%B8%B0/)