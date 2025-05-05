| cmd                                                                                                    | function                                         |
| ------------------------------------------------------------------------------------------------------ | ------------------------------------------------ |
| docker pull 이미지:태그                                                                                     | 이미지를 가져옴                                         |
| docker run -d —name 이름 —net 모드 이미지:태그 -p 포트:포트 -p 포트:포트 --restart always -v \[호스트 디렉토리\]:\[컨테이너 디렉토리\] | 인스턴스 실행 (이름, 권한, 포트포워딩, 마운트, 재시작 옵션 등 다양한 옵션 있음) |
| docker exec -it 이름 /셸                                                                                  |                                                  |
| docker stop                                                                                            |                                                  |
| docker rm                                                                                              |                                                  |
| docker pause                                                                                           |                                                  |
| docker unpause                                                                                         |                                                  |
| docker rmi                                                                                             | 이미지 삭제                                           |
| docker cp                                                                                              | 인스턴스 내 file copy                                 |
| docker update --restart=always <container-id>                                                          | 인스턴스 자동 실행                                       |
| docker rmi -f $(docker images -f "dangling=true" -q)                                                   | Danling된 이미지 전체 삭제                               |

- Network 구성 참고
    - [https://imjeongwoo.tistory.com/113](https://imjeongwoo.tistory.com/113)


**References**
- [https://itholic.github.io/docker-copy/](https://itholic.github.io/docker-copy/)
- [https://techblog-history-younghunjo1.tistory.com/246](https://techblog-history-younghunjo1.tistory.com/246)
- [https://ikcoo.tistory.com/33](https://ikcoo.tistory.com/33)