# livestyler-docs
이 저장소는 livestyler SDK와 API의 웹 문서를 관리하는 공간입니다. [MkDocs Material](https://squidfunk.github.io/mkdocs-material/)을 사용하여 작성되었습니다.

## 배포 정보
GitHub Pages를 통해 자동으로 배포됩니다:
https://dob-world.github.io/livestyler-docs/

## 로컬 개발 환경 설정
### Docker를 이용한 설정
1. Docker 이미지 빌드 (프로젝트에 필요한 플러그인을 포함한 이미지를 생성합니다. 최초 설정 시 또는 Dockerfile 변경 시에만 실행합니다.)
```
docker build -t livestyler-docs .
```

2. 빌드된 이미지로 로컬 서버 실행 (실시간 미리보기)
```
docker run --rm -it -p 8000:8000 -v ${PWD}:/docs livestyler-docs
```
서버가 실행되면 브라우저에서 http://localhost:8000으로 접속하여 문서를 확인할 수 있습니다.