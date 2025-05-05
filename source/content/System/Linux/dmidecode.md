> **dmidecode** - DMI table decoder ****
> 
> dmidecode is a tool for dumping a computer's DMI (some say SMBIOS) table contents in a human-readable format. This table contains a description of the system's hardware components, as well as other useful pieces of information such as serial numbers and BIOS revision.

Desktop Management Interface (DMI) 테이블이란 메인보드가 가지고 있는 시스템 하드웨어 정보. dmidecode는 이 정보를 사람이 읽을 수 있는 텍스트로 변환하여줌

### Options
```bash
root@nara:~# dmidecode --help
Usage: dmidecode [OPTIONS]
Options are:
 -d, --dev-mem FILE     Read memory from device FILE (default: /dev/mem)
 -h, --help             Display this help text and exit
 -q, --quiet            Less verbose output
 -s, --string KEYWORD   Only display the value of the given DMI string
 -t, --type TYPE        Only display the entries of given type
 -H, --handle HANDLE    Only display the entry of given handle
 -u, --dump             Do not decode the entries
     --dump-bin FILE    Dump the DMI data to a binary file
     --from-dump FILE   Read the DMI data from a binary file
     --no-sysfs         Do not attempt to read DMI data from sysfs files
     --oem-string N     Only display the value of the given OEM string
 -V, --version          Display the version and exit
```

### Usage
```bash
# 서버 제조사 확인
root@nara:~# dmidecode -s system-manufacturer
HP

# 프로세서 제조사 확인
root@nara:~# dmidecode -s processor-manufacturer
Intel(R) Corporation
Intel(R) Corporation
```