# livestyler-docs
이 저장소는 livestyler SDK와 API의 웹 문서를 관리하는 공간입니다. [MkDocs Material](https://squidfunk.github.io/mkdocs-material/)을 사용하여 작성되었습니다.

## 배포 정보
GitHub Pages를 통해 자동으로 배포됩니다:
https://dob-world.github.io/livestyler-docs/

## 로컬 개발 환경 설정
### Docker를 이용한 설정
1. Docker 이미지 다운로드
```
docker pull squidfunk/mkdocs-material
```
2. 프로젝트 초기화 (새 프로젝트 생성 시에만 실행)

```
docker run --rm -it -v ${PWD}:/docs squidfunk/mkdocs-material new .
```
3. 로컬 서버 실행 (실시간 미리보기)
```
docker run --rm -it -p 8000:8000 -v ${PWD}:/docs squidfunk/mkdocs-material
```
서버가 실행되면 브라우저에서 http://localhost:8000으로 접속하여 문서를 확인할 수 있습니다.">