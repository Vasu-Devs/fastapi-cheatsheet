# fastapi-cheatsheet
📘 A comprehensive FastAPI Cheat Sheet (Markdown + PDF)





# 🚀 FastAPI Cheat Sheet

A **comprehensive FastAPI Cheat Sheet** covering setup, routing, request/response models, validation, security, middleware, database integration, testing, and more.  
Perfect for quick reference while building FastAPI applications.  

---

## 📦 Installation & Run
```bash
pip install fastapi uvicorn
uvicorn main:app --reload
````

```python
from fastapi import FastAPI
app = FastAPI()
```

---

## 📍 Routing

### Basic Routes

```python
@app.get("/")
def read_root():
    return {"msg": "Hello FastAPI!"}

@app.get("/items/{item_id}")
def read_item(item_id: int, q: str | None = None):
    return {"item_id": item_id, "q": q}

@app.post("/items/")
def create_item(item: dict):
    return {"item": item}
```

### Path & Query Params

```python
@app.get("/users/{user_id}")
def get_user(user_id: int, active: bool = True):
    return {"user_id": user_id, "active": active}
```

---

## 📦 Request & Response Models

### Pydantic Models

```python
from pydantic import BaseModel

class Item(BaseModel):
    name: str
    price: float
    in_stock: bool = True

@app.post("/items/")
def create_item(item: Item):
    return item
```

### Response Models

```python
from typing import List

@app.get("/items/", response_model=List[Item])
def list_items():
    return [Item(name="Pen", price=10.5)]
```

---

## ✅ Validation

```python
from fastapi import Query, Path

@app.get("/search/")
def search(
    q: str = Query(..., min_length=3, max_length=50),
    page: int = Query(1, ge=1),
):
    return {"q": q, "page": page}

@app.get("/users/{user_id}")
def get_user(user_id: int = Path(..., ge=1)):
    return {"user_id": user_id}
```

---

## ⚙️ Dependencies

```python
from fastapi import Depends

def common_params(q: str = "", skip: int = 0, limit: int = 10):
    return {"q": q, "skip": skip, "limit": limit}

@app.get("/items/")
def read_items(commons: dict = Depends(common_params)):
    return commons
```

---

## 🔐 Security

### OAuth2 with JWT

```python
from fastapi import Depends, HTTPException
from fastapi.security import OAuth2PasswordBearer

oauth2_scheme = OAuth2PasswordBearer(tokenUrl="token")

@app.get("/users/me")
def read_users_me(token: str = Depends(oauth2_scheme)):
    return {"token": token}
```

### API Key Security

```python
from fastapi.security.api_key import APIKeyHeader

api_key_header = APIKeyHeader(name="X-API-Key")

@app.get("/secure-data/")
def secure_data(api_key: str = Depends(api_key_header)):
    if api_key != "secret123":
        raise HTTPException(403, "Unauthorized")
    return {"data": "Top secret!"}
```

---

## 📤 File Upload & Forms

```python
from fastapi import File, UploadFile, Form

@app.post("/upload/")
def upload(file: UploadFile = File(...)):
    return {"filename": file.filename}

@app.post("/login/")
def login(username: str = Form(...), password: str = Form(...)):
    return {"username": username}
```

---

## 🍪 Cookies & Headers

```python
from fastapi import Cookie, Header

@app.get("/cookies/")
def get_cookie(session_id: str = Cookie(None)):
    return {"session_id": session_id}

@app.get("/headers/")
def get_header(user_agent: str = Header(None)):
    return {"user_agent": user_agent}
```

---

## 🔄 Background Tasks

```python
from fastapi import BackgroundTasks

def write_log(msg: str):
    with open("log.txt", "a") as f:
        f.write(msg + "\n")

@app.post("/log/")
def log_message(msg: str, bg: BackgroundTasks):
    bg.add_task(write_log, msg)
    return {"status": "scheduled"}
```

---

## 🧵 Middleware & Events

```python
from fastapi import Request

@app.middleware("http")
async def log_requests(request: Request, call_next):
    print(f"Incoming request: {request.url}")
    response = await call_next(request)
    return response

@app.on_event("startup")
async def startup_event():
    print("App started")

@app.on_event("shutdown")
async def shutdown_event():
    print("App shutting down")
```

---

## 🗄️ Database Example (SQLAlchemy)

```python
from sqlalchemy import create_engine, Column, Integer, String
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import sessionmaker

DATABASE_URL = "sqlite:///./test.db"

engine = create_engine(DATABASE_URL, connect_args={"check_same_thread": False})
SessionLocal = sessionmaker(bind=engine)
Base = declarative_base()

class User(Base):
    __tablename__ = "users"
    id = Column(Integer, primary_key=True, index=True)
    name = Column(String, index=True)

Base.metadata.create_all(bind=engine)
```

---

## 🧪 Testing

```python
from fastapi.testclient import TestClient

client = TestClient(app)

def test_root():
    response = client.get("/")
    assert response.status_code == 200
    assert response.json() == {"msg": "Hello FastAPI!"}
```

---

## 📊 Docs & CORS

* Swagger UI → `/docs`
* ReDoc → `/redoc`

```python
from fastapi.middleware.cors import CORSMiddleware

app.add_middleware(
    CORSMiddleware,
    allow_origins=["*"],
    allow_methods=["*"],
    allow_headers=["*"],
)
```

---

## ⚡ Performance Tips

* Use **async def** for I/O tasks.
* Use **response_model** to filter/validate outputs.
* Use **lifespan events** for DB connections.
* Heavy tasks → use **BackgroundTasks** or Celery.

---

## 📜 License

Released under the **MIT License**.
You are free to use, share, and modify with attribution.

---

👨‍💻 Author: [Vasudev Siddh](https://github.com/yourusername)
⭐ If this helped you, give it a star on GitHub!

```

---


```

