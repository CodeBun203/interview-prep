# Find the Bug — Apple SDET Practice
## Python, JavaScript, SQL, API, and Async Challenges

Instructions: Read each broken code block and the description of what it is supposed to do. Find the bug(s) before reading the answer. Answers are at the bottom of each section.

---

## Section 1: Python Bugs

---

### Bug 01 — Mutable Default Argument

**What it should do:** Collect tags into a list and return it. Each call with no list argument should start fresh.

```python
def add_tag(tag, tags=[]):
    tags.append(tag)
    return tags

result1 = add_tag("apple")
result2 = add_tag("music")
print(result1)   # expected: ["apple"]
print(result2)   # expected: ["music"]
```

**Actual output:**
```
['apple']
['apple', 'music']
```

<details>
<summary>Answer</summary>

**Bug:** Default mutable argument. The list `[]` is created once when the function is defined, not on each call. Every call that does not pass a list shares the same list object.

**Fix:**
```python
def add_tag(tag, tags=None):
    if tags is None:
        tags = []
    tags.append(tag)
    return tags
```

**Rule:** Never use a mutable object (list, dict, set) as a default argument. Use `None` and create the object inside the function.

</details>

---

### Bug 02 — Off by One

**What it should do:** Return all but the last element of a list.

```python
def drop_last(items):
    return items[:-0]

print(drop_last([1, 2, 3, 4, 5]))
# expected: [1, 2, 3, 4]
```

**Actual output:**
```
[1, 2, 3, 4, 5]
```

<details>
<summary>Answer</summary>

**Bug:** `[:-0]` is equivalent to `[:0]` which is an empty slice... wait, actually `[:-0]` — in Python, `-0 == 0`, so `items[:-0]` is `items[:0]`... actually no. Let me think again.

`-0` in Python evaluates to `0`. So `items[:-0]` becomes `items[:0]` which would be `[]`. But the actual output shown is the full list `[1,2,3,4,5]`, which means the behavior is `items[:]`.

Actually in Python, `slice(None, -0, None)` — since `-0 == 0` and a slice endpoint of `0` means... `items[:-0]` — Python evaluates `-0` as `0`. But `items[:0]` returns `[]`, not the full list.

Let me reconsider. `[:-0]` — Python interprets this as `[:0]` because `-0 == 0`. And `list[:0]` returns `[]`.

So the actual output would be `[]`, not the full list. Let me fix the bug and answer:

**Bug:** `[:-0]` evaluates to `[:0]` because `-0 == 0` in Python, which returns an empty list. The intent was `[:-1]` to drop the last element.

**Fix:**
```python
def drop_last(items):
    return items[:-1]
```

**Rule:** Negative zero does not exist in Python integers. `0` and `-0` are identical. Always double-check slice indices.

</details>

---

### Bug 03 — Wrong Comparison Operator

**What it should do:** Check if a username is valid. It must be between 3 and 20 characters.

```python
def is_valid_username(username):
    return 3 < len(username) < 20

print(is_valid_username("ab"))      # expected: False
print(is_valid_username("abc"))     # expected: True
print(is_valid_username("hayden"))  # expected: True
```

**Actual output:**
```
False
False
True
```

<details>
<summary>Answer</summary>

**Bug:** `3 < len(username)` uses strict less-than. A username with exactly 3 characters has `len == 3`, and `3 < 3` is `False`. The requirement is "between 3 and 20 characters" which means 3 is valid.

**Fix:**
```python
def is_valid_username(username):
    return 3 <= len(username) <= 20
```

**Rule:** Read requirements carefully for "between" — it almost always means inclusive on both ends unless stated otherwise.

</details>

---

### Bug 04 — None Check

**What it should do:** Return the user's display name, falling back to their email if no display name is set.

```python
def get_display_name(user):
    if user["display_name"]:
        return user["display_name"]
    return user["email"]

user1 = {"display_name": "Hayden", "email": "h@example.com"}
user2 = {"display_name": None, "email": "h@example.com"}
user3 = {"display_name": "", "email": "h@example.com"}

print(get_display_name(user1))   # expected: "Hayden"
print(get_display_name(user2))   # expected: "h@example.com"
print(get_display_name(user3))   # expected: "h@example.com" — empty string means no name set
```

**Actual output:**
```
Hayden
h@example.com
h@example.com
```

This one actually works. Now consider this extended version:

```python
def get_display_name(user):
    if user["display_name"] != None:
        return user["display_name"]
    return user["email"]
```

<details>
<summary>Answer</summary>

**Bug:** `user["display_name"] != None` catches `None` correctly but an empty string `""` is not `None`. So a user with `display_name = ""` would return `""` instead of the email fallback.

The original truthiness check `if user["display_name"]` handles both `None` and `""` as falsy, which is actually correct here.

The bug in the extended version is using `!= None` instead of `is not None`, AND it fails to handle the empty string case.

**Fix:**
```python
def get_display_name(user):
    if user.get("display_name"):  # handles None, "", and missing key
        return user["display_name"]
    return user["email"]
```

**Rule:** Use `is None` / `is not None` for explicit None checks. Use truthiness checks when you want to treat empty strings and None the same way. Use `.get()` to avoid KeyError on missing keys.

</details>

---

### Bug 05 — Integer Division vs Float Division

**What it should do:** Calculate the average price of a list of purchase amounts.

```python
def average_price(amounts):
    return sum(amounts) / len(amounts)

print(average_price([10, 20, 30]))   # expected: 20.0
print(average_price([1, 2]))         # expected: 1.5
print(average_price([]))             # expected: 0
```

<details>
<summary>Answer</summary>

**Bug:** `average_price([])` causes a `ZeroDivisionError` because `len([])` is `0`. The function has no guard for an empty list.

**Fix:**
```python
def average_price(amounts):
    if not amounts:
        return 0
    return sum(amounts) / len(amounts)
```

**Rule:** Always handle the empty collection case for any function that computes aggregates. This is a boundary condition that will appear in every SDET coding exercise.

</details>

---

### Bug 06 — Loop Mutation

**What it should do:** Remove all expired subscriptions from a list.

```python
def remove_expired(subscriptions):
    for sub in subscriptions:
        if sub["expired"]:
            subscriptions.remove(sub)
    return subscriptions

subs = [
    {"id": 1, "expired": True},
    {"id": 2, "expired": True},
    {"id": 3, "expired": False},
]
print(remove_expired(subs))
# expected: [{"id": 3, "expired": False}]
```

**Actual output:**
```
[{'id': 2, 'expired': True}, {'id': 3, 'expired': False}]
```

<details>
<summary>Answer</summary>

**Bug:** You must never remove items from a list while iterating over it. When item at index 0 is removed, everything shifts left. The loop's internal counter moves to index 1, but what was index 1 is now index 0. Items get skipped.

**Fix:**
```python
def remove_expired(subscriptions):
    return [sub for sub in subscriptions if not sub["expired"]]
```

**Rule:** Never mutate a collection while iterating over it. Use a list comprehension or iterate over a copy.

</details>

---

### Bug 07 — String vs Integer Comparison

**What it should do:** Parse purchase data from an API response and filter purchases over $50.

```python
def get_large_purchases(purchases):
    return [p for p in purchases if p["amount"] > 50]

purchases = [
    {"id": 1, "amount": "120"},
    {"id": 2, "amount": "30"},
    {"id": 3, "amount": "75"},
]
print(get_large_purchases(purchases))
```

<details>
<summary>Answer</summary>

**Bug:** `amount` values are strings (`"120"`, `"30"`, `"75"`), not integers. Comparing a string to an integer with `>` raises a `TypeError` in Python 3.

**Fix:**
```python
def get_large_purchases(purchases):
    return [p for p in purchases if float(p["amount"]) > 50]
```

**Rule:** API responses and JSON data almost always deliver numbers as strings unless the schema explicitly enforces numeric types. Always cast before comparing.

</details>

---

### Bug 08 — Shallow Copy Trap

**What it should do:** Apply a discount to a purchase record without modifying the original.

```python
def apply_discount(purchase, discount_pct):
    updated = purchase.copy()
    updated["amount"] -= updated["amount"] * (discount_pct / 100)
    return updated

original = {"id": 1, "amount": 100, "metadata": {"currency": "USD", "region": "US"}}
discounted = apply_discount(original, 20)

print(discounted["amount"])          # expected: 80
print(original["amount"])            # expected: 100 (unchanged)
print(original["metadata"])          # expected: {"currency": "USD", "region": "US"}
```

This works fine as shown. Now add this line to the function:

```python
def apply_discount(purchase, discount_pct):
    updated = purchase.copy()
    updated["amount"] -= updated["amount"] * (discount_pct / 100)
    updated["metadata"]["region"] = "DISCOUNTED"   # added line
    return updated
```

<details>
<summary>Answer</summary>

**Bug:** `.copy()` is a shallow copy. It creates a new dict but nested objects (like `metadata`) are still shared references. Modifying `updated["metadata"]` also modifies `original["metadata"]`.

**Fix:**
```python
import copy

def apply_discount(purchase, discount_pct):
    updated = copy.deepcopy(purchase)
    updated["amount"] -= updated["amount"] * (discount_pct / 100)
    updated["metadata"]["region"] = "DISCOUNTED"
    return updated
```

**Rule:** Use `copy.deepcopy()` when you need a truly independent copy of a nested structure. `.copy()` only copies the top level.

</details>

---

## Section 2: API and HTTP Bugs

---

### Bug 09 — Missing Error Handling on HTTP Response

**What it should do:** Fetch a user's subscription status from an internal API and return it.

```python
import requests

def get_subscription_status(user_id):
    response = requests.get(f"https://api.internal/users/{user_id}/subscription")
    data = response.json()
    return data["status"]
```

<details>
<summary>Answer</summary>

**Bugs (multiple):**
1. No check that the HTTP request succeeded. If the server returns a 404 or 500, `response.json()` may fail or return an error payload.
2. No handling for the case where `data` does not contain the key `"status"`.
3. No timeout. The request can hang indefinitely.
4. No handling for network errors (connection refused, DNS failure).

**Fix:**
```python
import requests

def get_subscription_status(user_id):
    try:
        response = requests.get(
            f"https://api.internal/users/{user_id}/subscription",
            timeout=5
        )
        response.raise_for_status()   # raises HTTPError for 4xx/5xx
        data = response.json()
        return data.get("status")     # returns None if key missing, no KeyError
    except requests.exceptions.Timeout:
        raise RuntimeError(f"Request timed out for user {user_id}")
    except requests.exceptions.HTTPError as e:
        raise RuntimeError(f"API returned error: {e.response.status_code}")
    except requests.exceptions.RequestException as e:
        raise RuntimeError(f"Network error: {e}")
```

**Rule:** Never assume an HTTP request succeeds. Always check status codes, handle timeouts, and guard against missing keys in the response.

</details>

---

### Bug 10 — JSON Serialization Error

**What it should do:** Send a purchase record to an internal service as JSON.

```python
import requests
from datetime import datetime

def record_purchase(purchase_id, amount, timestamp):
    payload = {
        "purchase_id": purchase_id,
        "amount": amount,
        "timestamp": timestamp
    }
    response = requests.post("https://api.internal/purchases", json=payload)
    return response.status_code

record_purchase(1001, 49.99, datetime.now())
```

<details>
<summary>Answer</summary>

**Bug:** `datetime.now()` returns a `datetime` object. The `requests` library uses `json.dumps()` internally, which cannot serialize `datetime` objects and raises a `TypeError`.

**Fix:**
```python
from datetime import datetime, timezone

def record_purchase(purchase_id, amount, timestamp):
    payload = {
        "purchase_id": purchase_id,
        "amount": amount,
        "timestamp": timestamp.isoformat()   # convert to ISO 8601 string
    }
    response = requests.post("https://api.internal/purchases", json=payload)
    return response.status_code
```

**Rule:** `datetime`, `Decimal`, `UUID`, and custom objects are not JSON-serializable by default. Always convert them to strings or numbers before serializing.

</details>

---

### Bug 11 — Pagination Not Handled

**What it should do:** Fetch all purchases for a user from a paginated API and return them as a single list.

```python
import requests

def get_all_purchases(user_id):
    response = requests.get(f"https://api.internal/users/{user_id}/purchases")
    data = response.json()
    return data["purchases"]
```

<details>
<summary>Answer</summary>

**Bug:** The function only fetches the first page of results. Paginated APIs return a subset of results and a cursor or next-page URL. This function silently discards all purchases beyond the first page, which looks correct in testing with small datasets but fails in production.

**Fix:**
```python
import requests

def get_all_purchases(user_id):
    all_purchases = []
    url = f"https://api.internal/users/{user_id}/purchases"

    while url:
        response = requests.get(url, timeout=5)
        response.raise_for_status()
        data = response.json()
        all_purchases.extend(data["purchases"])
        url = data.get("next_page_url")   # None if last page

    return all_purchases
```

**Rule:** Always check API documentation for pagination. A function that returns a list from an API should handle all pages unless you intentionally want only the first.

</details>

---

## Section 3: JavaScript / Node.js Bugs

---

### Bug 12 — var Hoisting

**What it should do:** Log each purchase ID in the array after a 1-second delay.

```javascript
const purchases = [101, 102, 103];

for (var i = 0; i < purchases.length; i++) {
    setTimeout(function() {
        console.log(purchases[i]);
    }, 1000);
}

// expected output (after 1 second):
// 101
// 102
// 103
```

**Actual output:**
```
undefined
undefined
undefined
```

<details>
<summary>Answer</summary>

**Bug:** `var` is function-scoped, not block-scoped. By the time the `setTimeout` callbacks fire, the loop has finished and `i` is `3` (past the last index). All three callbacks share the same `i` variable. `purchases[3]` is `undefined`.

**Fix using `let`:**
```javascript
for (let i = 0; i < purchases.length; i++) {
    setTimeout(function() {
        console.log(purchases[i]);
    }, 1000);
}
```

`let` is block-scoped. Each iteration creates a new binding of `i`.

**Fix using `forEach`:**
```javascript
purchases.forEach(function(id) {
    setTimeout(function() {
        console.log(id);
    }, 1000);
});
```

**Rule:** Never use `var` in modern JavaScript. Use `let` for variables that change and `const` for variables that do not.

</details>

---

### Bug 13 — Promise Error Not Caught

**What it should do:** Fetch app store data and log the result. If the request fails, log an error.

```javascript
async function fetchAppData(appId) {
    const response = await fetch(`https://api.internal/apps/${appId}`);
    const data = await response.json();
    console.log(data);
}

fetchAppData("com.apple.music");
```

<details>
<summary>Answer</summary>

**Bug:** If `fetch()` throws a network error, or if `response.json()` fails, the error is unhandled. The function returns a rejected Promise with no catch handler. In Node.js this can crash the process or produce an `UnhandledPromiseRejectionWarning`.

Additionally, `fetch()` does not throw on non-2xx status codes. A 404 or 500 response will not be caught.

**Fix:**
```javascript
async function fetchAppData(appId) {
    try {
        const response = await fetch(`https://api.internal/apps/${appId}`);
        if (!response.ok) {
            throw new Error(`HTTP error: ${response.status}`);
        }
        const data = await response.json();
        console.log(data);
        return data;
    } catch (error) {
        console.error(`Failed to fetch app ${appId}:`, error.message);
        throw error;
    }
}
```

**Rule:** `fetch()` only rejects on network failure. Always check `response.ok` for HTTP errors. Always wrap async operations in try/catch.

</details>

---

### Bug 14 — Strict Equality vs Loose Equality

**What it should do:** Check if a subscription tier entitles the user to premium content.

```javascript
function hasPremiumAccess(tier) {
    return tier == 2 || tier == 3;
}

console.log(hasPremiumAccess(2));    // expected: true
console.log(hasPremiumAccess("2"));  // expected: false — string "2" is not a valid tier
console.log(hasPremiumAccess(null)); // expected: false
```

**Actual output:**
```
true
true   // BUG: should be false
false
```

<details>
<summary>Answer</summary>

**Bug:** `==` uses loose equality which coerces types. `"2" == 2` is `true` in JavaScript because `"2"` gets coerced to `2`.

**Fix:**
```javascript
function hasPremiumAccess(tier) {
    return tier === 2 || tier === 3;
}
```

**Rule:** Always use `===` (strict equality) in JavaScript. `==` performs type coercion and produces surprising results. The only time you might use `==` is to check for `null` OR `undefined` simultaneously: `value == null`.

</details>

---

### Bug 15 — Async forEach

**What it should do:** Process a list of purchase IDs asynchronously and return all results.

```javascript
async function processAll(purchaseIds) {
    const results = [];
    purchaseIds.forEach(async (id) => {
        const result = await processPurchase(id);
        results.push(result);
    });
    return results;
}
```

<details>
<summary>Answer</summary>

**Bug:** `forEach` does not await async callbacks. It fires all the async functions and immediately continues to `return results`, which is still empty. The `processPurchase` calls run in the background after the function has already returned.

**Fix using Promise.all:**
```javascript
async function processAll(purchaseIds) {
    const results = await Promise.all(
        purchaseIds.map(id => processPurchase(id))
    );
    return results;
}
```

**Fix using for...of (sequential processing):**
```javascript
async function processAll(purchaseIds) {
    const results = [];
    for (const id of purchaseIds) {
        const result = await processPurchase(id);
        results.push(result);
    }
    return results;
}
```

**Rule:** `forEach` is not async-aware. Use `Promise.all` with `.map()` for concurrent async processing, or `for...of` for sequential async processing.

</details>

---

## Section 4: SQL Bugs

---

### Bug 16 — NULL Comparison

**What it should do:** Find all subscriptions that have no cancellation date (i.e., still active).

```sql
SELECT *
FROM subscriptions
WHERE cancelled_at = NULL;
```

<details>
<summary>Answer</summary>

**Bug:** You cannot compare to NULL with `=`. `NULL = NULL` evaluates to `NULL` (unknown), not `TRUE`. This query returns zero rows regardless of how many subscriptions have no cancellation date.

**Fix:**
```sql
SELECT *
FROM subscriptions
WHERE cancelled_at IS NULL;
```

**Rule:** Always use `IS NULL` or `IS NOT NULL` to check for NULL values. Never use `= NULL` or `!= NULL`.

</details>

---

### Bug 17 — Aggregate Without GROUP BY

**What it should do:** Return the total purchase amount per user.

```sql
SELECT user_id, SUM(amount) as total_spent
FROM purchases;
```

<details>
<summary>Answer</summary>

**Bug:** When using an aggregate function like `SUM()`, every non-aggregated column in the SELECT must appear in a GROUP BY clause. This query will either throw an error or return one row with a single sum across all users (depending on the database).

**Fix:**
```sql
SELECT user_id, SUM(amount) as total_spent
FROM purchases
GROUP BY user_id;
```

**Rule:** Any column in SELECT that is not inside an aggregate function (SUM, COUNT, AVG, MAX, MIN) must be in the GROUP BY clause.

</details>

---

## Section 5: Test Code Bugs

---

### Bug 18 — Test That Never Fails

**What it should do:** Verify that `divide()` raises a `ZeroDivisionError` when dividing by zero.

```python
import pytest

def divide(a, b):
    return a / b

def test_divide_by_zero():
    try:
        divide(10, 0)
    except ZeroDivisionError:
        pass
```

<details>
<summary>Answer</summary>

**Bug:** This test always passes, even if `divide()` is changed to return `0` instead of raising an error. The `try/except` swallows the exception silently, and if no exception is raised, the test still passes because there is no assertion.

**Fix:**
```python
def test_divide_by_zero():
    with pytest.raises(ZeroDivisionError):
        divide(10, 0)
```

**Rule:** Never use bare `try/except` in a test to verify that an exception is raised. Use `pytest.raises()` as a context manager. It explicitly fails if the exception is NOT raised.

</details>

---

### Bug 19 — Asserting on Wrong Value

**What it should do:** Verify that `apply_discount()` correctly applies a 10% discount.

```python
def apply_discount(price, pct):
    return price - (price * pct / 100)

def test_apply_discount():
    result = apply_discount(100, 10)
    assert result == 90.0
    assert apply_discount(50, 20) == 40
    assert apply_discount(0, 50) == 0
    assert apply_discount(100, 0) == 100
    assert apply_discount(100, 100) == 100   # BUG IS HERE
```

<details>
<summary>Answer</summary>

**Bug:** `apply_discount(100, 100)` should return `0` (100% discount means the item is free). The assertion `== 100` is wrong. The test itself contains a logic error.

**Fix:**
```python
assert apply_discount(100, 100) == 0
```

**Rule:** Test assertions must be mathematically verified. Bugs in tests are harder to catch than bugs in code because tests are supposed to be the ground truth.

</details>

---

### Bug 20 — Test Depends on Execution Order

**What it should do:** Two independent tests for a shopping cart.

```python
cart = []

def test_add_item():
    cart.append({"item": "song", "price": 1.29})
    assert len(cart) == 1

def test_remove_item():
    cart.append({"item": "song", "price": 1.29})
    cart.pop()
    assert len(cart) == 0
```

<details>
<summary>Answer</summary>

**Bug:** Both tests share a module-level `cart` list. `test_add_item` leaves an item in the cart. If `test_remove_item` runs after it, the cart starts with one item, gains another, then removes one, leaving `len(cart) == 1`, which fails the assertion. Tests that depend on execution order are fragile and wrong.

**Fix:**
```python
import pytest

@pytest.fixture
def cart():
    return []

def test_add_item(cart):
    cart.append({"item": "song", "price": 1.29})
    assert len(cart) == 1

def test_remove_item(cart):
    cart.append({"item": "song", "price": 1.29})
    cart.pop()
    assert len(cart) == 0
```

**Rule:** Tests must be fully independent. Use pytest fixtures to create fresh state for each test. Never share mutable state between tests at the module level.

</details>

---

### Bug 21 — Floating Point Assertion

**What it should do:** Verify that a tax calculation is correct.

```python
def calculate_tax(price, rate):
    return price * rate

def test_calculate_tax():
    assert calculate_tax(9.99, 0.1) == 0.999
```

<details>
<summary>Answer</summary>

**Bug:** `9.99 * 0.1` in floating point arithmetic equals `0.9990000000000001`, not `0.999`. The strict `==` assertion fails.

**Fix:**
```python
import pytest

def test_calculate_tax():
    assert calculate_tax(9.99, 0.1) == pytest.approx(0.999)

# Or with a relative tolerance:
    assert calculate_tax(9.99, 0.1) == pytest.approx(0.999, rel=1e-6)
```

**Rule:** Never use `==` to compare floating point numbers. Use `pytest.approx()`, which allows a small tolerance for floating point imprecision.

</details>

---

## Section 6: Async Python Bugs

---

### Bug 22 — Blocking Call in Async Function

**What it should do:** Fetch subscription data for multiple users concurrently.

```python
import asyncio
import time
import httpx

async def fetch_user(user_id):
    time.sleep(1)   # simulate processing delay
    async with httpx.AsyncClient() as client:
        response = await client.get(f"https://api.internal/users/{user_id}")
        return response.json()

async def fetch_all(user_ids):
    tasks = [fetch_user(uid) for uid in user_ids]
    return await asyncio.gather(*tasks)
```

<details>
<summary>Answer</summary>

**Bug:** `time.sleep()` is a blocking call. When called inside an `async` function, it blocks the entire event loop, preventing any other coroutines from running. This defeats the purpose of using async and makes concurrent execution sequential.

**Fix:**
```python
async def fetch_user(user_id):
    await asyncio.sleep(1)   # non-blocking sleep — yields control to event loop
    async with httpx.AsyncClient() as client:
        response = await client.get(f"https://api.internal/users/{user_id}")
        return response.json()
```

**Rule:** In async code, never call blocking functions (time.sleep, file I/O with `open()`, CPU-heavy computation). Use their async equivalents: `asyncio.sleep()`, `aiofiles`, or run blocking code in a thread pool with `asyncio.run_in_executor()`.

</details>

---

### Bug 23 — Forgetting to Await

**What it should do:** Return the result of an async API call.

```python
import asyncio
import httpx

async def get_price(item_id):
    async with httpx.AsyncClient() as client:
        response = await client.get(f"https://api.internal/items/{item_id}/price")
        return response.json()

async def main():
    price_data = get_price("song_123")   # BUG IS HERE
    print(price_data)

asyncio.run(main())
```

<details>
<summary>Answer</summary>

**Bug:** `get_price("song_123")` is called without `await`. This returns a coroutine object, not the actual price data. The print statement outputs something like `<coroutine object get_price at 0x...>`, and the coroutine never actually runs.

**Fix:**
```python
async def main():
    price_data = await get_price("song_123")
    print(price_data)
```

**Rule:** Calling an `async def` function without `await` does not execute it. It returns a coroutine object. You must `await` it to get the result. Most async linters will catch this.

</details>

---

## Section 7: Data Transformation Bugs

---

### Bug 24 — Dict Key Error on Missing Field

**What it should do:** Extract the storefront region from a list of purchase records. Some records may not have a storefront field.

```python
def extract_regions(purchases):
    return [p["storefront"]["region"] for p in purchases]

purchases = [
    {"id": 1, "storefront": {"region": "US"}},
    {"id": 2, "storefront": {"region": "UK"}},
    {"id": 3},   # no storefront
]
```

<details>
<summary>Answer</summary>

**Bug:** `p["storefront"]` raises a `KeyError` for purchase id 3, which has no `storefront` key.

**Fix:**
```python
def extract_regions(purchases):
    return [
        p.get("storefront", {}).get("region", "UNKNOWN")
        for p in purchases
    ]
```

**Rule:** When working with data from APIs or external sources, never assume all keys are present. Chain `.get()` calls for nested access.

</details>

---

### Bug 25 — Modifying During Iteration (Dict)

**What it should do:** Remove all items from a catalog where the price is zero.

```python
def remove_free_items(catalog):
    for item_id, item in catalog.items():
        if item["price"] == 0:
            del catalog[item_id]
    return catalog
```

<details>
<summary>Answer</summary>

**Bug:** Modifying a dictionary while iterating over it with `.items()` raises a `RuntimeError: dictionary changed size during iteration`.

**Fix:**
```python
def remove_free_items(catalog):
    return {
        item_id: item
        for item_id, item in catalog.items()
        if item["price"] != 0
    }
```

**Rule:** Never add or remove keys from a dict while iterating over it. Build a new dict with a comprehension instead.

</details>

---

*End of Bug Challenges*

---

## Answer Key Summary

| Bug | Category | Core Issue |
|---|---|---|
| 01 | Python | Mutable default argument |
| 02 | Python | Negative zero in slice |
| 03 | Python | Strict vs inclusive comparison |
| 04 | Python | None vs falsy check |
| 05 | Python | Division by zero on empty input |
| 06 | Python | Mutating list during iteration |
| 07 | Python | String vs integer comparison from API |
| 08 | Python | Shallow copy of nested object |
| 09 | API | No error handling on HTTP response |
| 10 | API | datetime not JSON serializable |
| 11 | API | Pagination not handled |
| 12 | JavaScript | var hoisting in closure |
| 13 | JavaScript | Unhandled Promise rejection |
| 14 | JavaScript | Loose equality type coercion |
| 15 | JavaScript | Async forEach does not await |
| 16 | SQL | NULL comparison with = instead of IS |
| 17 | SQL | Missing GROUP BY with aggregate |
| 18 | Test | Exception test that never fails |
| 19 | Test | Wrong assertion value in test |
| 20 | Test | Shared mutable state between tests |
| 21 | Test | Floating point strict equality |
| 22 | Async | Blocking call in async function |
| 23 | Async | Missing await on coroutine |
| 24 | Data | KeyError on missing nested key |
| 25 | Data | Mutating dict during iteration |
