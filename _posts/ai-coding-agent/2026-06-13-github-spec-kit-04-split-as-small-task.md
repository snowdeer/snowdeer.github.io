---
layout: post
title: spec-kit Example (작은 단위의 Task로 나누어 명세)
category: AI Coding Agent
permalink: /common-sense/:year/:month/:day/:title/

tag: [claude]
---

# spec-kit 예제 (작은 규모의 Task 명세)

처음부터 너무 큰 규모의 프로젝트를 만드려고 하면 명세가 너무 방대해져서  
비효율적이 됨

**전체를 한 번에 명세하는 않는 것**이 중요함

## 기본 전략: Epic -> Feature 단위로 쪼개기

```sh
전체 프로젝트 (너무 큼 ❌)
    ↓
Epic 단위로 분리
    ↓
Feature 단위로 spec-kit 실행 (✅ 이 단위로)
```

실제 `spec-kit`에서도 권장하는 방식임.
`specify init` 할 때 프로젝트 전체가 아닌 기능 단위 브랜치에서 실행하는게 기본 사용법

## 구체적인 운영 방법

### 1. Constitution만 전체 프로젝트 기준으로 한 번 작성

```sh
/speckit.constitution

# 이것만 프로젝트 전체 기준으로 작성
- 기술 스택, 코딩 컨벤션, 아키텍처 원칙
- 한 번 쓰고 거의 수정 안 함
```

### 2. 나머지는 기능 브랜치마다 반복

```sh
bash# 기능마다 브랜치 따고
git checkout -b feature/todo-auth

# 그 기능만 명세
/speckit.specify   # 이 기능에서 뭘 만들지만
/speckit.plan      # 이 기능의 기술 계획만
/speckit.tasks     # 이 기능의 태스크만
/speckit.implement
```

### 3. Specify는 딱 한 기능만 기술

```sh
# ❌ 이렇게 하지 말 것
"로그인, 회원가입, 대시보드, 알림, 결제, 관리자 페이지 만들어줘"

# ✅ 이렇게
"이번 브랜치에서는 이메일/비밀번호 로그인 기능만.
- 로그인 폼 UI
- JWT 토큰 저장
- 로그인 실패 에러 처리
이것만 구현"
```

### 프로젝트 규모별 권장 단위

| 규모   | 브랜치 하나에 담을 범위       |
| ------ | ----------------------------- |
| 소규모 | 화면 하나 (예: 로그인 페이지) |
| 중규모 | 도메인 하나 (예: 인증 전체)   |
| 대규모 | 마이크로서비스 하나           |

> 결국 `spec-kit`을 프로젝트 전체 설계 도구가 아니라 기능 단위 실행 도구로 쓰는 게 핵심

## 앞서 개발했던 LeRobot Dataset Visualization Task 쪼개기

### 분리 기준

| 기능      | 설명                              |
| --------- | --------------------------------- |
| Feature 1 | 프로젝트 기반 셋업                |
| Feature 2 | HuggingFace Dataset 다운로드      |
| Feature 3 | 로컬 Dataset & Episode 탐색       |
| Feature 4 | Episode 뷰어 (영상 + 차트 + 재생) |

### Feature 1: 프로젝트 기반 셋업

```sh
/speckit.specify

백엔드(FastAPI)와 프론트엔드(Vue3) 프로젝트 기본 골격을 구성해줘.

## 요구사항
- 백엔드: uv 기반 FastAPI 프로젝트 초기화
  - /health 엔드포인트 (서버 상태 확인용)
  - datasets/ 디렉토리 자동 생성
  - CORS 설정 (Vue 개발 서버 허용)
- 프론트엔드: Vue3 + TypeScript 프로젝트 초기화
  - axios 또는 fetch 기반 API 클라이언트 설정
  - 기본 레이아웃 컴포넌트 (사이드바 + 메인 영역)
- LeRobot Dataset v3.0 지원
```

### Feature 2: HuggingFace Dataset 다운로드

```sh
/speckit.specify

HuggingFace에서 LeRobot dataset을 검색하고 로컬에 다운로드하는 기능을 구현해줘.

## 요구사항
- 백엔드
  - LeRobot 공식 라이브러리로 HuggingFace LeRobot dataset 리스트 조회 API
  - 선택한 dataset을 백엔드 datasets/ 디렉토리에 다운로드하는 API
  - 다운로드 진행 상태 반환 (진행률 or 완료 여부)
- 프론트엔드
  - HuggingFace dataset 리스트 출력 및 선택 UI
  - 다운로드 버튼 및 진행 상태 표시

## 의존성
- Feature 1 (프로젝트 셋업) 완료 후 진행
```

### Feature 3: 로컬 Dataset & Episode 탐색

```sh
/speckit.specify

로컬에 저장된 dataset 목록과 그 안의 episode를 탐색하는 기능을 구현해줘.

## 요구사항
- 백엔드
  - datasets/ 디렉토리 내 저장된 dataset 리스트 반환 API
  - 선택한 dataset 내 episode 리스트 반환 API
- 프론트엔드
  - 저장된 dataset 리스트 출력 및 선택 UI
  - 선택한 dataset의 episode 리스트 출력 및 선택 UI

## 의존성
- Feature 1 (프로젝트 셋업) 완료 후 진행
- Feature 2와 병렬 개발 가능
```

### Feature 4: Episode 뷰어

```sh
/speckit.specify

선택한 episode의 영상과 센서 데이터를 동기화해서 시각화하는 기능을 구현해줘.

## 요구사항
- 백엔드
  - 선택한 episode의 비디오 파일 스트리밍 API
  - episode의 parquet에서 action 및 observation.state 센서 데이터 반환 API
- 프론트엔드
  - 비디오 플레이어 (Play / Pause / Stop)
  - 센서 데이터 시계열 차트 (비디오 아래 배치)
  - 재생 시간에 따라 차트 현재 위치 동기화

## 의존성
- Feature 3 (Episode 탐색) 완료 후 진행
```
