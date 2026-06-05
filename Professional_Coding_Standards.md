# Professional Coding Standards
## What FAANG Interviewers Actually Expect

---

## The Short Answer First

| Practice | In Python | In JavaScript | In Java | In an Interview |
|---|---|---|---|---|
| Type hints | Always | Use TypeScript | Always | Yes |
| Docstrings | Yes, brief | JSDoc, brief | Javadoc, brief | Yes, one line |
| `@param @return` | No — wrong language | JSDoc style | Javadoc style | Only in Java/JS |
| `@author` | Never | Never | Sometimes in prod | Never |
| Comments | Sparingly | Sparingly | Sparingly | Only if non-obvious |
| Clean naming | Always | Always | Always | This matters most |

---

## The @param @author @input @output Question

These are **JavaDoc** tags. They belong in Java. Using them in Python looks like you learned Java first and carried the habit over. Python has its own docstring convention and interviewers who write Python daily will notice.

**In Java:**
```java
/**
 * Calculates the total price of an order after applying a discount.
 *
 * @param items List of items with price and quantity fields
 * @param discountCode Optional discount code string, or null
 * @return Total price as a double after discount applied
 * @throws IllegalArgumentException if items is empty
 */
public double calculateTotal(List<Item> items, String discountCode) {
```

**In Python, the equivalent is Google-style docstrings:**
```python
def calculate_total(items: list[dict], discount_code: str | None = None) -> float:
    """Calculate the total price of an order after applying a discount.

    Args:
        items: List of item dicts, each with 'price' and 'quantity' keys.
        discount_code: Optional discount code. Pass None for no discount.

    Returns:
        Total price as a float after discount is applied.

    Raises:
        ValueError: If items is empty or discount_code is unrecognized.
    """
```

**In JavaScript/TypeScript, JSDoc:**
```javascript
/**
 * Calculates the total price of an order.
 * @param {Object[]} items - Array of items with price and quantity
 * @param {string|null} discountCode - Optional discount code
 * @returns {number} Total price after discount
 */
function calculateTotal(items, discountCode = null) {
```

If you are writing TypeScript (which the Apple role uses), you often skip JSDoc entirely because the types already document the function:
```typescript
function calculateTotal(
  items: OrderItem[],
  discountCode?: string
): number {
```

**`@author` specifically:** Never use it. Not in an interview, not in production code at any of these companies. Source control (git blame) is authoritative for who wrote what. An `@author` tag that never gets updated as code changes is misinformation.

---

## Type Hints in Python: Always

Type hints are not optional at any serious company. They are a first-class part of modern Python. Interviewers writing Python professionally will expect them.

```python
# No type hints — looks like Python 2 era code
def process_purchase(purchase_id, amount, user):
    pass

# With type hints — professional Python 3
def process_purchase(
    purchase_id: str,
    amount: float,
    user: User
) -> PurchaseResult:
    pass
```

**The full type hint toolkit you should know:**

```python
from typing import Optional   # for Python < 3.10
# Python 3.10+ can use X | None syntax directly

# Basic types
name: str
count: int
price: float
active: bool

# Collections
items: list[str]
mapping: dict[str, int]
unique: set[str]
pair: tuple[str, int]

# Optional (can be the type or None)
user_id: str | None = None          # Python 3.10+
user_id: Optional[str] = None       # Python 3.9 and below

# Union (one of several types)
value: int | str                    # Python 3.10+
from typing import Union
value: Union[int, str]              # Python 3.9 and below

# Callable
from typing import Callable
handler: Callable[[str, int], bool]  # takes str and int, returns bool

# Return types
def get_user() -> User: ...
def get_user() -> User | None: ...  # may return None
def save() -> None: ...             # returns nothing

# TypedDict for dict structures
from typing import TypedDict

class OrderItem(TypedDict):
    item_id: str
    price: float
    quantity: int
```

---

## Docstrings: The Right Amount

The professional standard is: write a docstring when the function's purpose is not immediately obvious from its name and type signature alone.

**Does NOT need a docstring:**
```python
def is_even(n: int) -> bool:
    return n % 2 == 0

def get_user_by_id(user_id: str) -> User | None:
    return db.query(User).filter(User.id == user_id).first()
```

These functions are self-documenting. A docstring adds nothing.

**Does need a docstring:**
```python
def process_renewal_batch(subscriptions: list[Subscription]) -> RenewalSummary:
    """Attempt renewal charges for all subscriptions due today.

    Processes each subscription independently so a single failure does not
    block the rest of the batch. Failed renewals are marked as past_due
    rather than immediately cancelled.

    Args:
        subscriptions: List of Subscription objects with status=active
            and next_billing_date=today.

    Returns:
        RenewalSummary with counts of renewed, failed, and skipped.

    Raises:
        DatabaseError: If the batch status update fails after processing.
    """
```

This function is non-obvious. The behavior on failure (mark as past_due, do not cancel) is a business decision that is not visible from the code alone. That belongs in the docstring.

**Docstring length guidelines:**

| Function complexity | Docstring |
|---|---|
| Trivially obvious | None |
| Obvious but non-trivial | One line |
| Multiple parameters, specific behavior | Args + Returns |
| Can raise exceptions | Args + Returns + Raises |
| Surprising design decision | Add a note explaining why |

**One-line docstring:**
```python
def calculate_tax(price: float, rate: float) -> float:
    """Return price multiplied by rate, representing tax amount."""
    return price * rate
```

**Full Google-style docstring:**
```python
def merge_subscription_events(
    primary: list[Event],
    secondary: list[Event],
    window_seconds: int = 30
) -> list[Event]:
    """Merge two event streams, deduplicating events within a time window.

    Events from primary take precedence over secondary events with the
    same type occurring within window_seconds of each other. This prevents
    duplicate processing when the same user action triggers events from
    multiple services.

    Args:
        primary: Events from the primary service, in ascending timestamp order.
        secondary: Events from the secondary service, same ordering.
        window_seconds: Events within this window are considered duplicates.
            Defaults to 30 seconds.

    Returns:
        Merged, deduplicated list in ascending timestamp order.

    Example:
        >>> primary = [Event(type="purchase", ts=1000)]
        >>> secondary = [Event(type="purchase", ts=1010)]
        >>> merge_subscription_events(primary, secondary, window_seconds=30)
        [Event(type="purchase", ts=1000)]
    """
```

---

## Comments: The Most Misunderstood Practice

This is where most developers get it wrong in both directions. Too many comments is just as unprofessional as too few.

### Never write comments that describe WHAT the code does

```python
# BAD: the comment restates the code
# Increment the counter
count += 1

# BAD: the comment describes what is obvious from reading
# Check if the user is active
if user.status == "active":

# BAD: narrating a for loop
# Loop through each item in the list
for item in items:
```

If a reader needs a comment to understand what `count += 1` does, the problem is not a missing comment. The problem is naming.

### Write comments that explain WHY, when the why is non-obvious

```python
# GOOD: explains a non-obvious constraint
# Stripe requires idempotency keys to be unique per customer per 24 hours,
# not globally. Using user_id as prefix prevents collisions across customers
# while allowing retries within a session.
idempotency_key = f"{user_id}_{session_id}_{int(time.time() // 86400)}"

# GOOD: explains a workaround for a specific external behavior
# ServiceTitan's API returns 200 even for failed operations and encodes
# the error in the response body. Check the 'success' field explicitly.
if not response.json().get("success"):
    raise ServiceTitanError(response.json().get("message"))

# GOOD: explains a surprising algorithmic choice
# Use a deque instead of a list here because we need O(1) popleft
# when sliding the window. list.pop(0) is O(n).
window = deque()
```

### The test for whether a comment is necessary

Ask: would a competent Python developer who reads this code be surprised or confused without the comment? If yes, add it. If no, delete it.

---

## What Makes Code Look Professional: The Full Picture

Beyond documentation, here is what separates professional code from junior code. These matter more than any docstring.

### 1. Early returns over nested conditions

```python
# Amateur: deep nesting, hard to follow
def process_order(order):
    if order is not None:
        if order.status == "pending":
            if order.items:
                total = sum(item.price for item in order.items)
                return total
            else:
                return 0
        else:
            raise ValueError("Order not pending")
    else:
        raise ValueError("Order is None")

# Professional: early returns, linear flow
def process_order(order: Order) -> float:
    if order is None:
        raise ValueError("Order cannot be None")
    if order.status != "pending":
        raise ValueError(f"Cannot process order with status: {order.status}")
    if not order.items:
        return 0.0
    return sum(item.price for item in order.items)
```

### 2. Descriptive names over comments

```python
# Amateur: cryptic names, needs comments
def calc(p, r, d):
    # p = price, r = rate, d = days
    return p * (1 + r) ** d

# Professional: names are self-documenting
def calculate_compound_interest(
    principal: float,
    daily_rate: float,
    days: int
) -> float:
    return principal * (1 + daily_rate) ** days
```

### 3. Handle edge cases explicitly, not silently

```python
# Amateur: silently returns wrong value on bad input
def average(numbers):
    return sum(numbers) / len(numbers)

# Professional: explicit edge case handling
def average(numbers: list[float]) -> float:
    if not numbers:
        raise ValueError("Cannot compute average of empty list")
    return sum(numbers) / len(numbers)
```

### 4. Flat is better than clever

```python
# Clever: hard to read at a glance
result = [x for xs in [[f(i) for i in range(n)] for n in sizes] for x in xs]

# Professional: broken into named steps
batches = [[f(i) for i in range(n)] for n in sizes]
result = [item for batch in batches for item in batch]
```

### 5. Constants over magic numbers

```python
# Amateur: magic numbers
if retry_count > 3:
    raise TimeoutError()
time.sleep(1.5)

# Professional: named constants
MAX_RETRIES = 3
RETRY_DELAY_SECONDS = 1.5

if retry_count > MAX_RETRIES:
    raise TimeoutError()
time.sleep(RETRY_DELAY_SECONDS)
```

### 6. Single responsibility functions

```python
# Amateur: one function doing too much
def process_and_save_and_notify(order):
    total = sum(item.price for item in order.items)
    order.total = total
    db.save(order)
    email_client.send(order.user.email, f"Order total: {total}")
    slack_client.post(f"New order: {order.id}")
    return order

# Professional: separated concerns
def calculate_order_total(order: Order) -> float:
    return sum(item.price for item in order.items)

def save_order(order: Order) -> None:
    db.session.add(order)
    db.session.commit()

def notify_order_created(order: Order) -> None:
    email_client.send_order_confirmation(order)
    slack_client.post_order_alert(order)
```

---

## Interview-Specific: What to Do on CoderPad

CoderPad is a live environment where the hiring manager watches you code in real time. Your documentation approach should be fast and deliberate, not slow and comprehensive.

**The right pace:**

1. Write the function signature with type hints first: 10 seconds
2. Write a one-line docstring if the function is non-trivial: 10 seconds
3. Write the implementation
4. Add a comment only if you make a non-obvious choice

Do not spend three minutes writing docstrings while the interviewer watches. That signals you are uncertain and stalling, not that you are thorough.

**What to actually write in the interview:**

```python
def find_duplicate_transactions(transactions: list[dict]) -> list[str]:
    """Return IDs of transactions that appear more than once."""
    seen = set()
    duplicates = set()
    for txn in transactions:
        tid = txn["id"]
        if tid in seen:
            duplicates.add(tid)
        seen.add(tid)
    return sorted(duplicates)
```

That is the right level. Type hints on the signature, one-line docstring, clean implementation. No more is needed for an interview problem.

If you make a deliberate choice worth explaining, say it out loud rather than writing a comment. The interviewer is watching. Talking is faster than typing and shows active reasoning.

"I am using a set here for O(1) lookup instead of checking the list each time" is better said than written in a comment during a timed exercise.

---

## Interview-Specific: What to Do on HackerRank (Amazon)

HackerRank is autograded and timed. The priority order is:

1. Correct output on all test cases
2. Time complexity within limits
3. Clean readable code

Docstrings on HackerRank will not affect your score. Type hints will not affect your score. But clean, readable code helps YOU by making it easier to debug when a test case fails.

Spend your documentation budget on naming. A well-named variable catches bugs before they happen.

---

## The Full Professional Python Template

This is what a well-written Python module looks like. Use it as a mental reference.

```python
"""
Module for processing subscription renewal batches.

This module handles the daily renewal cycle for active subscriptions,
including payment processing, status updates, and failure handling.
"""

from __future__ import annotations

from dataclasses import dataclass
from typing import TYPE_CHECKING

if TYPE_CHECKING:
    from app.models import Subscription


MAX_RETRY_ATTEMPTS = 3
RENEWAL_BATCH_SIZE = 100


@dataclass
class RenewalResult:
    subscription_id: str
    success: bool
    error_message: str | None = None


def process_renewal_batch(
    subscriptions: list[Subscription],
) -> list[RenewalResult]:
    """Attempt renewal charges for a batch of subscriptions.

    Processes each subscription independently so a single payment failure
    does not block the rest of the batch.

    Args:
        subscriptions: Active subscriptions with billing due today.

    Returns:
        List of RenewalResult, one per subscription, in input order.

    Raises:
        ValueError: If subscriptions is empty.
    """
    if not subscriptions:
        raise ValueError("Subscription batch cannot be empty")

    return [_attempt_renewal(sub) for sub in subscriptions]


def _attempt_renewal(subscription: Subscription) -> RenewalResult:
    """Attempt a single subscription renewal and return the result."""
    try:
        charge_subscription(subscription.id)
        return RenewalResult(subscription_id=subscription.id, success=True)
    except PaymentDeclinedError as e:
        return RenewalResult(
            subscription_id=subscription.id,
            success=False,
            error_message=str(e),
        )


def charge_subscription(subscription_id: str) -> None:
    """Charge the payment method on file for a subscription.

    Raises:
        PaymentDeclinedError: If the charge is declined by the payment processor.
        SubscriptionNotFoundError: If no subscription matches the given ID.
    """
    ...
```

**What to notice:**
- Module-level docstring explains the purpose of the whole file
- Public functions have full docstrings with Args and Raises
- Private function (prefixed with `_`) has a one-liner because it is an internal helper
- Type hints on everything
- Named constants instead of magic numbers
- No inline comments because the code is self-explanatory
- No `@author`, no `@param`, no JavaDoc artifacts

---

## Quick Reference Card

**Always do:**
- Type hints on every function signature
- One-line docstring on non-obvious functions
- Full docstring (Args/Returns/Raises) on complex public functions
- Descriptive names over abbreviations
- Named constants over magic numbers
- Early returns over deep nesting

**Never do:**
- `@author` tags
- `@param` / `@input` / `@output` in Python (that is Java style)
- Comments that restate what the code already says
- One-letter variable names outside of mathematical loops
- Docstrings so long they take longer to read than the function

**In an interview specifically:**
- Type hints: yes, fast
- One-line docstring: yes if non-obvious
- Comments: say them out loud instead of writing them
- Multi-paragraph docstrings: no, you do not have time
