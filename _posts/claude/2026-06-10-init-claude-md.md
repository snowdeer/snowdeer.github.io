---
layout: post
title: CLAUDE.md로 프로젝트 설정하기
category: Claude
permalink: /common-sense/:year/:month/:day/:title/

tag: [claude]
---

# CLAUDE.md

`/init` 명령어로 `CLAUDE.md` 파일을 생성할 수 있음  
프로젝트의 구조, 코딩 컨벤션, 배포 방법 등을 설명하는 문서라고 볼 수 있음

| 유형              | 위치                                                   | 용도                  | 공유 범위               |
| ----------------- | ------------------------------------------------------ | --------------------- | ----------------------- |
| 프로젝트 디렉토리 | [프로젝트]/CLAUDE.md <br> [프로젝트]/.claude/CLAUDE.md | 팀 공유 프로젝트 설정 | 팀                      |
| 사용자 디렉토리   | ~/.claude/CLAUDE.md                                    | 개인 전역 설정        | 개인                    |
| 프로젝트 로컬     | [프로젝트]/CLAUDE.local.md                             | 개인 프로젝트 설정    | 개인(`.gitignore` 활용) |

### 사용자 디렉토리(~/.claude/)

사용자 메모리 설정인 `~/.claude/`는 개인의 코딩 스타일, 선호 도구, 공통 명령어 등을 저장할 수 있음

### 프로젝트 디렉토리([프로젝트]/.claude)

팀 차원에서 공유하려면 프로젝트 메모리를 활용
아래와 같은 형태로 다양한 역할의 파일들을 구성할 수 있음

```sh
.
└── .claude
    ├── CLAUDE.md
    ├── settings.json
    ├── agents
    │   └── todo-crud.md
    ├── commands
    │   ├── deploy.md
    │   └── migrate.md
    └── rules
        ├── api-rule.md
        └── component-rule.md

```

| 파일                    | 역할                                                                        |
| ----------------------- | --------------------------------------------------------------------------- |
| CLAUDE.md               | 프로젝트 개요, 도메인 모델, API 명세, 코딩 컨벤션, 네이밍 규칙, 금지사항 등 |
| settings.json           | TypeScript 설정, lint 규칙, 설정 등                                         |
| commands.deploy.md      | 배포 명령어                                                                 |
| commands/migrate.md     | 마이그레이션 명령어                                                         |
| agents/todo-crud.md     | TODO CRUD 구현 에이전트                                                     |
| rules/api-rule.md       | API 가이드 룰                                                               |
| rules/component-rule.md | 컴포넌트 가이드 룰                                                          |

## 파일 로딩 시점

- `CLAUDE.md`는 세션이 시작될 때 자동 로딩
- `.claude/rules/*.md` 파일들은 해당 경로의 파일 작업시에만 조건부 로딩
- 나중에 로드된 설정이 이전 설정을 덮어 씀
- 숫자가 클수록 우선 순위가 높음

## 효과적인 CLAUDE.md

아래 정보를 포함하는 것이 좋음

- 프로젝트 개요: 한 두 문장의 프로젝트 설명, 주요 기능 및 목적
- 기술 스택: 사용 언어, 프레임워크 및 주요 라이브러리
- 핵심 명령어: 빌드, 테스트, lint 등 명령어
- 디렉토리 구조
- 코딩 규칙: 네이밍 컨벤션, 스타일 가이드
- 금지 사항과 예외 규칙: 파일 수정, 삭제 관련 규칙, git, db 등 민감한 조작 금지 등

`CLAUDE.md`가 너무 길어지면 좋지 않고 200라인 이내가 좋음  
너무 길어질 경우 `@import`를 사용해서 작성하는 것이 좋음

```md
# CLAUDE.md

프로젝트 개요

## 상세 문서

- API 설계: @docs/api-spec.md
- Git workflow: @docs/git-workflow.md
```
