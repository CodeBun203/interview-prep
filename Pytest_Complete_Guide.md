# Pytest: Complete Guide
## From Basics to Advanced SDET Patterns

---

## Table of Contents
1. What pytest is and why it matters
2. Installation and project setup
3. Writing your first test
4. Assertions
5. Fixtures
6. Parametrize
7. Markers
8. Testing exceptions
9. conftest.py
10. Mocking with unittest.mock
11. Testing classes
12. Testing REST APIs
13. Testing async code
14. Coverage reporting
15. Test organization
16. Advanced patterns for SDET work

---

## 1. What pytest is and why it matters

pytest is Python's standard test framework. You write test functions, run `pytest`, and it finds and executes them automatically.

What makes pytest different from Python's built-in `unittest`:
- No need to inherit from a base class
- Uses plain `assert` statements instead of verbose `self.assertEqual`
- Powerful fixture system for managing test state
- Rich plugin ecosystem
- Clear, readable output with detailed failure messages

---

## 2. Installation and Project Setup

```bash
pip install pytest
pip install pytest-cov        # coverage reporting
pip install pytest-asyncio    # async test support
pip install requests-mock     # mock HTTP requests
pip install pytest-mock       # cleaner mock integration
```

**Recommended project structure:**
```
my_project/
├── app/
│   ├── __init__.py
│   ├── orders.py
│   └── users.py
├── tests/
│   ├── __init__.py
│   ├── conftest.py          # shared fixtures
│   ├── unit/
│   │   ├── test_orders.py
│   │   └── test_users.py
│   └── integration/
│       └── test_api.py
├── pytest.ini               # pytest configuration
└── requirements.txt
```

**pytest.ini (basic config):**
```ini
[pytest]
testpaths = tests
python_files = test_*.py
python_classes = Test*
python_functions = test_*
addopts = -v --tb=short
```

**Running tests:**
```bash
pytest                        # run all tests
pytest tests/unit/            # run a directory
pytest tests/unit/test_orders.py  # run one file
pytest tests/unit/test_orders.py::test_empty_cart  # run one test
pytest -k "discount"          # run tests whose names match "discount"
pytest -x                     # stop after first failure
pytest -v                     # verbose output
pytest -s                     # show print statements (no output capture)
```

---

## 3. Writing Your First Test

pytest discovers tests automatically. Rules:
- Test files must match `test_*.py` or `*_test.py`
- Test functions must start with `test_`
- Test classes must start with `Test` and contain no `__init__`

```python
# app/math_utils.py
def add(a, b):
    return a + b

def divide(a, b):
    if b == 0:
        raise ValueError("Cannot divide by zero")
    return a / b
```

```python
# tests/unit/test_math_utils.py
from app.math_utils import add, divide

def test_add_two_positive_numbers():
    assert add(2, 3) == 5

def test_add_negative_numbers():
    assert add(-1, -2) == -3

def test_add_zero():
    assert add(5, 0) == 5

def test_divide_basic():
    assert divide(10, 2) == 5.0
```

**Run it:**
```
$ pytest tests/unit/test_math_utils.py -v

tests/unit/test_math_utils.py::test_add_two_positive_numbers PASSED
tests/unit/test_math_utils.py::test_add_negative_numbers PASSED
tests/unit/test_math_utils.py::test_add_zero PASSED
tests/unit/test_math_utils.py::test_divide_basic PASSED

4 passed in 0.02s
```

**Test naming conventions:**
Good test names read like documentation. Use:
`test_[function]_[scenario]_[expected_outcome]`

Examples:
- `test_divide_by_zero_raises_value_error`
- `test_get_subscription_returns_none_when_user_not_found`
- `test_apply_discount_with_invalid_code_raises_error`

---

## 4. Assertions

pytest uses plain Python `assert`. When an assertion fails, pytest inspects the expression and prints a detailed diff.

```python
# Basic equality
assert result == expected

# Membership
assert "error" in response["status"]
assert item not in forbidden_items

# Boolean
assert is_valid
assert not has_error

# None checks
assert user is None
assert response is not None

# Type checks
assert isinstance(result, list)
assert isinstance(price, float)

# Length
assert len(results) == 3
assert len(errors) == 0

# Comparison
assert price > 0
assert retry_count <= max_retries
```

### Floating Point: pytest.approx

Never use `==` for floats. Use `pytest.approx`:

```python
import pytest

assert 0.1 + 0.2 == pytest.approx(0.3)
assert calculate_tax(9.99, 0.1) == pytest.approx(0.999)

# With relative tolerance (default is 1e-6)
assert result == pytest.approx(expected, rel=1e-3)

# With absolute tolerance
assert result == pytest.approx(expected, abs=0.01)

# Works on lists too
assert [0.1, 0.2] == pytest.approx([0.1, 0.2])
```

### Custom failure messages

```python
assert user["role"] == "admin", f"Expected admin role, got {user['role']!r}"
```

---

## 5. Fixtures

Fixtures are functions that provide setup data or state to tests. They are the most important concept in pytest.

### Basic fixture

```python
import pytest
from app.cart import ShoppingCart

@pytest.fixture
def empty_cart():
    return ShoppingCart()

@pytest.fixture
def cart_with_items():
    cart = ShoppingCart()
    cart.add({"id": "song_001", "price": 1.29})
    cart.add({"id": "song_002", "price": 0.99})
    return cart

def test_new_cart_is_empty(empty_cart):
    assert empty_cart.item_count == 0

def test_cart_total(cart_with_items):
    assert cart_with_items.total == pytest.approx(2.28)
```

Each test that requests `empty_cart` gets a fresh instance. Fixtures are not shared between tests by default.

### Fixture scope

Scope controls how often a fixture is created:

```python
@pytest.fixture(scope="function")   # default: new instance per test
@pytest.fixture(scope="class")      # one instance per test class
@pytest.fixture(scope="module")     # one instance per test file
@pytest.fixture(scope="session")    # one instance for the entire test run
```

Use `session` scope for expensive setup like database connections:

```python
@pytest.fixture(scope="session")
def db_connection():
    conn = create_database_connection()
    yield conn
    conn.close()
```

### Fixtures that yield (teardown)

Use `yield` to perform teardown after the test runs:

```python
@pytest.fixture
def temp_file(tmp_path):
    file_path = tmp_path / "test_data.json"
    file_path.write_text('{"key": "value"}')
    yield file_path
    # Code after yield runs after the test completes
    # tmp_path cleanup is automatic but you can add custom cleanup here

@pytest.fixture
def database():
    db = setup_test_database()
    yield db
    db.rollback()   # clean up any changes made during the test
    db.close()
```

### Fixtures can use other fixtures

```python
@pytest.fixture
def base_user():
    return {"id": "u_001", "name": "Hayden", "email": "h@test.com"}

@pytest.fixture
def premium_user(base_user):
    return {**base_user, "tier": "premium", "subscription_active": True}

def test_premium_features(premium_user):
    assert premium_user["tier"] == "premium"
```

### Built-in fixtures

pytest provides several built-in fixtures:

```python
def test_with_temp_dir(tmp_path):
    # tmp_path is a pathlib.Path to a temporary directory
    # automatically cleaned up after the test
    file = tmp_path / "data.txt"
    file.write_text("hello")
    assert file.read_text() == "hello"

def test_with_capsys(capsys):
    print("hello")
    captured = capsys.readouterr()
    assert captured.out == "hello\n"

def test_with_monkeypatch(monkeypatch):
    monkeypatch.setenv("API_KEY", "test_key_123")
    monkeypatch.setattr("app.config.DEBUG", True)
```

---

## 6. Parametrize

Run the same test with multiple input-output pairs. Eliminates copy-paste test functions.

### Basic parametrize

```python
import pytest
from app.validation import is_valid_email

@pytest.mark.parametrize("email,expected", [
    ("user@example.com", True),
    ("user@domain.co.uk", True),
    ("notanemail", False),
    ("missing@domain", False),
    ("@nodomain.com", False),
    ("", False),
    (None, False),
])
def test_email_validation(email, expected):
    assert is_valid_email(email) == expected
```

This generates 7 separate test cases from one function. Each has a unique name in the output.

### Parametrize with IDs

```python
@pytest.mark.parametrize("discount_code,expected_total", [
    pytest.param("SAVE10", 90.0, id="10_percent_off"),
    pytest.param("SAVE20", 80.0, id="20_percent_off"),
    pytest.param(None,     100.0, id="no_discount"),
], )
def test_discount_codes(discount_code, expected_total):
    items = [{"price": 100.0, "quantity": 1}]
    assert calculate_total(items, discount_code) == pytest.approx(expected_total)
```

### Parametrize on a class

```python
class TestPriceCalculation:
    @pytest.mark.parametrize("price,quantity,expected", [
        (10.0, 1, 10.0),
        (10.0, 3, 30.0),
        (9.99, 2, 19.98),
        (0.0,  5, 0.0),
    ])
    def test_line_total(self, price, quantity, expected):
        assert price * quantity == pytest.approx(expected)
```

### Combining parametrize decorators

```python
@pytest.mark.parametrize("region", ["US", "UK", "DE", "AU"])
@pytest.mark.parametrize("tier", ["basic", "premium"])
def test_subscription_valid_for_all_regions(region, tier):
    # Generates 4 * 2 = 8 test cases
    plan = f"{tier}-{region}-1M"
    assert is_valid_plan(plan)
```

---

## 7. Markers

Markers let you categorize and selectively run tests.

### Built-in markers

```python
import pytest

@pytest.mark.skip(reason="Not implemented yet")
def test_future_feature():
    pass

@pytest.mark.skipif(sys.platform == "win32", reason="Linux only")
def test_unix_only():
    pass

@pytest.mark.xfail(reason="Known bug in payment module, tracked in TICKET-123")
def test_known_bug():
    assert buggy_function() == expected  # expected to fail

@pytest.mark.xfail(strict=True)  # must fail — if it passes, the test fails
def test_must_fail():
    pass
```

### Custom markers

Define your own in `pytest.ini`:
```ini
[pytest]
markers =
    slow: marks tests as slow (run with -m slow)
    integration: marks integration tests requiring database
    smoke: marks critical path smoke tests
```

Use them:
```python
@pytest.mark.slow
def test_large_dataset_processing():
    ...

@pytest.mark.integration
def test_database_write():
    ...

@pytest.mark.smoke
def test_basic_purchase_flow():
    ...
```

Run selectively:
```bash
pytest -m smoke              # only smoke tests
pytest -m "not slow"         # everything except slow tests
pytest -m "integration and not slow"  # combine markers
```

---

## 8. Testing Exceptions

### pytest.raises basic usage

```python
import pytest
from app.orders import calculate_total

def test_empty_order_raises():
    with pytest.raises(ValueError):
        calculate_total([])
```

### Verify the error message

```python
def test_invalid_discount_error_message():
    with pytest.raises(ValueError, match="Unknown discount code"):
        calculate_total(items, "INVALID")

# match uses re.search, so it is a substring match by default
# Use ^ and $ for exact match:
with pytest.raises(ValueError, match="^Unknown discount code: INVALID$"):
    calculate_total(items, "INVALID")
```

### Inspect the exception object

```python
def test_exception_has_correct_attributes():
    with pytest.raises(InsufficientFundsError) as exc_info:
        account.withdraw(1000)

    assert exc_info.value.amount == 1000
    assert exc_info.value.balance < 1000
```

### Test that NO exception is raised

```python
def test_valid_input_does_not_raise():
    # If this raises, the test fails
    result = calculate_total([{"price": 9.99, "quantity": 1}])
    assert result == pytest.approx(9.99)
```

---

## 9. conftest.py

`conftest.py` is a special pytest file for sharing fixtures across multiple test files. pytest automatically discovers it without needing to import it.

### Structure

```
tests/
├── conftest.py          # fixtures available to ALL tests
├── unit/
│   ├── conftest.py      # fixtures available to unit tests only
│   └── test_orders.py
└── integration/
    ├── conftest.py      # fixtures available to integration tests only
    └── test_api.py
```

### Example conftest.py

```python
# tests/conftest.py
import pytest
from app.database import create_engine, get_session
from app.models import Base
from app.config import TestConfig

@pytest.fixture(scope="session")
def engine():
    engine = create_engine(TestConfig.DATABASE_URL)
    Base.metadata.create_all(engine)
    yield engine
    Base.metadata.drop_all(engine)

@pytest.fixture(scope="function")
def db_session(engine):
    connection = engine.connect()
    transaction = connection.begin()
    session = get_session(bind=connection)
    yield session
    session.close()
    transaction.rollback()   # undo all changes after each test
    connection.close()

@pytest.fixture
def sample_user(db_session):
    user = User(id="u_001", name="Test User", email="test@example.com")
    db_session.add(user)
    db_session.flush()
    return user

@pytest.fixture
def api_client(app):
    return app.test_client()
```

---

## 10. Mocking with unittest.mock

Mocking replaces real dependencies (database calls, HTTP requests, file system) with controlled substitutes so tests are fast, isolated, and deterministic.

### patch as a context manager

```python
from unittest.mock import patch, MagicMock
from app.notifications import send_purchase_confirmation

def test_send_confirmation_calls_email_service():
    with patch("app.notifications.email_client.send") as mock_send:
        send_purchase_confirmation("user@test.com", "ord_001")
        mock_send.assert_called_once_with(
            to="user@test.com",
            subject="Purchase Confirmed",
            body=unittest.mock.ANY,   # don't care about exact body content
        )
```

### patch as a decorator

```python
from unittest.mock import patch

@patch("app.orders.requests.post")
def test_order_notifies_warehouse(mock_post):
    mock_post.return_value.status_code = 200
    mock_post.return_value.json.return_value = {"status": "queued"}

    place_order({"item": "song", "price": 0.99})

    mock_post.assert_called_once()
```

### MagicMock

`MagicMock` automatically creates attributes and methods on access, making it easy to mock complex objects:

```python
from unittest.mock import MagicMock

mock_db = MagicMock()
mock_db.query.return_value.filter.return_value.first.return_value = None

result = get_user_by_email(mock_db, "unknown@test.com")
assert result is None
```

### Setting side effects

Use `side_effect` to make a mock raise an exception or return different values on successive calls:

```python
from unittest.mock import patch, MagicMock
import requests

# Raise an exception
mock_get = MagicMock()
mock_get.side_effect = requests.exceptions.ConnectionError("Connection refused")

# Return different values on successive calls
mock_get.side_effect = [
    MagicMock(status_code=500),   # first call fails
    MagicMock(status_code=500),   # second call fails
    MagicMock(status_code=200, json=lambda: {"data": "ok"}),  # third succeeds
]

# Use with retry logic test:
with patch("app.service.requests.get", side_effect=mock_get.side_effect):
    result = fetch_with_retry("https://api.internal/data", max_retries=3)
    assert result == {"data": "ok"}
```

### Verifying mock calls

```python
mock.assert_called()                          # called at least once
mock.assert_called_once()                     # called exactly once
mock.assert_called_with(arg1, kwarg=val)      # last call had these args
mock.assert_called_once_with(arg1, kwarg=val) # called exactly once with these args
mock.assert_any_call(arg1)                    # at least one call had these args
mock.assert_not_called()                      # never called

# Inspect call history
mock.call_count                               # number of times called
mock.call_args                                # args of the last call
mock.call_args_list                           # all calls as a list
```

### pytest-mock (cleaner syntax)

```python
# With pytest-mock, you get a `mocker` fixture instead of using patch manually

def test_purchase_sends_receipt(mocker):
    mock_send = mocker.patch("app.email.send_receipt")
    process_purchase(user_id="u_001", amount=9.99)
    mock_send.assert_called_once_with(user_id="u_001", amount=9.99)
```

---

## 11. Testing Classes

```python
# app/subscription.py
class Subscription:
    def __init__(self, user_id, plan):
        self.user_id = user_id
        self.plan = plan
        self.active = True
        self.history = []

    def cancel(self):
        if not self.active:
            raise ValueError("Subscription is already cancelled")
        self.active = False
        self.history.append("cancelled")

    def reactivate(self):
        if self.active:
            raise ValueError("Subscription is already active")
        self.active = True
        self.history.append("reactivated")
```

```python
# tests/unit/test_subscription.py
import pytest
from app.subscription import Subscription

class TestSubscription:
    @pytest.fixture
    def active_sub(self):
        return Subscription(user_id="u_001", plan="premium")

    @pytest.fixture
    def cancelled_sub(self, active_sub):
        active_sub.cancel()
        return active_sub

    def test_new_subscription_is_active(self, active_sub):
        assert active_sub.active is True

    def test_cancel_deactivates(self, active_sub):
        active_sub.cancel()
        assert active_sub.active is False

    def test_cancel_records_history(self, active_sub):
        active_sub.cancel()
        assert "cancelled" in active_sub.history

    def test_cancel_already_cancelled_raises(self, cancelled_sub):
        with pytest.raises(ValueError, match="already cancelled"):
            cancelled_sub.cancel()

    def test_reactivate_cancelled_subscription(self, cancelled_sub):
        cancelled_sub.reactivate()
        assert cancelled_sub.active is True

    def test_reactivate_active_subscription_raises(self, active_sub):
        with pytest.raises(ValueError, match="already active"):
            active_sub.reactivate()

    def test_cancel_then_reactivate_history(self, active_sub):
        active_sub.cancel()
        active_sub.reactivate()
        assert active_sub.history == ["cancelled", "reactivated"]
```

---

## 12. Testing REST APIs

### Testing a FastAPI application

```python
# app/main.py
from fastapi import FastAPI, HTTPException
from app.orders import get_order, create_order

app = FastAPI()

@app.get("/orders/{order_id}")
def read_order(order_id: str):
    order = get_order(order_id)
    if not order:
        raise HTTPException(status_code=404, detail="Order not found")
    return order

@app.post("/orders")
def write_order(order: dict):
    return create_order(order)
```

```python
# tests/integration/test_api.py
import pytest
from fastapi.testclient import TestClient
from unittest.mock import patch
from app.main import app

@pytest.fixture
def client():
    return TestClient(app)

def test_get_order_returns_200(client):
    with patch("app.main.get_order", return_value={"id": "ord_001", "status": "complete"}):
        response = client.get("/orders/ord_001")
    assert response.status_code == 200
    assert response.json()["id"] == "ord_001"

def test_get_order_not_found_returns_404(client):
    with patch("app.main.get_order", return_value=None):
        response = client.get("/orders/nonexistent")
    assert response.status_code == 404
    assert response.json()["detail"] == "Order not found"

def test_create_order_returns_201(client):
    payload = {"item_id": "song_001", "price": 1.29}
    with patch("app.main.create_order", return_value={"id": "ord_new", **payload}):
        response = client.post("/orders", json=payload)
    assert response.status_code == 200
    assert "id" in response.json()

def test_response_is_valid_json(client):
    with patch("app.main.get_order", return_value={"id": "ord_001"}):
        response = client.get("/orders/ord_001")
    # This will raise if the response is not valid JSON
    data = response.json()
    assert isinstance(data, dict)
```

### Mocking HTTP calls with requests-mock

```python
import requests
import requests_mock

from app.external_service import get_artist_info

def test_get_artist_info():
    with requests_mock.Mocker() as m:
        m.get(
            "https://api.internal/artists/artist_001",
            json={"id": "artist_001", "name": "Test Artist", "genre": "rock"},
            status_code=200
        )
        result = get_artist_info("artist_001")

    assert result["name"] == "Test Artist"

def test_get_artist_handles_timeout():
    with requests_mock.Mocker() as m:
        m.get(
            "https://api.internal/artists/artist_001",
            exc=requests.exceptions.Timeout
        )
        with pytest.raises(RuntimeError, match="timed out"):
            get_artist_info("artist_001")
```

---

## 13. Testing Async Code

```bash
pip install pytest-asyncio
```

```python
# pytest.ini
[pytest]
asyncio_mode = auto
```

```python
# app/async_service.py
import httpx

async def fetch_subscription(user_id: str) -> dict:
    async with httpx.AsyncClient() as client:
        response = await client.get(f"https://api.internal/users/{user_id}/subscription")
        response.raise_for_status()
        return response.json()
```

```python
# tests/unit/test_async_service.py
import pytest
from unittest.mock import AsyncMock, patch
from app.async_service import fetch_subscription

@pytest.mark.asyncio
async def test_fetch_subscription_success():
    mock_response = AsyncMock()
    mock_response.status_code = 200
    mock_response.json.return_value = {"status": "active", "plan": "premium"}
    mock_response.raise_for_status = AsyncMock()

    with patch("app.async_service.httpx.AsyncClient") as mock_client_class:
        mock_client = AsyncMock()
        mock_client.get.return_value = mock_response
        mock_client_class.return_value.__aenter__.return_value = mock_client

        result = await fetch_subscription("u_001")

    assert result["status"] == "active"
    assert result["plan"] == "premium"

@pytest.mark.asyncio
async def test_fetch_subscription_raises_on_error():
    with patch("app.async_service.httpx.AsyncClient") as mock_client_class:
        mock_client = AsyncMock()
        mock_client.get.side_effect = httpx.ConnectError("Connection refused")
        mock_client_class.return_value.__aenter__.return_value = mock_client

        with pytest.raises(httpx.ConnectError):
            await fetch_subscription("u_001")
```

---

## 14. Coverage Reporting

```bash
# Run tests with coverage
pytest --cov=app tests/

# Generate HTML report (open htmlcov/index.html in browser)
pytest --cov=app --cov-report=html tests/

# Show missing lines in terminal
pytest --cov=app --cov-report=term-missing tests/

# Fail if coverage drops below threshold
pytest --cov=app --cov-fail-under=80 tests/
```

**.coveragerc (coverage configuration):**
```ini
[run]
source = app
omit =
    app/migrations/*
    app/config.py

[report]
exclude_lines =
    pragma: no cover
    def __repr__
    raise NotImplementedError
```

**Mark lines to exclude from coverage:**
```python
def debug_only_function():  # pragma: no cover
    print("Only used in development")
```

---

## 15. Test Organization Best Practices

### One assertion concept per test
```python
# BAD: testing multiple things in one test
def test_order():
    order = create_order(items)
    assert order.total > 0
    assert order.status == "pending"
    assert order.user_id is not None
    assert len(order.items) == 2

# GOOD: one logical assertion per test
def test_order_has_positive_total(sample_order):
    assert sample_order.total > 0

def test_new_order_is_pending(sample_order):
    assert sample_order.status == "pending"

def test_order_assigned_to_user(sample_order, sample_user):
    assert sample_order.user_id == sample_user.id
```

### AAA Pattern: Arrange, Act, Assert
```python
def test_apply_discount():
    # Arrange
    items = [{"price": 100.0, "quantity": 1}]
    discount_code = "SAVE20"

    # Act
    result = calculate_total(items, discount_code)

    # Assert
    assert result == pytest.approx(80.0)
```

### Test names as documentation
Tests should be readable without looking at the code:
```python
def test_cancelled_subscription_cannot_be_cancelled_again():
    ...

def test_refund_is_denied_after_30_day_window():
    ...

def test_family_plan_allows_up_to_five_members():
    ...
```

### Avoid test interdependence
```python
# BAD: test_b depends on test_a having run first
cart = []

def test_a_add_item():
    cart.append("song")

def test_b_remove_item():
    cart.pop()   # fails if test_a did not run

# GOOD: each test is fully self-contained
@pytest.fixture
def cart():
    return []

def test_add_item(cart):
    cart.append("song")
    assert len(cart) == 1

def test_remove_item(cart):
    cart.append("song")
    cart.pop()
    assert len(cart) == 0
```

---

## 16. Advanced SDET Patterns

### Property-based testing with Hypothesis

Instead of writing specific test cases, define the properties that should always hold and let Hypothesis generate hundreds of test cases automatically.

```bash
pip install hypothesis
```

```python
from hypothesis import given, strategies as st
from app.math_utils import add

@given(st.integers(), st.integers())
def test_add_is_commutative(a, b):
    assert add(a, b) == add(b, a)

@given(st.integers())
def test_add_zero_is_identity(n):
    assert add(n, 0) == n

@given(st.floats(min_value=0, max_value=10000), st.floats(min_value=0, max_value=100))
def test_discount_never_increases_price(price, pct):
    result = apply_discount(price, pct)
    assert result <= price
```

### Contract testing

Verify that your service's API response matches what consumers expect:

```python
# tests/contract/test_purchase_api_contract.py
import pytest

REQUIRED_FIELDS = {"id", "status", "amount", "user_id", "created_at"}
VALID_STATUSES = {"pending", "complete", "failed", "refunded"}

def test_purchase_response_has_required_fields(client):
    response = client.post("/purchases", json=sample_purchase)
    data = response.json()
    assert REQUIRED_FIELDS.issubset(data.keys()), \
        f"Missing fields: {REQUIRED_FIELDS - data.keys()}"

def test_purchase_status_is_valid_value(client):
    response = client.post("/purchases", json=sample_purchase)
    data = response.json()
    assert data["status"] in VALID_STATUSES

def test_purchase_amount_is_positive_number(client):
    response = client.post("/purchases", json=sample_purchase)
    data = response.json()
    assert isinstance(data["amount"], (int, float))
    assert data["amount"] > 0
```

### Data-driven tests from files

```python
import json
import pytest
from pathlib import Path

TEST_DATA_DIR = Path(__file__).parent / "test_data"

def load_test_cases():
    with open(TEST_DATA_DIR / "validation_cases.json") as f:
        return json.load(f)

@pytest.mark.parametrize("case", load_test_cases())
def test_from_file(case):
    result = validate(case["input"])
    assert result == case["expected"], f"Failed on: {case['description']}"
```

### Snapshot testing

Save the first output as a "snapshot" and compare future outputs against it. Useful for API responses, rendered HTML, and report formats.

```bash
pip install syrupy
```

```python
def test_order_summary_format(snapshot, sample_order):
    result = format_order_summary(sample_order)
    assert result == snapshot
```

On first run, it saves the result. On future runs, it compares against the saved snapshot. Use `pytest --snapshot-update` to update snapshots when format changes are intentional.

### Performance assertions

```python
import time

def test_search_completes_within_threshold():
    start = time.monotonic()
    results = search_catalog(query="guitar", limit=100)
    elapsed = time.monotonic() - start

    assert elapsed < 0.5, f"Search took {elapsed:.3f}s, expected < 0.5s"
    assert len(results) > 0
```

### Testing CI pipeline locally

```bash
# Install act to run GitHub Actions locally
# https://github.com/nektos/act

act push                     # run push event
act -j unit-tests            # run specific job
act --secret-file .secrets   # load secrets from file
```

---

## Quick Reference

### Most important commands

```bash
pytest -v                     # verbose
pytest -x                     # stop on first failure
pytest -k "discount"          # filter by name
pytest -m smoke               # run by marker
pytest -s                     # show stdout
pytest --lf                   # run last failed tests only
pytest --tb=long              # full traceback on failure
pytest --cov=app --cov-report=html  # coverage with HTML report
```

### Most important fixtures to know

| Fixture | What it does |
|---|---|
| `tmp_path` | Temporary directory, auto-cleaned |
| `capsys` | Capture stdout/stderr |
| `monkeypatch` | Patch env vars, attributes, functions |
| `mocker` (pytest-mock) | Create mocks without patch context manager |
| `caplog` | Capture log output |
| `request` | Access test metadata from inside a fixture |

### Assertion cheat sheet

| What to test | How to test it |
|---|---|
| Equality | `assert a == b` |
| Float equality | `assert a == pytest.approx(b)` |
| None | `assert x is None` |
| Not None | `assert x is not None` |
| Membership | `assert x in collection` |
| Type | `assert isinstance(x, SomeClass)` |
| Exception raised | `with pytest.raises(SomeError):` |
| Exception message | `with pytest.raises(SomeError, match="text"):` |
| Dict subset | `assert expected.items() <= actual.items()` |
| List length | `assert len(lst) == n` |
| Empty | `assert not lst` |
