build.gradle에 아래와 같이 추가
```groovy
javadoc {
    options.encoding = "UTF-8"
    options.memberLevel = JavadocMemberLevel.PRIVATE
    options.addStringOption('Xdoclint:none', '-quiet')
}
```

Vlan 클래스에 아래와 같이 주석 추가
```java
/**
 * range : 1~4094
 */
public class Vlan {
    @Getter
    @ToString
    @EqualsAndHashCode
    @NoArgsConstructor(access = AccessLevel.PRIVATE)
    @AllArgsConstructor(access = AccessLevel.PRIVATE)
    @Builder
		...
}
```

빌드 후 빌드 디렉터리 하위에서 html 파일 확인
```
nara-MacBook:build tree -L 2 docs
docs
└── javadoc
    ├── allclasses-index.html
    ├── allpackages-index.html
    ├── com
    ├── constant-values.html
    ├── element-list
    ├── help-doc.html
    ├── index-all.html
    ├── index.html
    ├── jquery-ui.overrides.css
    ├── legal
    ├── member-search-index.js
    ├── module-search-index.js
    ├── overview-summary.html
    ├── overview-tree.html
    ├── package-search-index.js
    ├── resources
    ├── script-dir
    ├── script.js
    ├── search.js
    ├── serialized-form.html
    ├── stylesheet.css
    ├── tag-search-index.js
    └── type-search-index.js

6 directories, 19 files
```