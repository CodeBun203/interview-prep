# Tutorial: CI/CD with GitHub Actions

> Goal: add a real pipeline to one of your repos that tests on every push and deploys to `washgen`.
> Follow along in `webhook-gw` (Python) and `washgen-portal` (Node) — both real repos you own.

## Mental model
- **CI (Continuous Integration):** every push runs lint + tests automatically, so broken code is
  caught before merge.
- **CD (Continuous Delivery/Deployment):** after tests pass on `main`, automatically build and ship.
- A **workflow** (YAML in `.github/workflows/`) contains **jobs**; each job runs on a **runner**
  (a VM GitHub provides, or a **self-hosted runner** you control); each job has **steps**.

## Step 1 — minimal CI for a Python repo
Create `.github/workflows/ci.yml` in `webhook-gw`:
```yaml
name: CI
on:
  push:
    branches: [main]
  pull_request:                       # run on every PR — this is the "gate"

jobs:
  test:
    runs-on: ubuntu-latest            # GitHub-hosted runner
    steps:
      - uses: actions/checkout@v4     # clone the repo
      - uses: actions/setup-python@v5
        with:
          python-version: "3.12"
      - name: Install deps
        run: |
          python -m pip install --upgrade pip
          pip install -r requirements.txt
          pip install pytest ruff
      - name: Lint
        run: ruff check .
      - name: Test
        run: pytest -q --maxfail=1
```
Push it. Open the repo → **Actions** tab → watch it run. Green check = CI working.

## Step 2 — branch protection (the "gate")
Repo **Settings → Branches → Add rule** for `main`: require the `test` job to pass before merge.
Now nobody (including you) can merge red code. *This is the sentence: "merges are gated on green
tests."*

## Step 3 — Node/TS CI (for `washgen-portal`)
```yaml
name: CI
on: [push, pull_request]
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with: { node-version: "20", cache: "npm" }
      - run: npm ci
      - run: npm run lint --if-present
      - run: npm test --if-present
```

## Step 4 — build the Docker image (still CI)
Add a job that builds your image so a broken Dockerfile is caught too:
```yaml
  build:
    needs: test                       # only build if tests passed
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: docker/setup-buildx-action@v3
      - uses: docker/build-push-action@v6
        with: { context: ., push: false, tags: webhook-gw:ci }
```

## Step 5 — CD: deploy to washgen over SSH (the impressive part)
Add an SSH key as a repo **secret** (`Settings → Secrets and variables → Actions`):
`SSH_HOST` (your Tailscale IP or a deploy host), `SSH_USER`, `SSH_KEY`.
```yaml
  deploy:
    needs: build
    if: github.ref == 'refs/heads/main'   # deploy only from main
    runs-on: ubuntu-latest                # (or a self-hosted runner ON washgen — even better)
    steps:
      - uses: appleboy/ssh-action@v1
        with:
          host: ${{ secrets.SSH_HOST }}
          username: ${{ secrets.SSH_USER }}
          key: ${{ secrets.SSH_KEY }}
          script: |
            cd /home/admin/applications/webhook-gw
            git pull
            docker compose up -d --build
```
> **Pro version:** register a **self-hosted runner** on `washgen` itself (Settings → Actions →
> Runners). Then "deploy" is just a local `docker compose up -d` with no SSH/secret exposure — and
> you can honestly say "I run a self-hosted GitHub Actions runner on the production host."

## Writing tests (so the CI has something to gate on)
- **Python:** `pytest`. Put tests in `tests/test_*.py`. Use **fixtures** for setup, **`monkeypatch`**
  or `responses`/`respx` to mock external HTTP, `pytest.mark.parametrize` for table-driven tests.
  Coverage: `pip install pytest-cov` → `pytest --cov=app --cov-report=term-missing`.
- **JS/TS:** `jest` (or `vitest`). `*.test.ts`. Use `jest.mock()` to stub modules,
  `@testing-library/react` for components. Coverage: `jest --coverage`.

## Common building blocks / "collections" (the GitHub Actions ecosystem)
| Need | Action |
|---|---|
| Clone repo | `actions/checkout` |
| Set up runtime | `actions/setup-python`, `setup-node`, `setup-go`, `setup-java` |
| Cache deps | `actions/cache` (or `cache:` on setup-* actions) |
| Build Docker | `docker/build-push-action`, `docker/setup-buildx-action` |
| Login to registry | `docker/login-action` |
| Upload artifact | `actions/upload-artifact` |
| SSH deploy | `appleboy/ssh-action` |
| Matrix testing | `strategy.matrix` (test across many versions in parallel) |

## Interview soundbites
- "PR opens → lint + Pytest/Jest run on a hosted runner; merge is gated on green."
- "Merge to `main` → build the image, then a self-hosted runner on the prod host rolls it out with
  `docker compose up -d`."
- "Secrets live in GitHub Actions encrypted secrets, never in the repo."
- Know: **workflow / job / step / runner / matrix / artifact / secret / gate / `needs` (job deps)**.
