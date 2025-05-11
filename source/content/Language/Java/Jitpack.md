Jitpack이란 JVM 기반 코드들의 오픈소스 라이브러리 배포 플랫폼

아래와 같이 Jitpack을 호출, dependecy에 원하는 오픈소스 라이브러리의 주소를 추가하여 해당 repo가 제공하는 라이브러리를 프로젝트 내에서 import하는 것이 가능
```groovy
allprojects {
        repositories {
            jcenter()
            maven { url "<https://jitpack.io>" }
        }
}

dependencies {
	implementation 'com.github.[ANY]:[ANY]:[VER]'
}
```

VER의 경우 git remote에서 릴리즈 버전을 확인 후 그에 맞게 Version을 지정

이와 같이 jitpack을 이용하면 오픈소스 라이브러리를 fork 후 수정하고, 입맛대로 버전을 명시하여 의존성에 추가 후 자유롭게 라이브러리 형태로 사용하는 것이 가능