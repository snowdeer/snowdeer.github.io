---
layout: post
title: Poetry 예시 코드
category: Python
tag: [Python]
---

# Poetry 활용 샘플 코드 작성

## Poetry Init

```sh
$ mkdir snowdeer-poerty
$ cd snowdeer-poerty
$ poetry init -n
```

## Poetry Add [Packages]

```sh
$ poetry add "fastapi[standard]" uvicorn

Creating virtualenv snowdeer-poetry in /Users/snowdeer/Workspace/snowdeer/snowdeer-poetry/.venv
Using version ^0.125.0 for fastapi
Using version ^0.38.0 for uvicorn

Updating dependencies
Resolving dependencies... Downloading https://files.pythonhosted.org/packages/fa/a8/5b41e0da817d64113292ab1f8247140aac61cbf6cfd085d6a0fa77f4984f/websockets-15.0.
Resolving dependencies... Downloading https://files.pythonhosted.org/packages/c3/e2/51d9ee443aabcd5aa581d45b18b6198ced364b5cd97e5504c5d782ceb82c/fastar-0.8.0-cp3
Resolving dependencies... Downloading https://files.pythonhosted.org/packages/c3/e2/51d9ee443aabcd5aa581d45b18b6198ced364b5cd97e5504c5d782ceb82c/fastar-0.8.0-cp3
Resolving dependencies... Downloading https://files.pythonhosted.org/packages/86/7a/b970cd0138b0ece72eb28f086e933f9ed75b795716ad3de5ab22994b3b54/rignore-0.7.6-cp
Resolving dependencies... (6.2s)

Package operations: 42 installs, 0 updates, 0 removals

  • Installing typing-extensions (4.15.0)
  • Installing exceptiongroup (1.3.1)
  • Installing idna (3.11)
  • Installing mdurl (0.1.2)
  • Installing anyio (4.12.0)
  • Installing certifi (2025.11.12)
  • Installing dnspython (2.8.0)
  • Installing h11 (0.16.0)
  • Installing markdown-it-py (4.0.0)
  • Installing pygments (2.19.2)
  • Installing annotated-types (0.7.0)
  • Installing click (8.3.1)
  • Installing email-validator (2.3.0)
  • Installing httpcore (1.0.9)
  • Installing httptools (0.7.1)
  • Installing pydantic-core (2.41.5)
  • Installing python-dotenv (1.2.1)
  • Installing pyyaml (6.0.3)
  • Installing rich (14.2.0)
  • Installing shellingham (1.5.4)
  • Installing typing-inspection (0.4.2)
  • Installing urllib3 (2.6.2)
  • Installing uvloop (0.22.1)
  • Installing watchfiles (1.1.1)
  • Installing websockets (15.0.1)
  • Installing fastar (0.8.0)
  • Installing httpx (0.28.1)
  • Installing pydantic (2.12.5)
  • Installing rich-toolkit (0.17.1)
  • Installing rignore (0.7.6)
  • Installing sentry-sdk (2.48.0)
  • Installing typer (0.20.1)
  • Installing uvicorn (0.38.0)
  • Installing fastapi-cloud-cli (0.7.0)
  • Installing markupsafe (3.0.3)
  • Installing tomli (2.3.0)
  • Installing annotated-doc (0.0.4)
  • Installing fastapi-cli (0.0.16)
  • Installing jinja2 (3.1.6)
  • Installing python-multipart (0.0.21)
  • Installing starlette (0.50.0)
  • Installing fastapi (0.125.0)

Writing lock file
```

### 디렉토리 구조

아래와 같은 구조의 디렉토리 생성

```
.
├── poetry.lock
├── pyproject.toml
├── README.md
└── snowdeer_poetry
    ├── __init__.py
    └── main.py
```

#### snowdeer_poetry/main.py

```py
from fastapi import FastAPI

app = FastAPI()

@app.get("/hello")
async def hello_endpoint():
    return ["hello", "snowdeer"]
```

## 실행

그 이후 루트 디렉토리에서 아래 명령어 실행

```sh
$ poetry run uvicorn snowdeer_poetry.main:app --host 0.0.0.0 --port 8314 --reload

INFO:     Will watch for changes in these directories: ['/Users/snowdeer/Workspace/snowdeer/snowdeer-poetry']
INFO:     Uvicorn running on http://0.0.0.0:8314 (Press CTRL+C to quit)
INFO:     Started reloader process [60728] using WatchFiles
INFO:     Started server process [60770]
INFO:     Waiting for application startup.
```

## 코드에서 uvicorn 실행하는 법

만약 코드에서 `uvicorn`을 실행하려면 아래와 같이 `main.py` 수정

#### 수정된 main.py

```py
from fastapi import FastAPI

app = FastAPI()

@app.get("/hello")
async def hello_endpoint():
    return ["hello", "snowdeer"]

import uvicorn

def dev_server():
    uvicorn.run("snowdeer_poetry.main:app", host="0.0.0.0", port=8314, reload=True)
```

#### pyproject.toml

```toml
# 파일 맽 끝에 아래 라인 추가

[tool.poetry.scripts]
dev = "snowdeer_poetry.main:dev_server"
```

## 실행

```sh
$ poetry run dev

INFO:     Will watch for changes in these directories: ['/Users/snowdeer/Workspace/snowdeer/snowdeer-poetry']
INFO:     Uvicorn running on http://0.0.0.0:8314 (Press CTRL+C to quit)
INFO:     Started reloader process [64017] using WatchFiles
INFO:     Started server process [64022]
INFO:     Waiting for application startup.
INFO:     Application startup complete.
```
