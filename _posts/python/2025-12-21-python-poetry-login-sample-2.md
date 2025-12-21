---
layout: post
title: Python Login 백엔드 샘플 2(Poetry, FastAPI, PyTest)
category: Python
tag: [Python, FastAPI]
---

# snowdeer-user-api

## 샘플 프로젝트 생성

```sh
$ poetry new snowdeer-user-api

Created package snowdeer_user_api in snowdeer-user-api
```

아래와 같은 폴더 구조가 생성됨

```
.
├── pyproject.toml
├── README.md
├── snowdeer_user_api
│   └── __init__.py
└── tests
    └── __init__.py
```

## 패키지 설치

```sh
$ cd snowdeer-user-api
$ poetry add "fastapi[standard]" uvicorn
$ poetry add --group dev ruff pytest
```

## 샘플 코드 작성

#### snowdeer_user_api/main.py

<pre class="prettyprint">
from fastapi import FastAPI

app = FastAPI(title="snowdeer's User API")

@app.get("/hello")
async def hello():
    return {"message": "Hello, snowdeer!"}
</pre>

```sh
# 아래 명령어로 실행
$ poetry run uvicorn snowdeer_user_api.main:app --reload

# 다른 터미널에서 확인
$ curl localhost:8000/hello

{"message":"Hello Snowdeer Users API!"}
```

## SQLAlchemy 활용 Users 테이블 생성

```sh
$ poetry add sqlalchemy pydantic-settings
```

#### snowdeer_user_api/database.py

<pre class="prettyprint">
from sqlalchemy import create_engine, Column, Integer, String
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import sessionmaker

SQLALCHEMY_DATABASE_URL = "sqlite:///./users.db"
engine = create_engine(SQLALCHEMY_DATABASE_URL, connect_args={"check_same_thread": False})
SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)
Base = declarative_base()

class User(Base):
    __tablename__ = "users"
    id = Column(Integer, primary_key=True, index=True)
    username = Column(String, unique=True, index=True)
    password = Column(String)
</pre>

#### snowdeer_user_api/main.py

<pre class="prettyprint">
from fastapi import FastAPI
from .database import engine, Base, SessionLocal

Base.metadata.create_all(bind=engine)

def get_db():
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()

app = FastAPI(title="snowdeer's User API")

@app.get("/hello")
async def hello():
    return {"message": "Hello, snowdeer!"}
</pre>

## 회원가입(Register) 및 로그인(Login) 엔드포인트 추가

#### snowdeer_user_api/schemas.py

<pre class="prettyprint">
from pydantic import BaseModel

class UserCreate(BaseModel):
    username: str
    password: str

class UserLogin(BaseModel):
    username: str
    password: str
</pre>

#### snowdeer_user_api/database.py

<pre class="prettyprint">
from sqlalchemy import create_engine, Column, Integer, String
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import sessionmaker

SQLALCHEMY_DATABASE_URL = "sqlite:///./users.db"
engine = create_engine(SQLALCHEMY_DATABASE_URL, connect_args={"check_same_thread": False})
SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)
Base = declarative_base()

class User(Base):
    __tablename__ = "users"
    id = Column(Integer, primary_key=True, index=True)
    username = Column(String, unique=True, index=True)
    password = Column(String)

Base.metadata.create_all(bind=engine)

def get_db():
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()
</pre>

#### snowdeer_user_api/main.py

<pre class="prettyprint">
from fastapi import FastAPI, Depends, HTTPException
from sqlalchemy.orm import Session
from .schemas import UserCreate, UserLogin
from .database import get_db, User


app = FastAPI(title="snowdeer's User API")

@app.get("/hello")
async def hello():
    return {"message": "Hello, snowdeer!"}

@app.post("/register")
async def register(user: UserCreate, db: Session = Depends(get_db)):
    db_user = db.query(User).filter(User.username == user.username).first()
    if db_user:
        raise HTTPException(400, "Username already exists")
    new_user = User(username=user.username, password=user.password)
    db.add(new_user)
    db.commit()
    return {"msg": "User created"}

@app.post("/login")
async def login(user: UserLogin, db: Session = Depends(get_db)):
    db_user = db.query(User).filter(User.username == user.username).first()
    if not db_user or db_user.password != user.password:
        raise HTTPException(401, "Invalid credentials")
    return {"msg": "Login successful"}
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

{"msg":"Login successful"}

# 잘못된 패스워드로 테스트
$ curl -X POST http://localhost:8000/login \
  -H "Content-Type: application/json" \
  -d '{"username": "snowdeer", "password": "wrong-password"}'

{"detail":"Invalid credentials"}
```

## SHA256 암호화 적용

#### snowdeer_user_api/utils.py

<pre class="prettyprint">
import hashlib

def hash_password(password: str) -> str:
    return hashlib.sha256(password.encode()).hexdigest()
</pre>

#### snowdeer_user_api/main.py

아래 부분을 수정

<pre class="prettyprint">
from fastapi import FastAPI, Depends, HTTPException
from sqlalchemy.orm import Session
from .schemas import UserCreate, UserLogin
from .database import get_db, User
from .utils import hash_password


app = FastAPI(title="snowdeer's User API")

@app.get("/hello")
async def hello():
    return {"message": "Hello, snowdeer!"}

@app.post("/register")
async def register(user: UserCreate, db: Session = Depends(get_db)):
    db_user = db.query(User).filter(User.username == user.username).first()
    if db_user:
        raise HTTPException(400, "Username already exists")
    new_user = User(username=user.username, password=hash_password(user.password))
    db.add(new_user)
    db.commit()
    return {"msg": "User created"}

@app.post("/login")
async def login(user: UserLogin, db: Session = Depends(get_db)):
    db_user = db.query(User).filter(User.username == user.username).first()
    if not db_user or db_user.password != hash_password(user.password):
        raise HTTPException(401, "Invalid credentials")
    return {"msg": "Login successful"}
</pre>

## 암호화에 Salt 적용

#### snowdeer_user_api/utils.py

<pre class="prettyprint">
import hashlib
import secrets

def generate_salt() -> str:
    return secrets.token_hex(16)

def hash_password(password: str, salt: str = None) -> tuple[str, str]:
    if not salt:
        salt = generate_salt()
    salted_password = f"{password}{salt}"
    hashed = hashlib.sha256(salted_password.encode()).hexdigest()
    return hashed, salt
</pre>

#### snowdeer_user_api/main.py

<pre class="prettyprint">
from fastapi import FastAPI, Depends, HTTPException
from sqlalchemy.orm import Session
from .schemas import UserCreate, UserLogin
from .database import get_db, User
from .utils import hash_password


app = FastAPI(title="snowdeer's User API")

@app.get("/hello")
async def hello():
    return {"message": "Hello, snowdeer!"}

@app.post("/register")
async def register(user: UserCreate, db: Session = Depends(get_db)):
    db_user = db.query(User).filter(User.username == user.username).first()
    if db_user:
        raise HTTPException(400, "Username already exists")
    
    hashed, salt = hash_password(user.password)
    new_user = User(username=user.username, password=f"{hashed}:{salt}")
    db.add(new_user)
    db.commit()
    return {"msg": "User created"}

@app.post("/login")
async def login(user: UserLogin, db: Session = Depends(get_db)):
    db_user = db.query(User).filter(User.username == user.username).first()
    
    if not db_user:
        raise HTTPException(401, "Invalid credentials")
    
    _, salt = db_user.password.split(":")
    hashed_input, _ = hash_password(user.password, salt)
    if hashed_input != db_user.password.split(":")[0]:
        raise HTTPException(401, "Invalid credentials")
    
    return {"msg": "Login successful"}
</pre>
