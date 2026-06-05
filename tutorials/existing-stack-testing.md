# Testing Reference — Your Existing Production Stack

> You already use these daily. This is a quick reference so you can speak crisply about HOW you test
> in each — a guaranteed interview topic ("how do you test your code?").

## Python / FastAPI — Pytest
```python
# tests/test_routes.py
import pytest
from fastapi.testclient import TestClient
from app.main import app

client = TestClient(app)

@pytest.fixture                      # reusable setup
def sample_payload():
    return {"sku": "GEN-1", "quantity": 5}

def test_create_part(sample_payload):
    r = client.post("/parts", json=sample_payload)
    assert r.status_code == 201
    assert r.json()["sku"] == "GEN-1"

@pytest.mark.parametrize("qty,ok", [(5, True), (-1, False)])   # table-driven
def test_validation(qty, ok):
    r = client.post("/parts", json={"sku": "X", "quantity": qty})
    assert (r.status_code == 201) == ok
```
- **Layers:** unit (pure logic) → integration (DB/API) → e2e (full flow).
- **Mock external APIs** (ServiceTitan/Google) with **`respx`**/**`responses`** or `monkeypatch` —
  never hit live APIs in tests.
- **Test DB:** spin a throwaway Postgres (testcontainers) or use a transaction-rollback fixture.
- **Coverage:** `pytest --cov=app --cov-report=term-missing`. You quote "70%+ coverage" — be able
  to run this and show it.
- Libraries: **pytest, pytest-asyncio, pytest-cov, httpx/TestClient, respx, factory_boy, faker.**

## TypeScript / React / Next.js — Jest + Testing Library
```ts
// component.test.tsx
import { render, screen } from "@testing-library/react";
import { AppCard } from "./AppCard";

test("renders app name and launch button", () => {
  render(<AppCard name="Lead Claimer" href="/lead-claimer" />);
  expect(screen.getByText("Lead Claimer")).toBeInTheDocument();
  expect(screen.getByRole("link")).toHaveAttribute("href", "/lead-claimer");
});
```
- **Jest** (or **Vitest**) = runner/assertions; **@testing-library/react** = test from the user's
  POV (query by text/role, not implementation details).
- **Mock modules:** `jest.mock("./api")`. **MSW** (Mock Service Worker) to mock fetch/HTTP.
- **E2E:** **Playwright** (you already use it!) drives a real browser through login + flows.
- Coverage: `jest --coverage`.

## SQL / PostgreSQL
- Test migrations apply cleanly up AND down (**Alembic** `upgrade`/`downgrade`).
- Use **transaction rollback** per test for isolation (each test in its own tx, rolled back after).
- Seed with fixtures/factories; assert on query results.
- Tools: **pgTAP** (in-DB testing), or app-level via SQLAlchemy + pytest.

## Bash / scripts
- **`bats`** (Bash Automated Testing System) for shell scripts (your backup/RAID scripts):
```bash
@test "backup script produces a dump file" {
  run ./postgres-backup.sh
  [ "$status" -eq 0 ]
  [ -f "$DUMP_FILE" ]
}
```
- Lint with **`shellcheck`** (catches quoting/portability bugs).

## Docker / Compose
- **`docker compose config`** validates compose files; healthchecks act as runtime tests.
- **Testcontainers** (Python/Node/Go/Java) = spin real dependencies in containers during tests.
- In CI, use **service containers** (GitHub Actions `services:`) for Postgres/Redis.

## The interview answer for "how do you test?"
"Test pyramid: lots of fast unit tests, fewer integration tests against a throwaway Postgres, a thin
layer of Playwright e2e on critical flows. External APIs are mocked with respx/MSW so tests are
deterministic. Everything runs in GitHub Actions on every PR, gated on green + coverage. I use
Pytest for Python and Jest + Testing Library for the frontend."
