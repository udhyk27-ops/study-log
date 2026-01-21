# 프로젝트 디렉토리 구조 마크다운 생성
1. Window
tree /F /A | Out-File structure.md -Encoding utf8

2. Mac
tree -I "node_modules|vendor" -a > structure.md