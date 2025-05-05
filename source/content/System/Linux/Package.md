<aside> 💡 리눅스 패키지: 리눅스 소프트웨어 실행에 필요한 파일(executable, conf, etc.)들의 묶음.

</aside>

리눅스 패키지는 OS 계열에 따라 크게 RPM과 DEB로 나눌 수 있다. OS 계열로 나누지 않고, 소스 패키지와 바이너리 패키지로도 나눌 수 있는데, 전자의 경우 컴파일이 필요하므로 설치에 시간이 오래걸린다. 바이너리 패키지는 설치가 빠르고 간편하나 실행 파일이 요구하는 라이브러리가 없는 경우 실행이 불가하며 이를 의존성이라 한다. 따라서 대부분 OS는 단순히 패키지 파일 설치/제거 등에 사용하는 저수준 패키지 도구뿐만 아니라, 패키지 검색 및 의존성까지 해결해주는 고수준 패키지 도구 또한 지원한다.

**Remark**[1] 저수준 패키지 도구 : 패키지 파일 설치/제거에 사용. 패키지 간 의존성 등을 해결해주지 못함[2] 고수준 패키지 도구 : 패키지 파일 설치/제거 뿐만 아니라 패키지 검색 및 의존성을 해결해주는 도구

# 1. RPM
Red Hat 계열 OS에서 사용하는 패키징 방식.
Centos에서는 저수준 패키지 도구1로는 rpm, 고수준 패키지 도구2로 yum을 사용한다.

# 2. DEB
Debian 계열 OS에서 사용하는 패키징 방식.
Ubuntu에서는 저수준 패키지 도구1로는 dpkg, 고수준 패키지 도구2로 apt를 사용한다.

본 페이지에서는 간단한 패키지 빌드 방법에 대해 설명.

## **패키지 빌드 튜토리얼**
### **1. 빌드 디렉토리 구성**
패키지 이름을 지정. 일반적인 명명법은 아래와 같음
```
1<project>_<major version>.<minor version>-<package revision>
2예시) async-watcher_1.0-1

```
이름을 지정한 후, 패키지 이름과 동일하게 디렉토리를 생성
```
1$ mkdir async-watcher_1.0-1
```
빌드 디렉토리 내에 바이너리 파일이 위치할 경로를 생성 후, 해당 경로에 바이너리 파일을 복사
```
1$ mkdir async-watcher_1.0-1/usr
2$ mkdir async-watcher_1.0-1/usr/local
3$ mkdir async-watcher_1.0-1/usr/local/bin
4$ cp ./async-watcher async-watcher_1.0-1/usr/local/bin

```
### **2. 패키지 메타데이터 작성**
빌드 디렉토리 내에 DEBIAN 디렉토리 생성, 패키지 메타데이터인 control 파일을 작성
```
1$ mkdir async-watcher_1.0-1/DEBIAN
2$ vi async-watcher_1.0-1/DEBIAN/control
```
메타데이터 파일은 아래와 같은 양식으로 작성
```
1Package: async-watcher # 패키지명
2Version: 1.0-1 # major 및 minor 버전
3Section: Utilities # 패키지 분류
4Priority: optional #
5Architecture: amd64 # 호환 아키텍쳐
6Depends: # 의존 패키지
7Maintainer: OBelle Access Developers <access@atto-research.com> # 패키지 관리자
8Description: A utility for A-Sync table record lookup # 패키지 설명
```
그 외의 필드나 필드별 세부 정보는 debian 정책 참고
### **3. 패키지 빌드**
위 작업을 모두 수행한 뒤, 아래의 명령으로 패키지 빌드 후 deb 파일 생성 확인
```
1$ dpkg-deb --build async-watcher_1.0-1
```


**References**
- [rpm build 실습](https://zetawiki.com/wiki/RPM_%ED%8C%A8%ED%82%A4%EC%A7%80_%EB%A7%8C%EB%93%A4%EA%B8%B0_%EC%8B%A4%EC%8A%B5_\(rpmbuild\))
- [rpm spec](http://wiki.pchero21.com/wiki/Rpmbuild_spec_file)
- [deb build 실습](https://fedingo.com/how-to-build-deb-package-in-ubuntu-debian-linux/)
- [deb control fields](https://www.debian.org/doc/debian-policy/ch-controlfields.html#source-package-control-files-debian-control)