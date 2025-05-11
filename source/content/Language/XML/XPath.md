### XPath란
[[XML]] 문서의 특정 요소나 속성에 접근하기 위한 경로를 지정하는 문법

### **노드의 형식**
1. 루트 노드(root node) : xml 자체를 표현하는 가상 노드
2. 요소 노드(element node) : 태그
3. 텍스트 노드(text node) : 태그 내용
4. 속성 노드(attribute node) : 속성
5. 주석 노드(comment node) : 주석문
6. 네임스페이스 노드(namespace node) : xmlns: 로 시작하는 태그
7. 처리 명령 노드(processing instruction node) : \<? 태그로 시작하는 것

### **위치경로**
```xml
1. 노드 이름
- 해당 노드이름과 일치하는 모든 노드를 선택

1. /
- 루트 노드부터 순서대로 탐색

1. //
- 현재 노드의 위치와 상관없이 지정된 노드에서부터 순서대로 탐색

1. .
- 현재 노드를 선택

1. ..
- 현재 노드의 부모 노드를 선택

1. @
- 속성 노드를 선택
```

### **위치경로 예제**
```xml
developer            : <developer>요소를 모두 선택
/p_languages         : 루트 노드의 자식 노드인 <p_languages>요소를 선택 (절대 경로 탐색)
p_languages/language : <p_languages>요소의 자식 노드 중 <language>요소를 모두 선택 
												(상대 경로 탐색)
//                   : 루트 노드의 하위 노드를 모두 선택
//priority           : 위치에 상관없이 <priority>요소를 모두 선택
.//                  : 현재 노드의 하위 노드를 모두 선택
version/@status      : 모든 <version>요소의 status 속성 노드를 모두 선택
```

### **문법 및 예제**
```xml
1. contains : 포함 하는 값을 찾아줌
'//div[contains(@class,"class명")]'
- div에서 class명이 포함하는 요소 선택

2. last : 마지막 값을 찾아줌
'//div(@class="class명")/a[last()]'
- div에서 class명으로 된 것들 밑에 있는 a 태그 여러개중 마지막 요소 선택

3. not
'img[contains(@class, "alpha") and not(contains(@class, 'beta'))]'
- class에 "beta"라는 값이 없으면서 "alpha"클라스를 포함한 이미지 선택

4. and
'img[contains(@class, "alpha") and contains(@class, "beta")]'
- class에 "alpha"라는 값과 "beta"라는 값을 모두 포함한 이미지 선택

5. or
'img[contains(@class, "alpha") or contains(@class, "beta")]'
- "alpha"라는 클라스을 가진 이미지 또는 "beta"라는 클라스을 가진 이미지 선택

6. position
'//div(@class="class명")/a[position() > 1]'
- div에서 class명으로 된 것들 밑에 있는 a 태그 여러개중 첫번째를 제외한 요소 선택
```

### **추가 활용 예제**
![[Pasted image 20250511141829.png|500]]
![[Pasted image 20250511141843.png|500]]

**References**
- [https://uuukpyo.tistory.com/14](https://uuukpyo.tistory.com/14)