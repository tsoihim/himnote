> **ss** - another utility to investigate sockets
> 
> ”ss is used to dump socket statistics. It allows showing information similar to netstat. It can display more TCP and state informations than other tools.”

ss 명령을 통해 열려있는 소켓들에 대하여 상태, 송수신 큐 등의 정보를 출력가능
```bash
dev@nara:~ ss -x # domain socket만 출력
Netid  State      Recv-Q Send-Q                          Local Address:Port                                           Peer Address:Port
u_str  ESTAB      0      0                                           * 15757804                                                  * 15757805
u_str  ESTAB      0      0                 /run/systemd/journal/stdout 14402                                                     * 14401
```

### Usage: ss \[ OPTIONS \]
- -l, --listening display listening sockets
- -m, --memory show socket memory usage
- -p, --processes show process using socket
- -t, --tcp display only TCP sockets
- -u, --udp display only UDP sockets
- -x, --unix display only Unix domain sockets