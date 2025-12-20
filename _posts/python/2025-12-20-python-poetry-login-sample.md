---
layout: post
title: Python Login 백엔드 샘플(Poetry, FastAPI, SQLite)
category: Python
tag: [Python, FastAPI]
---

# Sample Login 백엔드 서비스 코드

## 샘플 프로젝트 생성

```sh
$ poetry new snowdeer-login-sample

Created package snowdeer_login_sample in snowdeer-login-sample
```

아래와 같은 폴더 구조가 생성됨

```
.
├── pyproject.toml
├── README.md
├── snowdeer_login_sample
│   └── __init__.py
└── tests
    └── __init__.py
```

## 패키지 설치

```sh
$ cd snowdeer-login-sample
$ poetry add "fastapi[standard]" uvicorn sqlalchemy
```

### 샘플 코드 작성

`snowdeer-login-sample/snowdeer_login_sample/main.py`에 아래 내용 생성

<pre class="prettyprint">
from fastapi import FastAPI

app = FastAPI()

@app.get("/hello")
async def hello():
    return {"message": "hello, snowdeer !!"}
</pre>

```sh
# 아래 명령어로 실행
$ poetry run uvicorn snowdeer_login_sample.main:app --reload

# 다른 터미널에서 확인
$ curl localhost:8000/hello

{"message":"hello, snowdeer !!"}
```

## SQLAlchemy 활용 Users 테이블 생성

#### snowdeer-login-sample/snowdeer_login_sample/database.py

<pre class="prettyprint">
from sqlalchemy import create_engine, Column, Integer, String
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import sessionmaker

SQLALCHEMY_DATABASE_URL = "sqlite:///./users.db"
engine = create_engine(SQLALCHEMY_DATABASE_URL)
SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)
Base = declarative_base()

class User(Base):
    __tablename__ = "users"
    id = Column(Integer, primary_key=True, index=True)
    username = Column(String, unique=True, index=True)
    password = Column(String)

Base.metadata.create_all(bind=engine)
</pre>

#### snowdeer-login-sample/snowdeer_login_sample/main.py

<pre class="prettyprint">
from fastapi import FastAPI, Depends
from sqlalchemy.orm import Session
from .database import SessionLocal, User

app = FastAPI()

def get_db():
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()

@app.get("/hello")
async def hello():
    return {"message": "hello, snowdeer !!"}
</pre>

## 회원가입(Register) 및 로그인(Login) 엔드포인트 추가

#### snowdeer-login-sample/snowdeer_login_sample/main.py

<pre class="prettyprint">
from fastapi import FastAPI, Depends, HTTPException
from pydantic import BaseModel
from sqlalchemy.orm import Session
from .database import SessionLocal, User

app = FastAPI()

class UserCreate(BaseModel):
    username: str
    password: str

class UserLogin(BaseModel):
    username: str
    password: str

def get_db():
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()

@app.get("/hello")
async def hello():
    return {"message": "Hello FastAPI with DB!"}

@app.post("/register")
async def register(user: UserCreate, db: Session = Depends(get_db)):
    db_user = User(username=user.username, password=user.password)
    db.add(db_user)
    db.commit()
    db.refresh(db_user)
    return {"msg": "User created", "user_id": db_user.id}

@app.post("/login")
async def login(user: UserLogin, db: Session = Depends(get_db)):
    db_user = db.query(User).filter(User.username == user.username).first()
    if not db_user or db_user.password != user.password:
        raise HTTPException(status_code=400, detail="Invalid credentials")
    return {"msg": "Login successful", "user_id": db_user.id}
</pre>

아래 명령어로 동작 확인

```sh
# 아직 회원 가입을 안했기 때문에 오류가 생겨야 정상
$ curl -X POST http://localhost:8000/login \
  -H "Content-Type: application/json" \
  -d '{"username": "snowdeer", "password": "snowdeer"}'

{"detail":"Invalid credentials"}
```

```sh
# 회원 가입
curl -X POST http://localhost:8000/register \
  -H "Content-Type: application/json" \
  -d '{"username": "snowdeer", "password": "snowdeer"}'

{"msg":"User created","user_id":1}
```

```sh
# 로그인 테스트
$ curl -X POST http://localhost:8000/login \
  -H "Content-Type: application/json" \
  -d '{"username": "snowdeer", "password": "snowdeer"}'

{"msg":"Login successful","user_id":1}

# 잘못된 패스워드로 테스트
$ curl -X POST http://localhost:8000/login \
  -H "Content-Type: application/json" \
  -d '{"username": "snowdeer", "password": "wrong-password"}'

{"detail":"Invalid credentials"}
```

## SHA256 암호화 적용

#### snowdeer-login-sample/snowdeer_login_sample/utils.py

<pre class="prettyprint">
import hashlib

def hash_password(password: str) -> str:
    return hashlib.sha256(password.encode()).hexdigest()
</pre>

#### snowdeer-login-sample/snowdeer_login_sample/main.py

아래 부분을 수정

<pre class="prettyprint">
from .utils import hash_password

# ...

@app.post("/register")
async def register(user: UserCreate, db: Session = Depends(get_db)):
    hashed_pw = hash_password(user.password)
    db_user = User(username=user.username, password=hashed_pw)
    db.add(db_user)
    db.commit()
    db.refresh(db_user)
    return {"msg": "User created"}

@app.post("/login")
async def login(user: UserLogin, db: Session = Depends(get_db)):
    hashed_pw = hash_password(user.password)
    db_user = db.query(User).filter(
        User.username == user.username, User.password == hashed_pw
    ).first()
    if not db_user:
        raise HTTPException(status_code=400, detail="Invalid credentials")
    return {"msg": "Login successful"}
</pre>
