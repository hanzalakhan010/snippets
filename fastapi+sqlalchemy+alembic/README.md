
## 1. Install the essentials

```bash
uv add fastapi uvicorn sqlalchemy psycopg2-binary alembic python-dotenv
```

### Optional but wise
```bash
uv add pydantic-settings
```


## 2. Project structure
```
server/
├── main.py
├── db/
│   ├── base.py
│   ├── session.py
│   └── models/
│       └── user.py
├── core/
│   └── config.py
```

## 3. Database config (Postgres URL)

```python

#core/config.py
from pathlib import Path
from pydantic_settings import BaseSettings

# .env is in the server/ directory (one level above core/)
ENV_FILE = Path(__file__).resolve().parent.parent / ".env"

class Settings(BaseSettings):
	DATABASE_URL: str
	GEMINI_API_KEY: str = ""
	MONGO_URI: str = ""

class Config:
	env_file = str(ENV_FILE)	
	extra = "ignore"

settings = Settings()
```

.env

```
DATABASE_URL=postgresql+psycopg2://user:password@localhost:5432/mydb
```

## 4. SQLAlchemy engine + session
```python
#db/session.py
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker
from app.core.config import settings

engine = create_engine(settings.DATABASE_URL, echo=True)
SessionLocal = sessionmaker(
	autocommit=False,
	autoflush=False,
	bind=engine,
)

def get_db():
	db = SessionLocal()
try:
	yield db
finally:
	db.close()
```

## 5. Declarative base (Alembic needs this)

```python
from sqlalchemy.orm import DeclarativeBase

class Base(DeclarativeBase):
    pass
```


## 6. Example model

```python
from db.base import Base
from sqlalchemy import String
from sqlalchemy.orm import Mapped, mapped_column

class User(Base):
	__tablename__ = "users"
	id: Mapped[int] = mapped_column(primary_key=True)
	email: Mapped[str] = mapped_column(String, unique=True, index=True)
```


## 7. FastAPI entry point

```python

from fastapi import FastAPI
from app.db.models import user  # ensure model import

app = FastAPI()

@app.get("/")
def root():
    return {"status": "alive"}

```

## 8. Alembic setup

```bash
alembic init alembic
```
### Update `alembic/env.py`
```python

from app.db.base import Base
from app.core.config import settings
from app.db.models import user  # import models

target_metadata = Base.metadata
config.set_main_option("sqlalchemy.url", settings.DATABASE_URL)

```

## 9. Create & apply migrations

```bash
alembic revision --autogenerate -m "create users table"
alembic upgrade head

```
