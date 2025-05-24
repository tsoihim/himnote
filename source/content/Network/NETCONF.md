### NETCONF란?
- NETwork CONFiguration (NETCONF)
- 네트워크 장비들의 설정 관리를 위한 프로토콜
    - 네트워크 노드 수와 종류의 급격한 증가에 따른 네트워크 설정 자동화 필요성 상승
- TCP/SSH 위에서 정의되는 응용 계층 프로토콜
    - [[XML]] 형식과 그 형식에 따른 동작내용이 주가 된다
    - RPC 패러다임을 사용
![[Pasted image 20250524235752.png|500]]

### NETCONF 구조
- NETCONF layer    
    - Content : 실제 콘텐츠 (YANG 형식)
    - Operations : 동작의 종류 정의
    - Messages : 메세지 단위 정의
    <aside> 💡 NETCONF 요청/응답은 각각 rpc/rpc-reply라는 Element로 감싸짐 해당 Element에는 message-id라는 attribute가 포함되어 요청/응답 간 매칭이 가능 (element and attribute in xml)
    
    </aside>
    
- 동작 종류
    - \<get> : 장치 상태 정보 요청
    - \<get-config> : 장치 설정 요청
        - <edit, copy, delete -config>
    - \<lock> : 장치 설정 락
        - \<unlock>
    - \<close-session> : graceful
        - \<kill-session>
    ![[Pasted image 20250524235855.png|500]]
- Yet Another Next Generation (YANG)
    - NETCONF로 전달하는 데이터 모델링 언어
![[Pasted image 20250524235943.png|500]]
- NETCONF 세션
    - NETCONF 세션은 네트워크 관리자와 기기간의 로지컬한 연결
        - 여러 세션이 동시에 동작 가능
    - 구조
        - \<hello> : 연결 수립
            - \<capability> : 지원 기능 확인
            - \<session-id> : 세션 id 확인
        - \<rpc> : 본 내용
        - \<close-session> : 세션 종료

### NETCONF 데이터 상세
- capabilities
    - 기기와 handshake시 받을 수 있는 기기의 NETCONF 관련 기능 지원 여부

| 이름                        | 설명                                                                                                                                                                                            |
| ------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| writableRunningCapability | indicates that the device supports direct writes to the <running> configuration datastore                                                                                                     |
| candidateCapability       | indicates that the device supports a candidate configuration datastore, which is used to hold configuration data that can be manipulated without impacting the device's current configuration |
| confirmedCommitCapability | indicates that the server will support the <cancel-commit> operation and the <confirmed>, <confirm-timeout>, <persist>, and <persist-id> parameters for the <commit> operation                |
| rollbackOnErrorCapability | indicates that the server will support the "rollback-on-error" value in the <error-option> parameter to the <edit-config> operation                                                           |
| validateCapability        | Validation consists of checking a complete configuration for syntactical and semantic errors before applying the configuration to the device                                                  |
| startupCapability         | The device supports separate running and startup configuration datastores                                                                                                                     |
| notificationCapability    | "ietf-notification-capabilities" provides information related to the YANG-Push capability                                                                                                     |
| interleaveCapability      | indicates that the NETCONF peer supports the ability to interleave other NETCONF operations within a notification subscription                                                                |
| urlCapability             | The NETCONF peer has the ability to accept the <url> element in <source> and <target> parameters                                                                                              |
| xpathCapability           | The XPath capability indicates that the NETCONF peer supports the use of XPath expressions in the <filter> element                                                                            |
| partialLockingCapability  | indicates that the device supports the locking of its configuration with a more limited scope than a complete configuration datastore                                                         |


**References**
- [https://www.rfc-editor.org/rfc/rfc4741](https://www.rfc-editor.org/rfc/rfc4741)