- @Value default 값 없음
- DI 하는 방식이 생성자를 직접 작성 함 : annotation 으로 변경 
- DI 시 생성자에서 interface가 아니라 impl 임
- service packge 내에 impl packge 구분 필요
- hostMonitor 는 왜 service가 이나라, component 인가? 
- IpValidator.isValidIp() -> 어떻게 valid 하지 않은지 user에게 알려주려면 어떻게 해야 할까? 
- ApiException constructor overide 안 함? 이유는? 
- ApiExceptionEntity 사용하는 곳? 
- Service interface에 exception이 define되지 않는 이유는? 
- ExceptionEnum 가 HttpStatus를 갖어야하는 이유는? 
- HostService.get 이 list base로 동작해야 하는 이유는? 특정 ip 하나로 get하고 싶을 때 불편 함
- HostServiceImpl.update 는 error filter code가 마지막 else에 있음. 다른 function과 일관성 고민 필요
- List\<Info> hostList = new ArrayList\<Info>(); -> List\<Info> hostList = new ArrayList\<>(); 
- HostDto, HostStatusDto 를 나눈 이유는? vs Repo를 같이 쓰는 이유는?
- HostStatusServiceImpl.icmpTimeout 사용하는 곳? 
- hostMonitor() 에서 나눠서 처리하는 방식이 로직으로 되어 있는데, 더 좋은 방법은? 
- MonitorTask 생성자에 파라미터로 ip만 있으면 어떨지? 
- MonitorTask 에서 update 를 하는 것이 좋을지? 
- exception나는 상황이라면 어떤 현상이 있을까?

- Rest API 생성,수정 response body or header에 uri를 return 해주면 좋을것 같음. 
- 구현부가 domain layer일지, service layer일지 확인 해보면 좋을것 같음. 
- Entity update시 dirty-checking 관련 코드 
- Entity의 builder 대신 static factory method 활용 해보는것도 괜찮을것 같음. 
- class naming 오해소지가 있음 - Entity \<-> VO
