---
apply: always
---

# SQLModel usage

## 1. Basic Principles

- SQLModel is designed to simplify SQL database interactions in Python, especially with FastAPI applications [[1]](https://sqlmodel.tiangolo.com/)
- It combines the features of SQLAlchemy (for ORM) and Pydantic (for data validation) into a unified API [[1]](https://sqlmodel.tiangolo.com/)
- SQLModel uses Python type annotations for everything, providing excellent editor support with autocompletion and error checking [[2]](https://sqlmodel.tiangolo.com/tutorial/)

## 2. Defining Models

### Model Creation

```python
from sqlmodel import Field, SQLModel

class Hero(SQLModel, table=True):
    id: int | None = Field(default=None, primary_key=True)
    name: str
    secret_name: str
    age: int | None = None
```

- Use the `table=True` parameter when defining a class that should map to a database table [[1]](https://sqlmodel.tiangolo.com/)
- All fields with type annotations become columns in the database table
- Use `Field()` to customize column properties (like primary_key, default values, etc.)
- Use modern Python type hints (e.g., `int | None` instead of `Optional[int]`)

## 3. Database Setup

### Engine Creation

```python
from sqlmodel import create_engine

DATABASE_URL = "sqlite:///database.db"
engine = create_engine(DATABASE_URL, echo=True)
```

- Use `create_engine()` to establish a connection to your database
- The `echo=True` parameter enables SQL statement logging for debugging
- Put database connection code in a separate module to avoid circular imports

### Table Creation

```python
from sqlmodel import SQLModel

def create_db_and_tables():
    SQLModel.metadata.create_all(engine)
```

- Use `SQLModel.metadata.create_all(engine)` to create all tables defined in your models
- Wrap this in a function to control when tables are created and prevent accidental creation

## 4. CRUD Operations

### Creating Data

```python
from sqlmodel import Session

def create_hero(hero: Hero):
    with Session(engine) as session:
        session.add(hero)
        session.commit()
        session.refresh(hero)
        return hero
```

- Use the `Session` class to manage database transactions
- Always use a context manager (`with` statement) to ensure proper session handling
- Call `session.add()` to stage an object for insertion
- Call `session.commit()` to save changes to the database
- Use `session.refresh()` to update the object with any database-generated values

### Reading Data

```python
from sqlmodel import select

def get_hero(hero_id: int):
    with Session(engine) as session:
        statement = select(Hero).where(Hero.id == hero_id)
        hero = session.exec(statement).first()
        return hero

def get_all_heroes():
    with Session(engine) as session:
        statement = select(Hero)
        heroes = session.exec(statement).all()
        return heroes
```

- Use the `select()` function to create SQL SELECT statements
- Use `session.exec()` to execute the query and return a result set
- Use methods like `.first()`, `.one()`, or `.all()` to get results from the query

### Updating Data

```python
def update_hero(hero_id: int, hero_data: dict):
    with Session(engine) as session:
        hero = session.get(Hero, hero_id)
        if hero:
            for key, value in hero_data.items():
                setattr(hero, key, value)
            session.add(hero)
            session.commit()
            session.refresh(hero)
        return hero
```

- Use `session.get()` to retrieve an object by primary key
- Update object attributes and then call `session.add()` and `session.commit()`

### Deleting Data

```python
def delete_hero(hero_id: int):
    with Session(engine) as session:
        hero = session.get(Hero, hero_id)
        if hero:
            session.delete(hero)
            session.commit()
        return hero
```

- Use `session.delete()` to mark an object for deletion
- Always call `session.commit()` to finalize the deletion

## 5. Relationships

### One-to-Many Relationship

```python
class Team(SQLModel, table=True):
    id: int | None = Field(default=None, primary_key=True)
    name: str
    headquarters: str

    heroes: list["Hero"] = Relationship(back_populates="team")

class Hero(SQLModel, table=True):
    id: int | None = Field(default=None, primary_key=True)
    name: str
    secret_name: str
    age: int | None = None

    team_id: int | None = Field(default=None, foreign_key="team.id")
    team: Team | None = Relationship(back_populates="heroes")
```

- Define foreign keys using `Field(foreign_key="table.column")`
- Use `Relationship()` to establish bidirectional relationships between models
- Use `back_populates` to specify the attribute name on the related model

## 6. Integration with FastAPI

### Combining Table Models and API Models

```python
from fastapi import FastAPI, HTTPException
from sqlmodel import Session, select

app = FastAPI()

# Table Model
class HeroBase(SQLModel):
    name: str
    secret_name: str
    age: int | None = None

class Hero(HeroBase, table=True):
    id: int | None = Field(default=None, primary_key=True)

# API Models
class HeroCreate(HeroBase):
    pass

class HeroRead(HeroBase):
    id: int

@app.post("/heroes/", response_model=HeroRead)
def create_hero(hero: HeroCreate):
    db_hero = Hero.model_validate(hero.model_dump())
    with Session(engine) as session:
        session.add(db_hero)
        session.commit()
        session.refresh(db_hero)
        return db_hero
```

- Create a base model with common fields
- Derive table models with `table=True` and API-specific models
- Use different models for different operations (create, read, update)
- Use `model_validate` and `model_dump` for converting between model types

## 7. Best Practices

- Keep database models in separate modules to avoid circular imports
- Use dependency injection for database sessions in FastAPI
- Define a base model class for common fields and behavior
- Use type annotations consistently for better editor support
- Create separate models for API input/output vs. database tables
- Implement proper error handling and validation
- Use transactions for operations that require multiple database changes
- Always close sessions with context managers or try/finally blocks
- Use SQL indexing for fields that are frequently queried

## 8. Advanced Features

- Use async sessions for asynchronous database operations in FastAPI
- Implement database migrations using Alembic
- Use SQLModel's ability to combine with raw SQLAlchemy for complex queries
- Create composite models for handling many-to-many relationships
- Leverage Pydantic's validation features for input data validation
