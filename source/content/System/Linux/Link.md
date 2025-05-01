### Inode
> In **Unix based** operating system each file is indexed by an **Inode**. Inode are special disk blocks they are created when the file system is created. The number of Inode limits the total number of files/directories that can be stored in the file system.

### Link
리눅스에서 링크란 원본 디렉토리에 대한 shortcut을 만드는 것. 링크를 이용해 depth가 깊은 디렉토리에 대한 링크를 생성하여 원본 디렉토리에 편하게 접근하는 등의 작업이 가능
```bash
root@nara_dev:test ls
origin
root@nara_dev:test cat origin
test
root@nara_dev:test ln origin testlink # origin에 대한 link 생성
root@nara_dev:test ls
origin  testlink
root@nara_dev:test cat testlink # link가 origin을 가리키는 것 확인
test
```

리눅스 링크에는 두가지 타입의 링크; 하드링크와 심볼릭링크가 있음
- 하드링크는 오직 파일에만 링크할 수 있으며 다른 디스크나 볼륨에 존재하는 파일에 대한 참조는 불가. 하드링크는 원본과 같은 inode를 참조하며, 원본 파일이 사라지더라도 유지됨
- 심볼릭링크는 디렉토리에도 링크할 수 있으며 다른 디스크나 볼륨의 파일이나 디렉토리도 참조할 수 있음. 심볼릭링크는 원본이 사라지는 경우 사용불가함. 또한 원본 파일이나 디렉토리의 실제 물리적 위치가 아닌 추상화된 파일 혹은 디렉토리를 참조, 원본과 다른 고유한 inode 사용

```bash
root@nara_dev:test ls
origin
root@nara_dev:test ln origin hlink # hard link 생성
root@nara_dev:test ln -s origin slink # symbolic link 생성
root@nara_dev:test ls -li
267549 -rw-rw-r-- 2 root root 5 Oct 22 07:10 hlink # origin과 같은 inode
267549 -rw-rw-r-- 2 root root 5 Oct 22 07:10 origin
259274 lrwxrwxrwx 1 root root 6 Oct 22 07:19 slink -> origin # origin과 다른 inode
```

**References**
- [https://www.geeksforgeeks.org/inode-in-operating-system/](https://www.geeksforgeeks.org/inode-in-operating-system/)
- [https://www.linux.com/topic/desktop/understanding-linux-links/](https://www.linux.com/topic/desktop/understanding-linux-links/)