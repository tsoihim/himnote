하위 디렉토리 토큰 전체 replace / delete
- `find . -type f -exec sed -i.bak "s/[ORG]/[ALT]/g" {} \\;`
- `find . -type f -name "*.bak" -exec rm {} \\;`