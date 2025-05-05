> **mount** - mount a filesystem
> 
> ”The filesystem is used to control how data is stored on the device or provided in a virtual way by network or other services.”

Unix 시스템에서 모든 파일들은 root 디렉토리(/) 아래 체계적으로 정리됨.
파일들이 실제로는 여러 디바이스에 걸쳐서 존재할 수도 있는데, mount 커맨드는 특정 디바이스의 파일시스템이 리눅스 파일트리에 붙을 수 있도록 하는 역할을 함 (반대로 umount 커맨드를 통해 mount를 해제할 수도 있음)

df 커맨드를 통해 아래와 같이 각 파일시스템이 어디에 mount 됐는지 확인해볼 수 있음
```bash
root@acc:/# df -hT
Filesystem     Type   Size  Used Avail Use% Mounted on
tmpfs          tmpfs   26G  3.0M   26G   1% /run
/dev/sda3      ext4   436G   44G  371G  11% /
tmpfs          tmpfs  126G     0  126G   0% /dev/shm
```

리눅스에선 보통 디스크가 추가될 때마다 /dev/sda, /dev/sdb, … 와 같이 뒤에 붙는 알파벳이 하나씩 증가
디스크의 파티션별로는 /dev/sda1, /dev/sda2, … 와 같이 뒤에 붙는 숫자가 하나씩 증가

아래는 Mount로 USB 인식시키는 예제 
- USB 연결
- fdisk -l로 USB 인식 확인
```bash
atto@nara_dev:~ fdisk -l # fdisk 예제
Disk /dev/vda: 30 GiB, 32212254720 bytes, 62914560 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0xe1a0d441

Device     Boot Start      End  Sectors Size Id Type
/dev/vda1  *     2048 62914526 62912479  30G 83 Linux

Disk /dev/vdb: 366 KiB, 374784 bytes, 732 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
```
- mount -t vfat /dev/sdc1 [MOUNT 경로]
    - 이후 df로 mount 확인
    - mount 하고 나면 USB 사용 가능
- 사용 종료 시에는 umount [MOUNT 경로]
    - 해당 디렉토리 잡고 있는 프로세스가 있으면 umount 불가
- 필요 시 MOUNT 경로 디렉토리 삭제
    - usb mount용으로 따로 디렉토리 생성한 경우