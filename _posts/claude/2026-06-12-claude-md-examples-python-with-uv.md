---
layout: post
title: CLAUDE.md 예제 (Python with UV)
category: Claude
permalink: /common-sense/:year/:month/:day/:title/

tag: [claude]
---

# CLAUDE.md 예제 (Python with UV)

````md
# CLAUDE.md

이 파일은 Claude가 이 프로젝트에서 코드를 생성하거나 수정할 때 참조하는 가이드입니다.

---

## 프로젝트 개요

- **프레임워크**: FastAPI
- **패키지 매니저**: uv
- **DB**: PostgreSQL + SQLAlchemy (async)
- **유효성 검사**: Pydantic v2
- **테스트**: pytest + httpx
- **컨테이너**: Docker

---

## 프로젝트 구조

```
.
├── app/
│   ├── main.py          # FastAPI 앱 생성 및 라우터 등록
│   ├── config.py        # 환경변수 설정 (pydantic-settings)
│   ├── database.py      # SQLAlchemy 엔진 및 세션
│   ├── models.py        # SQLAlchemy ORM 모델
│   ├── schemas.py       # Pydantic 스키마 (Request / Response)
│   ├── crud.py          # DB CRUD 함수
│   └── routers/
│       └── <domain>.py  # 도메인별 APIRouter
├── tests/
│   ├── conftest.py
│   └── test_<domain>.py
├── pyproject.toml
├── uv.lock
├── .env
├── .env.local
├── .env.test
└── docker
    └── Dockerfile
```

---

## 개발 환경 및 주요 명령어

```bash
# 의존성 설치
uv sync

# 개발 서버 실행
uv run fastapi dev app/main.py

# 프로덕션 서버 실행
uv run fastapi run app/main.py

# 패키지 추가
uv add <package>
uv add --dev <package>       # 개발 전용

# 테스트 실행
uv run pytest                # 전체
uv run pytest -v             # 상세 출력
uv run pytest tests/test_foo.py  # 특정 파일

# Docker
docker compose up --build    # 빌드 후 실행
docker compose up -d         # 백그라운드 실행
docker compose down          # 종료
```

---

## 코드 규칙

### FastAPI

- 라우터는 `app/routers/` 아래 도메인별 파일로 분리한다.
- 라우터는 `APIRouter(prefix="/...", tags=["..."])`로 생성하고 `main.py`에서 `app.include_router()`로 등록한다.
- 경로 함수는 `async def`를 사용한다.
- 응답 모델은 항상 `response_model=`로 명시한다.
- HTTP 예외는 `raise HTTPException(status_code=..., detail=...)`을 사용한다.

```python
# app/routers/items.py 예시
from fastapi import APIRouter, Depends, HTTPException
from sqlalchemy.ext.asyncio import AsyncSession
from app import crud, schemas
from app.database import get_db

router = APIRouter(prefix="/items", tags=["items"])

@router.get("/{item_id}", response_model=schemas.ItemResponse)
async def get_item(item_id: int, db: AsyncSession = Depends(get_db)):
    item = await crud.get_item(db, item_id)
    if not item:
        raise HTTPException(status_code=404, detail="Item not found")
    return item
```

### Pydantic v2

- 스키마는 `app/schemas.py`에 모아서 관리한다.
- Request / Response 스키마를 명확히 분리한다.
- `model_config = ConfigDict(from_attributes=True)`를 Response 스키마에 반드시 적용한다.
- `field_validator` 또는 `model_validator`를 활용하고, v1 스타일 (`@validator`)은 사용하지 않는다.

```python
from pydantic import BaseModel, ConfigDict, field_validator

class ItemCreate(BaseModel):
    name: str
    price: float

    @field_validator("price")
    @classmethod
    def price_must_be_positive(cls, v: float) -> float:
        if v <= 0:
            raise ValueError("price must be positive")
        return v

class ItemResponse(BaseModel):
    model_config = ConfigDict(from_attributes=True)

    id: int
    name: str
    price: float
```

### SQLAlchemy (Async)

- 엔진은 `create_async_engine`, 세션은 `AsyncSession`을 사용한다.
- 세션은 `async_sessionmaker`로 생성하고 `get_db` 의존성을 통해 주입한다.
- ORM 모델은 `app/models.py`에 정의한다.
- CRUD 함수는 `app/crud.py`에 모으고, 각 함수는 `db: AsyncSession`을 첫 번째 인자로 받는다.

```python
# app/database.py
from sqlalchemy.ext.asyncio import create_async_engine, async_sessionmaker, AsyncSession
from app.config import settings

engine = create_async_engine(settings.database_url, echo=False)
AsyncSessionLocal = async_sessionmaker(engine, expire_on_commit=False)

async def get_db():
    async with AsyncSessionLocal() as session:
        yield session
```

### 환경변수 (pydantic-settings)

- 환경변수는 `app/config.py`의 `Settings` 클래스로 관리한다.
- `.env` 파일을 읽어 주입한다. `.env`는 `.gitignore`에 포함하고, `.env.example`을 항상 최신으로 유지한다.
- 전역 인스턴스 `settings`를 통해 접근한다.

```python
# app/config.py
from pydantic_settings import BaseSettings, SettingsConfigDict

class Settings(BaseSettings):
    model_config = SettingsConfigDict(env_file=".env", env_file_encoding="utf-8")

    database_url: str
    secret_key: str = "changeme"
    debug: bool = False

settings = Settings()
```

---

## 테스트 규칙

- 테스트 파일은 `tests/test_<domain>.py` 형식으로 작성한다.
- `conftest.py`에서 테스트용 DB 세션과 `AsyncClient`를 픽스처로 제공한다.
- 테스트 DB는 인메모리 SQLite (`sqlite+aiosqlite:///:memory:`) 또는 별도 PostgreSQL 테스트 DB를 사용한다.
- 각 테스트는 독립적으로 실행 가능해야 하며, 픽스처로 상태를 초기화한다.

```python
# tests/conftest.py 예시
import pytest_asyncio
from httpx import AsyncClient, ASGITransport
from sqlalchemy.ext.asyncio import create_async_engine, async_sessionmaker, AsyncSession
from app.main import app
from app.database import get_db
from app.models import Base

TEST_DATABASE_URL = "sqlite+aiosqlite:///:memory:"

@pytest_asyncio.fixture
async def client():
    engine = create_async_engine(TEST_DATABASE_URL)
    TestSession = async_sessionmaker(engine, expire_on_commit=False)

    async with engine.begin() as conn:
        await conn.run_sync(Base.metadata.create_all)

    async def override_get_db():
        async with TestSession() as session:
            yield session

    app.dependency_overrides[get_db] = override_get_db

    async with AsyncClient(transport=ASGITransport(app=app), base_url="http://test") as ac:
        yield ac

    async with engine.begin() as conn:
        await conn.run_sync(Base.metadata.drop_all)
```

---

## Docker 구성

- `Dockerfile`은 `uv`를 사용해 의존성을 설치하고 `uv run`으로 앱을 실행한다.
- `docker-compose.yml`에서 PostgreSQL 서비스와 앱 서비스를 함께 정의한다.
- DB URL 등 민감한 값은 `.env`를 통해 주입한다.

```dockerfile
# Dockerfile 예시
FROM python:3.12-slim

COPY --from=ghcr.io/astral-sh/uv:latest /uv /usr/local/bin/uv

WORKDIR /app
COPY pyproject.toml uv.lock ./
RUN uv sync --frozen --no-dev

COPY app/ ./app/

CMD ["uv", "run", "fastapi", "run", "app/main.py", "--host", "0.0.0.0", "--port", "8000"]
```

```yaml
# docker-compose.yml 예시
services:
  db:
    image: postgres:16
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: postgres
      POSTGRES_DB: appdb
    ports:
      - "5432:5432"

  app:
    build: .
    env_file: .env
    ports:
      - "8000:8000"
    depends_on:
      - db
```

---

## 하지 말아야 할 것

- `requirements.txt`를 직접 편집하지 않는다. 의존성은 항상 `uv add` / `uv remove`로 관리한다.
- `uv.lock`을 수동으로 수정하지 않는다.
- SQLAlchemy 동기 세션(`Session`)을 사용하지 않는다. 반드시 `AsyncSession`을 쓴다.
- Pydantic v1 스타일 (`@validator`, `orm_mode = True`)을 사용하지 않는다.
- 환경변수를 코드에 하드코딩하지 않는다. 항상 `settings`를 통해 접근한다.
- 라우터 함수 안에 직접 SQL을 작성하지 않는다. CRUD 함수로 분리한다.
````
