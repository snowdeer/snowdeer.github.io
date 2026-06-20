---
layout: post
title: vscode에 Claude bypass 모드 설정
category: AI Coding Agent
permalink: /common-sense/:year/:month/:day/:title/

tag: [claude]
---

# vscode에서 bypass 모드 설정하기

## vscode settings

먼저 세팅에서 아래 설정을 켜야 함

![alt text](/assets/ai-agent/ai-agent-001.png)

![alt text](/assets/ai-agent/ai-agent-002.png)

#### claude/settings.json

프로젝트 루트에 `claude/settings.json`

```json
{
  "permissions": {
    "allow": ["Bash", "Read"],
    "deny": [],
    "defaultMode": "bypassPermissions"
  }
}
```

완전히 격리된 환경(컨테이너, VM, CI 등)이 아닌 경우 bypassPermissions 모드 사용에 주의가 필요함.

## 적용 확인

![alt text](/assets/ai-agent/ai-agent-003.png)
