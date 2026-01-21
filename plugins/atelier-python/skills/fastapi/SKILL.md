---
name: python:fastapi
description: Building REST APIs with FastAPI, Pydantic validation, and OpenAPI. Use when creating routes, handling requests, designing endpoints, implementing validation, error responses, pagination, or generating API documentation.
user-invocable: false
---

# FastAPI - Modern Python Web APIs

FastAPI is a modern, fast web framework for building APIs with Python, using standard Python type hints.

## Quick Start

### Basic Application

```python
from fastapi import FastAPI
from pydantic import BaseModel

app = FastAPI(
    title="My API",
    description="API for my application",
    version="1.0.0",
)

class Item(BaseModel):
    name: str
    price: float

@app.get("/")
def read_root():
    return {"message": "Hello World"}

@app.get("/items/{item_id}")
def read_item(item_id: int, q: str | None = None):
    return {"item_id": item_id, "q": q}

@app.post("/items", response_model=Item)
def create_item(item: Item):
    return item
```

**Run with:**
```bash
uvicorn main:app --reload
```

## Request Models

Use Pydantic models for request validation:

```python
from pydantic import BaseModel, EmailStr, Field
from decimal import Decimal

class CreateUserRequest(BaseModel):
    email: EmailStr
    name: str = Field(min_length=1, max_length=100)
    age: int = Field(ge=18, le=120)

@app.post("/users")
def create_user(user: CreateUserRequest):
    """Request automatically validated"""
    return {"email": user.email, "name": user.name}
```

## Response Models

Define response schemas:

```python
from uuid import UUID
from pydantic import BaseModel

class UserResponse(BaseModel):
    id: UUID
    email: str
    name: str

    model_config = {"from_attributes": True}  # Enable ORM mode

@app.post("/users", response_model=UserResponse)
def create_user(user: CreateUserRequest, service: UserService):
    """Response automatically serialized"""
    user_entity = User.from_request(user)
    created_user = service.create(user_entity)
    return created_user  # Converted using model_config
```

## Path Parameters

```python
@app.get("/users/{user_id}")
def get_user(user_id: UUID):
    """Path parameter with type validation"""
    ...

@app.get("/products/{product_id}/reviews/{review_id}")
def get_review(product_id: int, review_id: int):
    """Multiple path parameters"""
    ...
```

## Query Parameters

```python
from typing import Literal

@app.get("/items")
def list_items(
    skip: int = 0,
    limit: int = 10,
    sort: Literal["asc", "desc"] = "asc",
    filter: str | None = None,
):
    """Query parameters with defaults and validation"""
    ...
```

## Request Body

```python
from pydantic import BaseModel

class Item(BaseModel):
    name: str
    description: str | None = None
    price: float
    tax: float | None = None

@app.post("/items")
def create_item(item: Item):
    """JSON body automatically parsed and validated"""
    total = item.price + (item.tax or 0)
    return {"item": item, "total": total}
```

## Router Organization

Split routes across multiple routers:

```python
# routers/users.py
from fastapi import APIRouter

router = APIRouter(
    prefix="/users",
    tags=["users"],
)

@router.get("/")
def list_users():
    ...

@router.post("/")
def create_user(user: CreateUserRequest):
    ...

# main.py
from fastapi import FastAPI
from routers import users

app = FastAPI()
app.include_router(users.router)
```

## Dependency Injection

FastAPI's core feature for managing dependencies:

```python
from fastapi import Depends
from sqlalchemy.orm import Session

def get_db() -> Session:
    """Database session dependency"""
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()

@app.get("/users")
def list_users(db: Session = Depends(get_db)):
    """db automatically injected"""
    return db.query(UserModel).all()
```

### Dependency Classes

```python
from fastapi import Depends, HTTPException, Header

class AuthService:
    def __init__(self, token: str = Header(alias="Authorization")):
        self.token = token

    def get_current_user(self) -> User:
        """Decode token and get user"""
        if not self.token:
            raise HTTPException(status_code=401)
        # Decode token...
        return user

@app.get("/me")
def get_current_user_profile(auth: AuthService = Depends()):
    """AuthService automatically injected"""
    user = auth.get_current_user()
    return user
```

See `references/dependencies.md` for advanced patterns.

## Validation

### Field Constraints

```python
from pydantic import BaseModel, Field, field_validator
from decimal import Decimal

class Product(BaseModel):
    name: str = Field(min_length=1, max_length=100)
    price: Decimal = Field(gt=0, decimal_places=2)
    stock: int = Field(ge=0)
    tags: list[str] = Field(max_length=10)

    @field_validator("tags")
    @classmethod
    def validate_tags(cls, v):
        if not v:
            raise ValueError("At least one tag required")
        return v
```

### Custom Validators

```python
from pydantic import BaseModel, field_validator, model_validator

class Order(BaseModel):
    items: list[str]
    discount: float

    @field_validator("discount")
    @classmethod
    def validate_discount(cls, v):
        if not 0 <= v <= 1:
            raise ValueError("Discount must be between 0 and 1")
        return v

    @model_validator(mode="after")
    def validate_discount_eligibility(self):
        if self.discount > 0 and len(self.items) < 2:
            raise ValueError("Discount requires at least 2 items")
        return self
```

See `references/validation.md` for detailed patterns.

## Error Handling

### HTTP Exceptions

```python
from fastapi import HTTPException

@app.get("/users/{user_id}")
def get_user(user_id: int):
    user = db.get(user_id)
    if not user:
        raise HTTPException(
            status_code=404,
            detail="User not found",
        )
    return user
```

### Custom Exception Handlers

```python
from fastapi import Request
from fastapi.responses import JSONResponse

class BusinessError(Exception):
    def __init__(self, message: str):
        self.message = message

@app.exception_handler(BusinessError)
async def business_error_handler(request: Request, exc: BusinessError):
    return JSONResponse(
        status_code=400,
        content={"error": exc.message},
    )

@app.get("/transfer")
def transfer(from_id: int, to_id: int, amount: float):
    if amount <= 0:
        raise BusinessError("Amount must be positive")
    ...
```

## Middleware

### Built-in Middleware

```python
from fastapi.middleware.cors import CORSMiddleware

app.add_middleware(
    CORSMiddleware,
    allow_origins=["*"],
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)
```

### Custom Middleware

```python
from starlette.middleware.base import BaseHTTPMiddleware
from time import time

class TimingMiddleware(BaseHTTPMiddleware):
    async def dispatch(self, request, call_next):
        start = time()
        response = await call_next(request)
        duration = time() - start
        response.headers["X-Process-Time"] = str(duration)
        return response

app.add_middleware(TimingMiddleware)
```

See `references/middleware.md` for advanced patterns.

## Lifespan Events

Handle startup and shutdown:

```python
from contextlib import asynccontextmanager

@asynccontextmanager
async def lifespan(app: FastAPI):
    """Runs at startup and shutdown"""
    # Startup
    print("Starting up...")
    db.connect()
    yield
    # Shutdown
    print("Shutting down...")
    db.disconnect()

app = FastAPI(lifespan=lifespan)
```

## Background Tasks

Run tasks after returning response:

```python
from fastapi import BackgroundTasks

def send_email(email: str, message: str):
    """Send email in background"""
    print(f"Sending email to {email}")

@app.post("/users")
def create_user(
    user: CreateUserRequest,
    background_tasks: BackgroundTasks,
):
    """Email sent after response"""
    user_id = save_user(user)
    background_tasks.add_task(send_email, user.email, "Welcome!")
    return {"id": user_id}
```

## File Uploads

```python
from fastapi import File, UploadFile

@app.post("/upload")
async def upload_file(file: UploadFile = File(...)):
    """Handle file upload"""
    contents = await file.read()
    return {
        "filename": file.filename,
        "content_type": file.content_type,
        "size": len(contents),
    }
```

## Status Codes

```python
from fastapi import status

@app.post("/users", status_code=status.HTTP_201_CREATED)
def create_user(user: CreateUserRequest):
    """Returns 201 Created"""
    ...

@app.delete("/users/{user_id}", status_code=status.HTTP_204_NO_CONTENT)
def delete_user(user_id: int):
    """Returns 204 No Content"""
    ...
```

## REST Resource Naming

Follow REST conventions:

```python
# Collections (plural nouns)
@app.get("/users")          # List users
@app.post("/users")         # Create user

# Resources (plural/id)
@app.get("/users/{id}")     # Get user
@app.put("/users/{id}")     # Update user (full)
@app.patch("/users/{id}")   # Update user (partial)
@app.delete("/users/{id}")  # Delete user

# Sub-resources
@app.get("/users/{user_id}/orders")  # User's orders
@app.get("/users/{user_id}/orders/{order_id}")  # Specific order

# Actions (use POST for non-CRUD)
@app.post("/users/{id}/activate")  # Activate user
@app.post("/orders/{id}/cancel")   # Cancel order
```

## Pagination

```python
from pydantic import BaseModel

class PaginatedResponse(BaseModel):
    items: list[UserResponse]
    total: int
    page: int
    page_size: int

@app.get("/users", response_model=PaginatedResponse)
def list_users(page: int = 1, page_size: int = 10):
    """Paginated response"""
    skip = (page - 1) * page_size
    users = db.query(User).offset(skip).limit(page_size).all()
    total = db.query(User).count()

    return PaginatedResponse(
        items=[UserResponse.model_validate(u) for u in users],
        total=total,
        page=page,
        page_size=page_size,
    )
```

## OpenAPI Customization

```python
app = FastAPI(
    title="My API",
    description="API for my application",
    version="1.0.0",
    openapi_tags=[
        {"name": "users", "description": "User operations"},
        {"name": "products", "description": "Product operations"},
    ],
)

@app.get("/users", tags=["users"], summary="List all users")
def list_users():
    """
    Retrieve a list of all users.

    This endpoint returns all users in the system with pagination support.
    """
    ...
```

## Testing

```python
from fastapi.testclient import TestClient

def test_create_user():
    """Test user creation"""
    client = TestClient(app)

    response = client.post(
        "/users",
        json={"email": "test@example.com", "name": "Test User", "age": 25},
    )

    assert response.status_code == 201
    data = response.json()
    assert data["email"] == "test@example.com"
```

## Project Structure

```
my-api/
├── main.py                   # FastAPI app
├── routers/                  # Route handlers
│   ├── __init__.py
│   ├── users.py
│   └── products.py
├── schemas/                  # Pydantic models
│   ├── __init__.py
│   ├── users.py
│   └── products.py
├── services/                 # Business logic
│   ├── __init__.py
│   └── users.py
├── repositories/             # Data access
│   ├── __init__.py
│   └── users.py
├── dependencies.py           # Dependency injection
└── middleware.py             # Custom middleware
```

## Best Practices

1. **Use response_model**: Define explicit response schemas
2. **Validate inputs**: Use Pydantic models with constraints
3. **Dependency injection**: Manage database sessions, auth, etc.
4. **Router organization**: Split routes by resource
5. **Error handling**: Use HTTP exceptions and custom handlers
6. **Type hints**: FastAPI uses them for validation and docs
7. **OpenAPI docs**: Customize with descriptions and tags

## Anti-Patterns

❌ **No response model**: Implicit response shape
❌ **Dict responses**: Use Pydantic models
❌ **Global state**: Use dependency injection
❌ **Mixed concerns**: Keep routers thin, logic in services
❌ **Ignoring validation**: Always validate inputs
❌ **Poor error messages**: Provide helpful error details

See references/ for dependency injection, middleware, validation patterns, and API design best practices.
