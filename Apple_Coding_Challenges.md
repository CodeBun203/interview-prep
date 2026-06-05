# Coding Challenges — Apple SDET Practice
## Aligned to ASE Media Platform Quality Role

Each challenge includes a problem statement, examples, constraints, and a solution. Try to solve each one before reading the solution.

Recommended language: Python 3

---

## Section 1: Data Transformation (Core SDET Skill)

---

### Challenge 01 — Parse Purchase Logs

**Difficulty:** Easy

**Problem:**
You receive raw log entries from the App Store purchase system. Each line is formatted as:

```
TIMESTAMP | LEVEL | USER_ID | MESSAGE
```

Write a function that parses a list of raw log strings into a list of structured dictionaries. Skip any lines that do not match the expected format.

**Input:**
```python
logs = [
    "2025-06-01T10:00:00 | INFO | user_123 | Purchase completed",
    "2025-06-01T10:01:00 | ERROR | user_456 | Payment declined",
    "malformed line",
    "2025-06-01T10:02:00 | INFO | user_789 | Subscription renewed",
]
```

**Expected Output:**
```python
[
    {"timestamp": "2025-06-01T10:00:00", "level": "INFO", "user_id": "user_123", "message": "Purchase completed"},
    {"timestamp": "2025-06-01T10:01:00", "level": "ERROR", "user_id": "user_456", "message": "Payment declined"},
    {"timestamp": "2025-06-01T10:02:00", "level": "INFO", "user_id": "user_789", "message": "Subscription renewed"},
]
```

**Constraints:**
- Skip any line that does not have exactly 4 pipe-separated parts
- Strip whitespace from all values
- Do not raise exceptions for malformed lines

<details>
<summary>Solution</summary>

```python
def parse_logs(logs):
    records = []
    for line in logs:
        parts = line.split(" | ")
        if len(parts) != 4:
            continue
        records.append({
            "timestamp": parts[0].strip(),
            "level": parts[1].strip(),
            "user_id": parts[2].strip(),
            "message": parts[3].strip(),
        })
    return records
```

**What to talk through in an interview:**
- Why `len(parts) != 4` is the right guard (not try/except)
- Why you strip whitespace even though the format looks clean (real data is never clean)
- That you are not raising exceptions, just skipping, as specified

</details>

---

### Challenge 02 — Flatten Nested Purchase Data

**Difficulty:** Easy-Medium

**Problem:**
An internal API returns nested purchase data. You need to flatten it into a list of individual item records, each containing the order-level metadata.

**Input:**
```python
orders = [
    {
        "order_id": "ord_001",
        "user_id": "user_123",
        "items": [
            {"item_id": "song_a", "price": 1.29},
            {"item_id": "song_b", "price": 0.99},
        ]
    },
    {
        "order_id": "ord_002",
        "user_id": "user_456",
        "items": [
            {"item_id": "album_x", "price": 9.99},
        ]
    }
]
```

**Expected Output:**
```python
[
    {"order_id": "ord_001", "user_id": "user_123", "item_id": "song_a", "price": 1.29},
    {"order_id": "ord_001", "user_id": "user_123", "item_id": "song_b", "price": 0.99},
    {"order_id": "ord_002", "user_id": "user_456", "item_id": "album_x", "price": 9.99},
]
```

<details>
<summary>Solution</summary>

```python
def flatten_orders(orders):
    records = []
    for order in orders:
        for item in order.get("items", []):
            records.append({
                "order_id": order["order_id"],
                "user_id": order["user_id"],
                "item_id": item["item_id"],
                "price": item["price"],
            })
    return records

# One-liner with list comprehension:
def flatten_orders(orders):
    return [
        {"order_id": o["order_id"], "user_id": o["user_id"], **item}
        for o in orders
        for item in o.get("items", [])
    ]
```

**Note on `**item`:** The double-star unpacks the item dict into the new dict. Only use this if you are sure `item` does not have keys that would overwrite `order_id` or `user_id`.

</details>

---

### Challenge 03 — Group Transactions by Region

**Difficulty:** Medium

**Problem:**
Given a list of transaction records, group them by their storefront region and return a dict where each key is a region and each value is a list of transaction IDs for that region.

**Input:**
```python
transactions = [
    {"id": "txn_001", "region": "US", "amount": 9.99},
    {"id": "txn_002", "region": "UK", "amount": 7.99},
    {"id": "txn_003", "region": "US", "amount": 1.29},
    {"id": "txn_004", "region": "DE", "amount": 4.99},
    {"id": "txn_005", "region": "UK", "amount": 0.99},
]
```

**Expected Output:**
```python
{
    "US": ["txn_001", "txn_003"],
    "UK": ["txn_002", "txn_005"],
    "DE": ["txn_004"],
}
```

<details>
<summary>Solution</summary>

```python
from collections import defaultdict

def group_by_region(transactions):
    groups = defaultdict(list)
    for txn in transactions:
        groups[txn["region"]].append(txn["id"])
    return dict(groups)

# Without defaultdict:
def group_by_region(transactions):
    groups = {}
    for txn in transactions:
        region = txn["region"]
        if region not in groups:
            groups[region] = []
        groups[region].append(txn["id"])
    return groups
```

</details>

---

### Challenge 04 — Normalize API Response

**Difficulty:** Medium

**Problem:**
Two internal services return user data in different formats. Write a function that normalizes both formats into a standard format.

**Format A (identity service):**
```python
{"userId": "u_123", "fullName": "Hayden NeSmith", "emailAddress": "h@example.com"}
```

**Format B (legacy service):**
```python
{"user_id": "u_456", "first_name": "Alice", "last_name": "Smith", "email": "a@example.com"}
```

**Target format:**
```python
{"id": "u_123", "name": "Hayden NeSmith", "email": "h@example.com"}
```

Write a function that accepts either format and returns the target format.

<details>
<summary>Solution</summary>

```python
def normalize_user(user):
    # Format A uses camelCase with fullName
    if "userId" in user:
        return {
            "id": user["userId"],
            "name": user["fullName"],
            "email": user["emailAddress"],
        }
    # Format B uses snake_case with first/last name
    elif "user_id" in user:
        return {
            "id": user["user_id"],
            "name": f"{user['first_name']} {user['last_name']}",
            "email": user["email"],
        }
    else:
        raise ValueError(f"Unknown user format: {user.keys()}")
```

**What to talk through:**
- Detecting format by checking for a key that is unique to each format
- Raising a descriptive error for unknown formats rather than returning None silently
- The f-string for combining first and last name

</details>

---

## Section 2: String Processing

---

### Challenge 05 — Redact Sensitive Data in Logs

**Difficulty:** Easy-Medium

**Problem:**
Apple's internal logging pipeline must redact credit card numbers and email addresses from log strings before storing them. Write a function that replaces them with `[REDACTED]`.

A credit card number matches: exactly 16 digits, possibly separated by spaces or dashes in groups of 4.
An email matches: standard format `user@domain.extension`.

**Examples:**
```python
redact("User 4111111111111111 purchased a song")
# "User [REDACTED] purchased a song"

redact("Contact user@apple.com for support")
# "Contact [REDACTED] for support"

redact("Card 4111-1111-1111-1111 used by user@apple.com")
# "Card [REDACTED] used by [REDACTED]"
```

<details>
<summary>Solution</summary>

```python
import re

def redact(text):
    # Credit card: 16 digits optionally grouped by spaces or dashes
    text = re.sub(r'\b\d{4}[\s-]?\d{4}[\s-]?\d{4}[\s-]?\d{4}\b', '[REDACTED]', text)
    # Email address
    text = re.sub(r'\b[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Z|a-z]{2,}\b', '[REDACTED]', text)
    return text
```

**Key regex concepts:**
- `\b` — word boundary, prevents matching digits in the middle of longer numbers
- `[\s-]?` — optionally match a space or dash between digit groups
- `[A-Za-z0-9._%+-]+` — one or more valid email local-part characters
- `{2,}` — two or more characters for the TLD

</details>

---

### Challenge 06 — Find Duplicate Transaction IDs

**Difficulty:** Easy

**Problem:**
Given a list of transaction IDs, return a list of IDs that appear more than once. The result should be sorted alphabetically.

**Input:**
```python
ids = ["txn_001", "txn_002", "txn_001", "txn_003", "txn_002", "txn_004"]
```

**Expected Output:**
```python
["txn_001", "txn_002"]
```

<details>
<summary>Solution</summary>

```python
from collections import Counter

def find_duplicates(ids):
    counts = Counter(ids)
    return sorted(id for id, count in counts.items() if count > 1)

# Without Counter:
def find_duplicates(ids):
    seen = set()
    duplicates = set()
    for id in ids:
        if id in seen:
            duplicates.add(id)
        seen.add(id)
    return sorted(duplicates)
```

</details>

---

### Challenge 07 — Validate Subscription Plan Codes

**Difficulty:** Easy-Medium

**Problem:**
Subscription plan codes must follow the format: `[TIER]-[REGION]-[DURATION]`

Where:
- TIER is one of: `FREE`, `BASIC`, `PREMIUM`, `FAMILY`
- REGION is a 2-letter uppercase country code
- DURATION is `1M`, `3M`, `6M`, or `1Y`

Write a function that validates a plan code and returns `True` or `False`.

**Examples:**
```python
is_valid_plan("PREMIUM-US-1Y")   # True
is_valid_plan("BASIC-UK-3M")     # True
is_valid_plan("VIP-US-1Y")       # False (VIP is not a valid tier)
is_valid_plan("PREMIUM-USA-1Y")  # False (USA is 3 letters)
is_valid_plan("PREMIUM-US-2M")   # False (2M is not a valid duration)
is_valid_plan("premium-us-1y")   # False (must be uppercase)
```

<details>
<summary>Solution</summary>

```python
import re

VALID_TIERS = {"FREE", "BASIC", "PREMIUM", "FAMILY"}
VALID_DURATIONS = {"1M", "3M", "6M", "1Y"}

def is_valid_plan(code):
    parts = code.split("-")
    if len(parts) != 3:
        return False
    tier, region, duration = parts
    if tier not in VALID_TIERS:
        return False
    if not re.match(r'^[A-Z]{2}$', region):
        return False
    if duration not in VALID_DURATIONS:
        return False
    return True

# More concise with regex:
def is_valid_plan(code):
    pattern = r'^(FREE|BASIC|PREMIUM|FAMILY)-[A-Z]{2}-(1M|3M|6M|1Y)$'
    return bool(re.match(pattern, code))
```

</details>

---

## Section 3: Algorithms

---

### Challenge 08 — Most Frequent Purchase Category

**Difficulty:** Easy

**Problem:**
Given a list of purchases, return the category that appears most frequently. If there is a tie, return the category that comes first alphabetically.

**Input:**
```python
purchases = [
    {"category": "music"},
    {"category": "apps"},
    {"category": "music"},
    {"category": "books"},
    {"category": "apps"},
    {"category": "music"},
]
```

**Expected Output:**
```python
"music"
```

<details>
<summary>Solution</summary>

```python
from collections import Counter

def most_frequent_category(purchases):
    if not purchases:
        return None
    counts = Counter(p["category"] for p in purchases)
    max_count = max(counts.values())
    # Get all categories with the max count, sort alphabetically, take first
    return min(
        (cat for cat, count in counts.items() if count == max_count)
    )
```

**Why `min()` for alphabetical tiebreaker:** `min()` on strings returns the alphabetically smallest string. This handles the tiebreaker requirement without sorting the whole list.

</details>

---

### Challenge 09 — Merge Overlapping Maintenance Windows

**Difficulty:** Medium

**Problem:**
Your team schedules maintenance windows as `[start, end]` pairs. Write a function that merges all overlapping windows and returns the minimal list of non-overlapping windows. Windows are given as minute offsets from midnight.

**Input:**
```python
windows = [[60, 120], [90, 150], [200, 300], [250, 320], [400, 500]]
```

**Expected Output:**
```python
[[60, 150], [200, 320], [400, 500]]
```

<details>
<summary>Solution</summary>

```python
def merge_windows(windows):
    if not windows:
        return []

    windows.sort(key=lambda w: w[0])
    merged = [windows[0]]

    for start, end in windows[1:]:
        if start <= merged[-1][1]:
            merged[-1][1] = max(merged[-1][1], end)
        else:
            merged.append([start, end])

    return merged
```

**Walk through:**
1. Sort by start time
2. Take the first window as the current merge candidate
3. For each subsequent window: if it overlaps with the last merged window (start <= last end), extend the last end if needed. Otherwise, start a new window.

</details>

---

### Challenge 10 — Rate Limiter

**Difficulty:** Medium

**Problem:**
Implement a simple rate limiter that allows at most `max_calls` calls within a rolling `window_seconds` window per user.

```python
limiter = RateLimiter(max_calls=3, window_seconds=60)
limiter.is_allowed("user_123", timestamp=0)    # True
limiter.is_allowed("user_123", timestamp=10)   # True
limiter.is_allowed("user_123", timestamp=20)   # True
limiter.is_allowed("user_123", timestamp=30)   # False (3 calls in window)
limiter.is_allowed("user_123", timestamp=61)   # True (first call at t=0 expired)
```

<details>
<summary>Solution</summary>

```python
from collections import defaultdict, deque

class RateLimiter:
    def __init__(self, max_calls, window_seconds):
        self.max_calls = max_calls
        self.window_seconds = window_seconds
        self.call_times = defaultdict(deque)  # user_id -> deque of timestamps

    def is_allowed(self, user_id, timestamp):
        times = self.call_times[user_id]

        # Remove timestamps outside the rolling window
        while times and times[0] <= timestamp - self.window_seconds:
            times.popleft()

        if len(times) < self.max_calls:
            times.append(timestamp)
            return True

        return False
```

**Why a deque:** `popleft()` is O(1) on a deque but O(n) on a list. For a high-throughput service like the App Store, this matters.

</details>

---

### Challenge 11 — Find the Missing Transaction ID

**Difficulty:** Easy-Medium

**Problem:**
Transaction IDs are assigned sequentially from 1 to N. You receive a list of IDs but one is missing. Find the missing one.

**Input:**
```python
ids = [1, 2, 4, 5, 6]   # N = 6, missing: 3
```

**Expected Output:**
```python
3
```

**Constraints:** Solve it in O(n) time and O(1) space.

<details>
<summary>Solution</summary>

```python
def find_missing(ids):
    n = len(ids) + 1   # there should be n+1 numbers total
    expected_sum = n * (n + 1) // 2
    return expected_sum - sum(ids)
```

**Why this works:** The sum of integers from 1 to N is `N*(N+1)/2`. Subtract the actual sum from the expected sum to get the missing value. No extra data structures needed.

</details>

---

## Section 4: API and Commerce Logic

---

### Challenge 12 — Retry with Exponential Backoff

**Difficulty:** Medium

**Problem:**
Write a function `retry_request(func, max_retries, base_delay)` that:
- Calls `func()`
- If it raises an exception, retries up to `max_retries` times
- Waits `base_delay * 2^attempt` seconds between retries
- Raises the last exception if all retries are exhausted

```python
def flaky_api():
    # Simulates a call that fails twice then succeeds
    ...

result = retry_request(flaky_api, max_retries=3, base_delay=1)
```

<details>
<summary>Solution</summary>

```python
import time

def retry_request(func, max_retries, base_delay):
    last_exception = None
    for attempt in range(max_retries + 1):
        try:
            return func()
        except Exception as e:
            last_exception = e
            if attempt < max_retries:
                wait = base_delay * (2 ** attempt)
                time.sleep(wait)
    raise last_exception
```

**Walk through:**
- Attempt 0: try immediately, if fails wait `base_delay * 1`
- Attempt 1: if fails wait `base_delay * 2`
- Attempt 2: if fails wait `base_delay * 4`
- Attempt 3 (max_retries): if fails, raise the last exception
- Total attempts: `max_retries + 1` (the original call plus retries)

</details>

---

### Challenge 13 — Calculate Subscription Renewal Dates

**Difficulty:** Easy-Medium

**Problem:**
Given a subscription start date and plan type, return the next renewal date.

Plan types: `"monthly"` (30 days), `"quarterly"` (90 days), `"annual"` (365 days)

```python
from datetime import date

get_renewal_date(date(2025, 1, 15), "monthly")    # date(2025, 2, 14)
get_renewal_date(date(2025, 1, 15), "quarterly")  # date(2025, 4, 15)
get_renewal_date(date(2025, 1, 15), "annual")     # date(2026, 1, 15)
```

<details>
<summary>Solution</summary>

```python
from datetime import date, timedelta

PLAN_DAYS = {
    "monthly": 30,
    "quarterly": 90,
    "annual": 365,
}

def get_renewal_date(start_date, plan):
    days = PLAN_DAYS.get(plan)
    if days is None:
        raise ValueError(f"Unknown plan type: {plan}")
    return start_date + timedelta(days=days)
```

</details>

---

### Challenge 14 — Detect Anomalous Transactions

**Difficulty:** Medium

**Problem:**
A transaction is anomalous if its amount is more than 2 standard deviations above the mean of all transactions for that user. Return a list of anomalous transaction IDs.

**Input:**
```python
transactions = [
    {"id": "t1", "user": "u1", "amount": 10},
    {"id": "t2", "user": "u1", "amount": 12},
    {"id": "t3", "user": "u1", "amount": 11},
    {"id": "t4", "user": "u1", "amount": 95},  # anomalous
    {"id": "t5", "user": "u2", "amount": 100},
    {"id": "t6", "user": "u2", "amount": 105},
]
```

<details>
<summary>Solution</summary>

```python
import math
from collections import defaultdict

def find_anomalies(transactions):
    # Group amounts by user
    user_amounts = defaultdict(list)
    for txn in transactions:
        user_amounts[txn["user"]].append(txn["amount"])

    # Compute mean and std per user
    def mean(values):
        return sum(values) / len(values)

    def std(values):
        avg = mean(values)
        variance = sum((v - avg) ** 2 for v in values) / len(values)
        return math.sqrt(variance)

    user_stats = {}
    for user, amounts in user_amounts.items():
        avg = mean(amounts)
        sd = std(amounts)
        user_stats[user] = (avg, sd)

    # Find anomalies
    anomalies = []
    for txn in transactions:
        avg, sd = user_stats[txn["user"]]
        if sd > 0 and txn["amount"] > avg + 2 * sd:
            anomalies.append(txn["id"])

    return anomalies
```

</details>

---

## Section 5: SDET-Specific Challenges

---

### Challenge 15 — Write Tests for a Purchase Function

**Difficulty:** Medium

**Problem:**
You are given the following function. Write a complete pytest test suite for it. Cover happy paths, edge cases, and error conditions.

```python
def calculate_order_total(items, discount_code=None):
    """
    Calculate the total price of an order.

    items: list of dicts with 'price' (float) and 'quantity' (int)
    discount_code: optional string. "SAVE10" = 10% off, "SAVE20" = 20% off
    Returns: float total after discount
    Raises: ValueError if items is empty or contains invalid data
    Raises: ValueError if discount_code is unrecognized
    """
    if not items:
        raise ValueError("Order must contain at least one item")

    subtotal = 0
    for item in items:
        if item["price"] < 0:
            raise ValueError(f"Price cannot be negative: {item['price']}")
        if item["quantity"] < 1:
            raise ValueError(f"Quantity must be at least 1: {item['quantity']}")
        subtotal += item["price"] * item["quantity"]

    discount = 0
    if discount_code == "SAVE10":
        discount = 0.10
    elif discount_code == "SAVE20":
        discount = 0.20
    elif discount_code is not None:
        raise ValueError(f"Unknown discount code: {discount_code}")

    return subtotal * (1 - discount)
```

<details>
<summary>Solution — Full Test Suite</summary>

```python
import pytest
from order import calculate_order_total


class TestHappyPath:
    def test_single_item(self):
        items = [{"price": 9.99, "quantity": 1}]
        assert calculate_order_total(items) == pytest.approx(9.99)

    def test_multiple_items(self):
        items = [
            {"price": 1.29, "quantity": 2},
            {"price": 9.99, "quantity": 1},
        ]
        assert calculate_order_total(items) == pytest.approx(12.57)

    def test_no_discount_code(self):
        items = [{"price": 100.0, "quantity": 1}]
        assert calculate_order_total(items) == pytest.approx(100.0)

    def test_discount_save10(self):
        items = [{"price": 100.0, "quantity": 1}]
        assert calculate_order_total(items, "SAVE10") == pytest.approx(90.0)

    def test_discount_save20(self):
        items = [{"price": 100.0, "quantity": 1}]
        assert calculate_order_total(items, "SAVE20") == pytest.approx(80.0)

    def test_quantity_greater_than_one(self):
        items = [{"price": 5.00, "quantity": 4}]
        assert calculate_order_total(items) == pytest.approx(20.0)

    def test_discount_applied_to_full_subtotal(self):
        items = [
            {"price": 10.0, "quantity": 2},
            {"price": 5.0, "quantity": 2},
        ]
        assert calculate_order_total(items, "SAVE10") == pytest.approx(27.0)


class TestEdgeCases:
    def test_price_is_zero(self):
        items = [{"price": 0.0, "quantity": 1}]
        assert calculate_order_total(items) == pytest.approx(0.0)

    def test_large_quantity(self):
        items = [{"price": 0.99, "quantity": 1000}]
        assert calculate_order_total(items) == pytest.approx(990.0)

    def test_float_precision(self):
        items = [{"price": 0.1, "quantity": 3}]
        assert calculate_order_total(items) == pytest.approx(0.3)

    def test_discount_code_none_explicit(self):
        items = [{"price": 50.0, "quantity": 1}]
        assert calculate_order_total(items, discount_code=None) == pytest.approx(50.0)


class TestErrorConditions:
    def test_empty_items_raises(self):
        with pytest.raises(ValueError, match="at least one item"):
            calculate_order_total([])

    def test_negative_price_raises(self):
        items = [{"price": -5.0, "quantity": 1}]
        with pytest.raises(ValueError, match="negative"):
            calculate_order_total(items)

    def test_zero_quantity_raises(self):
        items = [{"price": 9.99, "quantity": 0}]
        with pytest.raises(ValueError, match="Quantity"):
            calculate_order_total(items)

    def test_unknown_discount_code_raises(self):
        items = [{"price": 9.99, "quantity": 1}]
        with pytest.raises(ValueError, match="Unknown discount code"):
            calculate_order_total(items, "SAVE50")

    def test_empty_string_discount_raises(self):
        items = [{"price": 9.99, "quantity": 1}]
        with pytest.raises(ValueError, match="Unknown discount code"):
            calculate_order_total(items, "")


class TestParametrized:
    @pytest.mark.parametrize("code,expected", [
        (None, 100.0),
        ("SAVE10", 90.0),
        ("SAVE20", 80.0),
    ])
    def test_discount_codes(self, code, expected):
        items = [{"price": 100.0, "quantity": 1}]
        assert calculate_order_total(items, code) == pytest.approx(expected)
```

**What makes this test suite complete:**
- Organized into clear classes by category
- Every code path in the function is exercised
- All error messages are verified with `match=`
- Floating point comparisons use `pytest.approx`
- Parametrized test reduces repetition for discount code variants

</details>

---

### Challenge 16 — Mock an External API Call

**Difficulty:** Medium

**Problem:**
Write tests for the following function without making real HTTP calls. Use `unittest.mock` to mock the requests library.

```python
import requests

def get_app_rating(app_id):
    response = requests.get(f"https://api.internal/apps/{app_id}/rating")
    if response.status_code == 404:
        return None
    response.raise_for_status()
    return response.json()["rating"]
```

<details>
<summary>Solution</summary>

```python
import pytest
from unittest.mock import patch, MagicMock
from app_service import get_app_rating


def test_returns_rating_on_success():
    mock_response = MagicMock()
    mock_response.status_code = 200
    mock_response.json.return_value = {"rating": 4.5}

    with patch("app_service.requests.get", return_value=mock_response):
        result = get_app_rating("com.apple.music")

    assert result == 4.5


def test_returns_none_on_404():
    mock_response = MagicMock()
    mock_response.status_code = 404

    with patch("app_service.requests.get", return_value=mock_response):
        result = get_app_rating("com.nonexistent.app")

    assert result is None


def test_raises_on_server_error():
    mock_response = MagicMock()
    mock_response.status_code = 500
    mock_response.raise_for_status.side_effect = requests.exceptions.HTTPError("500 Server Error")

    with patch("app_service.requests.get", return_value=mock_response):
        with pytest.raises(requests.exceptions.HTTPError):
            get_app_rating("com.apple.music")


def test_correct_url_called():
    mock_response = MagicMock()
    mock_response.status_code = 200
    mock_response.json.return_value = {"rating": 4.0}

    with patch("app_service.requests.get", return_value=mock_response) as mock_get:
        get_app_rating("com.apple.music")

    mock_get.assert_called_once_with(
        "https://api.internal/apps/com.apple.music/rating"
    )
```

</details>

---

### Challenge 17 — CI Pipeline Scenario

**Difficulty:** Conceptual

**Problem:**
You are designing a CI/CD pipeline for an internal tool at Apple. The tool has:
- A Python backend (FastAPI)
- A React frontend
- PostgreSQL database
- Integration tests that require a running database

Design the GitHub Actions pipeline stages. What runs in parallel? What must run sequentially? What environment variables and secrets are needed?

<details>
<summary>Solution</summary>

```yaml
# .github/workflows/ci.yml
name: CI

on:
  push:
    branches: [main, develop]
  pull_request:

jobs:
  # These three run in parallel — no dependencies between them
  lint-backend:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v4
        with:
          python-version: "3.12"
      - run: pip install ruff
      - run: ruff check .

  lint-frontend:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: "20"
      - run: npm ci
        working-directory: frontend
      - run: npm run lint
        working-directory: frontend

  type-check:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v4
        with:
          python-version: "3.12"
      - run: pip install mypy
      - run: mypy app/

  # Unit tests run after lint passes (catches syntax errors cheaply first)
  unit-tests:
    needs: [lint-backend, lint-frontend]
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v4
        with:
          python-version: "3.12"
      - run: pip install -r requirements.txt
      - run: pytest tests/unit/ --cov=app --cov-report=xml
      - uses: codecov/codecov-action@v4

  # Integration tests need the database service
  integration-tests:
    needs: [unit-tests]
    runs-on: ubuntu-latest
    services:
      postgres:
        image: postgres:15
        env:
          POSTGRES_DB: testdb
          POSTGRES_USER: testuser
          POSTGRES_PASSWORD: testpassword
        ports:
          - 5432:5432
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-retries 5
    env:
      DATABASE_URL: postgresql://testuser:testpassword@localhost:5432/testdb
      API_KEY: ${{ secrets.TEST_API_KEY }}
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v4
        with:
          python-version: "3.12"
      - run: pip install -r requirements.txt
      - run: alembic upgrade head
      - run: pytest tests/integration/

  # Deploy only on main branch after all tests pass
  deploy:
    needs: [integration-tests, type-check]
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Deploy to staging
        env:
          DEPLOY_TOKEN: ${{ secrets.DEPLOY_TOKEN }}
        run: ./scripts/deploy.sh staging
```

**Key design decisions to explain:**
- Lint and type check run in parallel to save time
- Unit tests run before integration tests to fail fast and cheaply
- Integration tests use GitHub Actions `services:` to spin up a real Postgres container
- Secrets are stored in GitHub Secrets, never hardcoded
- Deploy only runs on `main` branch after all tests pass
- `alembic upgrade head` applies migrations before running integration tests

</details>

---

*End of Coding Challenges*

---

## Challenge Difficulty Summary

| # | Challenge | Difficulty | Pattern |
|---|---|---|---|
| 01 | Parse Purchase Logs | Easy | String parsing |
| 02 | Flatten Nested Data | Easy-Medium | Nested iteration |
| 03 | Group by Region | Medium | defaultdict |
| 04 | Normalize API Response | Medium | Format detection |
| 05 | Redact Sensitive Data | Easy-Medium | Regex |
| 06 | Find Duplicate IDs | Easy | Counter / Set |
| 07 | Validate Plan Codes | Easy-Medium | Regex / Validation |
| 08 | Most Frequent Category | Easy | Counter |
| 09 | Merge Maintenance Windows | Medium | Sort + merge intervals |
| 10 | Rate Limiter | Medium | Deque + sliding window |
| 11 | Find Missing Transaction | Easy-Medium | Math sum formula |
| 12 | Retry with Backoff | Medium | Exception handling |
| 13 | Renewal Date Calculation | Easy-Medium | datetime |
| 14 | Detect Anomalous Transactions | Medium | Statistics |
| 15 | Write Test Suite | Medium | pytest patterns |
| 16 | Mock External API | Medium | unittest.mock |
| 17 | CI Pipeline Design | Conceptual | GitHub Actions |
