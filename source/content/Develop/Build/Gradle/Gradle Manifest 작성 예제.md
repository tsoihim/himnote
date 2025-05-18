## 개요
JAR 파일은 전자 서명, 버전 컨트롤 등의 다양한 기능을 제공하며 이는 JAR 파일 내부의 manifest를 이용한 것
JAR 파일의 압축을 풀어서 .MF 파일을 확인할 수 있음
Manifest 파일은 JAR 자체의 메타 정보를 포함하고 있는 파일로 버전, 벤더 등 다양한 정보를 확인 가능

## 예제
jar 생성 시 아래와 같이 manifest에 attribute를 추가하도록 build.gradle 스크립트에 추가
```groovy
jar {
		manifest {
			attributes(
				"Implementation-Title": archiveBaseName,
				"Implementation-Version": archiveVersion,
				"Implementation-Vendor": "Nara" 
			)
		}
}
```

이후 아래와 같이 Java 코드에서 attribute를 가져오도록 할 수 있음
```java
Test.class.getPackage().getImplementationTitle()
Test.class.getPackage().getImplementationVersion()
Test.class.getPackage().getImplementationVendor()
```