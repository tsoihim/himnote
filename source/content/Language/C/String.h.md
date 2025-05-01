String 다루는 라이브러리;
다양한 함수가 있고 각각의 세부동작이 다름

C에서 문자열을 다룰 때 null terminating 등 다양한 사항들을 고려해야하므로 상황에 대한 고려없이 아무 함수나 갖다쓰는것은 지양해야함

아래는 String.h에 정의된 함수 예시

### strlen
```c
// terminating null byte 제외한 문자열 길이를 반환
size_t strlen(const char *s);
```

### strcpy, strncpy
```c
// 길이 지정 안되므로 위험
char *strcpy(char *dest, const char *src);

// dest가 더 크면 나머지는 null 채워줌. 하지만 크기 동일하거나 작으면 null 채워줘야함
char *strncpy(char *dest, const char *src, size_t n);
```