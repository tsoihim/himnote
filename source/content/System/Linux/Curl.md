> **curl** - transfer a URL
> 
> “curl is a tool to transfer data from or to a server, using one of the supported protocols
> 
> (HTTP, HTTPS, …).”

터미널로 HTTP 관련 테스트 시 유용하게 사용 가능

- GET해오는 경우 아래와 같이 사용
    - curl -X GET \[URL]
- POST하는 경우 아래와 같이 사용
    - curl -X POST \[URL] -d \[REQEUST BODY]
    - ex) curl -X POST \[URL] --header "Content-Type: application/json" --data '{"test1":"test","test2":"test"}’