### TLV란?
Type, Length, and Value(TLV)는 데이터 타입, 길이, 값이라는 세가지 구성요소로 정보를 인코딩하는 스킴
주로 패킷 생성이나 파싱 등에서 쓰임

가령 아래와 같은 포맷을 띌 수 있음
![[Pasted image 20250517194507.png|400]]

Length로 길이를 명확히 명시했으므로 무조건 Length 값으로 데이터 길이를 잘 끊어서 파싱해야함

C에서는 보통 아래와 같은 형태로 구조체 정의하여 TLV 파싱하는 경우가 많음
```c
typedef struct { // LLDP TLV 파싱용 구조체
    uint16_t  type;
    uint16_t  length;
    char value[0];
} ob_lldp_tlv_t;
```