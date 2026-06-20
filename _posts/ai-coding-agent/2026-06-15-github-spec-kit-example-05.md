---
layout: post
title: spec-kit Example (Youtube Downloader for Android)
category: AI Coding Agent
permalink: /common-sense/:year/:month/:day/:title/

tag: [claude]
---

# spec-kit 예제 (Youtube Downloader for Android)

## speckit-constitution

```sh
/speckit-constitution

Flutter로 안드로이드 App을 개발할예정이야.  한글로 작성해줘. 유튜브에서 원하는 동영상을 다운로드하는 프로그램을 개발할거야. 많이 사용하는 유명한 파이썬 라이브러리로 유튜브 영상을 다운로드할거야. 개발은 TDD의 Red Green으로 진행해줘. 명확하게 정의한 다음 코드리뷰를 해서 공통 로직이 있는지 리팩토링할 것이 있는지 검사해서 마무리해줘.
```

## speckit-specify

```sh
/speckit-specify

## 기능요구사항
- 프론트엔드에 유튜브 URL 입력 기능이 있음
- 입력받은 URL을 이용해서 유튜브 다운로드 기능 실행
- 저장옵션 선택(ex. mp4, mp3)
- 다운로드 진행상황 표시

## 기술스택
- uv 기반 파이썬 패키지 관리
- FastAPI
- Vue3 + TypeScript
- vite
```

## speckit-plan

```md
/speckit-plan

한글로 개발 계획 생성해줘.
```

## speckit-tasks

```md
/speckit-tasks

한글로 진행해줘.
```

## speckit-implement

```sh
/speckit-implement

지금까지의 설계를 이용해서 구현해줘.
```

## 개발 완료 코드

- https://github.com/snowdeer/snowdeer-youtube-downloader
