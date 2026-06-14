---
layout: post
title: spec-kit Example (LeRobot Dataset Visualization)
category: AI Coding Agent
permalink: /common-sense/:year/:month/:day/:title/

tag: [claude]
---

# spec-kit 예제 (LeRobot Dataset Visualization)

## speckit-constitution

```md
/speckit-constitution

## 프로젝트 전체의 개발 철학

- UI는 단순하고 직관적으로 유지 — 불필요한 복잡성 배제
- 직접 구현보다 검증된 라이브러리(Huggingface의 lerobot 라이브러리)를 우선 사용
- 로그인이나 인증 필요없는 PoC 서비스
- 백엔드와 프론트엔드로 구성
- 프론트엔드는 브라우저의 화면 100%의 width를 가지는 반응형 디자인
- 기술스택
  - 백엔드: uv 및 Fastapi, LeRobot 공식 라이브러리 사용
  - 프론트엔드: Vue3 및 TypeScript 사용
```

![alt text](/assets/ai-agent/spec-kit-006.png)

## speckit-specify

```md
/speckit-specify

다음 기능을 포함하는 웹 기반 LeRobot dataset visualization 어플리케이션 개발해줘.

## 기능 요구사항

- Dataset 다운로드 기능
  - Huggingface의 LeRobot dataset 리스트 출력 및 선택
  - 선택한 Huggingface LeRobot Dataset 다운로드해서 백엔드의 datasets 디렉토리 내에 저장
- Dataset 리스트 출력 및 선택 기능
  - 저장된 Dataset 리스트 출력 및 선택
  - 선택한 Dataset 내 Episode 리스트 출력 및 선택
- 선택한 Episode의 데이터 출력
  - Dataset 내부의 비디오 영상 배치
  - 그 아래 data 내부의 parquet의 센서 데이터(action 및 observation.state)를 시계열 차트 형태로 출력
  - Play, Pause, Stop 기능
- LeRobot Dataset v3.0 지원

## 기술 스택

- 백엔드: uv 및 Fastapi, LeRobot 공식 라이브러리 사용
- 프론트엔드: Vue3 및 TypeScript 사용
```

![alt text](/assets/ai-agent/spec-kit-007.png)

## speckit-plan

```md
/speckit-plan

## 기술 계획 작성

요구사항을 바탕으로 Todo 앱의 기술 계획을 작성해주세요.

다음 내용을 포함해주세요:

- 백엔드, 프론트엔드의 각각의 파일 구조
- LeRobot 데이터 구조(에피소드 구분, 카메라, action, observation.state 등)
- Parquet 스트리밍 서버 구조
- 백엔드와 프론트엔드간 데이터 요청 및 전송 API
- 프론트엔드의 스켈레톤 UX 구조
```

![alt text](/assets/ai-agent/spec-kit-008.png)

## speckit-tasks

```md
/speckit-tasks

계획을 작고 실행 가능한 태스크 단위로 분해해주세요.
의존성 순서대로 정렬해주세요 (먼저 해야 할 것부터).

태스크 형식 예시:

- [ ] 백엔드, 프론트엔드의 기본 디렉토리 구조 생성
- [ ] 프론트엔드의 기본 스타일 및 CSS 작성
- [ ] 백엔드의 Huggingface LeRobot Dataset 리스트 획득 기능
- [ ] 프론트엔드의 Huggingface LeRobot Dataset 리스트 출력 및 다운로드 화면
- [ ] 백엔드의 Huggingface LeRobot Dataset 다운로드 기능
- [ ] 프론트엔드의 현재 다운로드된 LeRobot Dataset 리스트 조회 화면
- [ ] 선택한 Dataset의 episode 리스트 출력
- [ ] 선택한 episode의 데이터 상세 출력 페이지
- [ ] action 및 observation.state 값을 시계열(x축: 시간, y축: 값) 차트로 출력
- [ ] 재생시 차트에 붉은색 timeline 출력
- [ ] 재생 시 멀티 비디오 및 차트의 동기화
```

![alt text](/assets/ai-agent/spec-kit-009.png)

## 5. speckit-implement

```sh
/speckit-implement

태스크 목록의 모든 항목을 코드로 구현해주세요.
원칙(constitution)과 요구사항(spec)을 엄격히 따라주세요.

구현 가이드라인:
- 백엔드, 프론트엔드 각각 디렉토리로 구분
  - 백엔드: uv 및 Fastapi 활용
  - 프론트엔드: Vue3 및 TypeScript 활용
```

![alt text](/assets/ai-agent/spec-kit-010.png)

이 상태에서 완벽히 동작하는 코드가 나오지는 않음  
테스트를 계속하면서 올바르게 동작하는 코드를 만들어가야 함
