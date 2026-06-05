# AI-Assisted Engineering Best Practices
## Working with AI at FAANG Standards

---

## Why This Matters for Your Interviews

Every company you are currently interviewing with has explicitly called this out:

- **Snowflake:** "AI-native thinkers... treating AI as a high-trust collaborator that is core to how you solve problems"
- **Apple:** "Familiarity with prompt engineering and AI-assisted developer workflows"
- **Amazon Leo:** "Leverage and contribute to the development of GenAI and AI-powered tools to enhance development productivity"

These companies are not looking for engineers who occasionally use ChatGPT to fix a bug. They want engineers who have internalized AI as a fundamental part of how they work, the same way engineers in 2010 internalized version control. The expectation is that you are faster, more thorough, and produce higher quality output because of how you use these tools, not in spite of needing them.

---

## The Right Mental Model

Before anything about tools, get this right.

**AI is a pair programmer, not a code generator.**

A code generator is something you point at a problem and wait for an answer. A pair programmer is someone you think alongside. The difference is that with a pair programmer, you both own the output. You review what they write, push back when something is wrong, and take responsibility for everything that ships.

If you treat AI as a code generator, you will:
- Ship bugs you do not understand
- Fail technical interviews when asked about code on your resume
- Produce inconsistent, hard-to-maintain codebases
- Get caught out when the AI confidently produces wrong answers

If you treat AI as a pair programmer, you will:
- Move significantly faster on boilerplate and pattern work
- Write more thorough tests because the AI surfaces edge cases you miss
- Understand your own code better because explaining it to AI forces clarity
- Build things that would take three times as long solo

**The rule that matters most: never commit code you cannot explain line by line.**

FAANG interviewers will read your resume and ask you to walk through systems you built. If the answer is "the AI wrote it and it worked," you will not get the job. If the answer is "I used AI to scaffold this, then modified it to handle our specific edge cases around X and Y," that is a strong answer.

---

## Understanding Your Tools

### Cursor

Cursor is an AI-powered code editor built on VS Code. It has full access to your entire codebase at once and operates inside your development environment.

**What it is best at:**
- Tab autocomplete that understands context across your whole file
- Inline edits (Ctrl+K) to modify a specific block of code with a natural language instruction
- The Composer agent (Ctrl+Shift+I) for multi-file tasks: "add pagination to all API endpoints," "refactor this module to use async/await"
- Chat (Ctrl+L) for asking questions about your own codebase: "what does this function return when the input is empty?"
- Fast iteration inside a file you are actively working on

**When to reach for Cursor:**
- You are writing code and want intelligent autocomplete
- You want to make a targeted change to specific lines with a natural language instruction
- You want to ask a question about a specific file or function
- You are refactoring within a bounded scope

### Claude Code

Claude Code is a CLI agent that runs in your terminal. It operates at the level of your entire project and can read files, write files, run commands, search the codebase, and chain multi-step operations.

**What it is best at:**
- Tasks that span many files: "update all API handlers to log request timing"
- Research tasks: "find every place in this codebase that calls the ServiceTitan API"
- Writing new files from scratch based on context it reads from existing ones
- Running tests, interpreting failures, and suggesting fixes in a loop
- Conversations that require understanding the project holistically
- Tasks where you want a full explanation alongside the code

**When to reach for Claude Code:**
- You want to understand how a large codebase is structured before writing anything
- You are building a new feature that touches many parts of the system
- You want to generate a full file with proper structure based on existing patterns
- You want to run commands and interpret their output in context

### Using Both Together

The most effective pattern is:
1. Use Claude Code to plan, scaffold, and understand. It reads broadly.
2. Use Cursor to iterate and refine inside specific files. It edits precisely.

Example workflow for adding a new API endpoint:
1. Open Claude Code and ask: "Read the existing API handlers and describe the patterns they follow. Then scaffold a new handler for POST /subscriptions."
2. Claude Code reads the codebase, identifies patterns, and writes a file that matches them.
3. Switch to Cursor. Open the new file and use inline edits to adjust specific parts.
4. Use Cursor chat to ask: "Are there edge cases in this handler I am not handling?"

---

## Files to Create Before Writing a Line of Code

This is where most engineers fail with AI. They open a new project and immediately start asking the AI to write code without giving it any context. The AI produces generic output that does not match your project's patterns, naming conventions, or constraints.

The fix is to create context files first. These tell the AI everything it needs to produce output that fits your project.

---

### CLAUDE.md

This is Claude Code's primary context file. It lives in the root of your project and is automatically read at the start of every session. This is not documentation for humans. It is a briefing document for AI.

**What to put in it:**

```markdown
# Project Name

## What this is
One paragraph describing the project: what it does, who uses it, what problem it solves.

## Architecture
- Backend: Python 3.12, FastAPI, SQLAlchemy 2.0, Alembic
- Frontend: React 18, TypeScript, TailwindCSS
- Database: PostgreSQL 15, Redis for caching
- Infrastructure: Docker, GitHub Actions, deployed on AWS ECS

## Project structure
src/
  api/          # FastAPI route handlers
  models/       # SQLAlchemy models
  services/     # Business logic layer
  schemas/      # Pydantic request/response schemas
  tests/
    unit/
    integration/

## Coding conventions
- All functions must have type hints
- Use snake_case for Python, camelCase for TypeScript
- API responses always use the standard envelope: {"data": ..., "error": null}
- Every new module needs a corresponding test file
- No print statements in production code — use the logger

## Commands
- Run tests: pytest tests/ -v
- Run linter: ruff check .
- Start dev server: uvicorn src.main:app --reload
- Run migrations: alembic upgrade head

## Things to never do
- Never hardcode credentials or API keys
- Never skip input validation on API endpoints
- Never use sync requests library in async handlers — use httpx

## Current focus
[Update this as the project evolves — what is being actively worked on]
```

The more specific CLAUDE.md is, the better Claude Code's output matches your project. A generic CLAUDE.md produces generic code. A specific one produces code that looks like it was written by a developer who has been on the project for months.

---

### .cursor/rules/ (Cursor Rules)

Cursor uses its own rule files stored in `.cursor/rules/`. Each file is a `.mdc` file (markdown with metadata) that gives Cursor persistent instructions. You can have multiple rule files for different concerns.

**How to create them:**
In Cursor, open the Command Palette (Ctrl+Shift+P), search "New Cursor Rule," and give it a name.

**Example: general.mdc**
```markdown
---
description: General coding standards for this project
globs: ["**/*.py", "**/*.ts", "**/*.tsx"]
---

# General Standards

- Always include type hints in Python functions
- Never use `any` type in TypeScript — use proper types or generics
- Use descriptive variable names — no single letters except loop counters
- Functions should do one thing — if you find yourself writing "and" in a description, split it
- Write the happy path first, then handle errors
- Default to returning early rather than deeply nested if blocks
```

**Example: testing.mdc**
```markdown
---
description: Testing standards
globs: ["**/test_*.py", "**/*.test.ts"]
---

# Testing Standards

- Use pytest fixtures for all shared state — never module-level mutable variables
- Use pytest.approx for all float comparisons
- Always use pytest.raises with match= to verify error messages
- Test one concept per test function
- Follow AAA pattern: Arrange, Act, Assert
- Mock at the boundary — mock HTTP calls and database calls, not internal functions
- Test names should read as documentation: test_[function]_[scenario]_[expected]
```

**Example: api.mdc**
```markdown
---
description: API handler patterns
globs: ["**/api/**/*.py", "**/routes/**/*.py"]
---

# API Handler Standards

- All endpoints must have a Pydantic request schema and response schema
- Use HTTPException for error responses, not bare return with status codes
- Log all incoming requests at DEBUG level and all errors at ERROR level
- Never access the database directly in a handler — call a service function
- All list endpoints must support pagination via page and page_size query params
```

---

### .gitignore Considerations

Add AI tool config files that contain personal or sensitive context:
```
.cursor/
.claude/     # contains session data, not project rules
```

But do commit the project context files:
```
# Commit these — they are project documentation for AI
CLAUDE.md
.cursor/rules/
```

---

## What to Use AI For

### High-value, low-risk tasks

**Boilerplate and scaffolding**
Tests, API schemas, database models, migration files, CI pipeline YAML, Docker configurations. These are pattern-heavy and repetitive. AI is excellent at them. You still review every line.

Example prompt (good):
```
Read src/api/orders.py and src/schemas/orders.py to understand the existing patterns.
Then create src/api/subscriptions.py and src/schemas/subscriptions.py following
the exact same structure. The subscription model has fields: id, user_id, plan
(one of: basic, premium, family), status (active, cancelled, expired),
created_at, and cancelled_at (nullable).
```

**Test generation**
Give the AI a function and ask it to write a complete test suite. It is very good at identifying edge cases you would miss. Then read every test assertion before committing.

Example prompt (good):
```
Read this function carefully: [paste function]

Write a complete pytest test suite for it. I want:
1. Happy path tests for every documented input type
2. Edge cases: empty input, None, boundary values
3. Error cases for every exception the function can raise
4. Use pytest.parametrize where tests share the same structure
5. Use pytest.approx for any float assertions

After writing the tests, list any edge cases you could not cover because
the function's behavior is ambiguous.
```

The last instruction is important. It makes the AI tell you what it does not know rather than silently making assumptions.

**Explaining unfamiliar code**
When you join a new codebase (which you will at any of these companies), AI is exceptional at explaining what code does, why it was written that way, and what dependencies it has.

Example prompt (good):
```
Read src/payment/processor.py. Explain:
1. What this module does at a high level
2. What each public function does and what it returns
3. What external services it calls and when
4. What could go wrong and what error handling exists
5. What I would need to understand to safely modify this module
```

**Refactoring**
AI is good at mechanical refactoring: renaming things, extracting functions, converting sync to async, adding type hints to an untyped codebase. Less reliable for architectural refactoring where judgment is needed.

**Debugging assistance**
Paste an error traceback and the relevant code. AI is good at spotting the connection. But form your own hypothesis first before asking — this keeps you sharp.

**Code review**
Ask the AI to review your own code before you submit a PR. It will catch things you missed.

Example prompt:
```
Review this code for:
1. Correctness bugs — anything that will produce wrong output
2. Missing error handling — places where exceptions are possible but not caught
3. Security issues — injection, unvalidated input, exposed secrets
4. Performance problems — N+1 queries, unnecessary loops, blocking calls
5. Anything that would fail in an edge case I probably did not test

Be specific. Point to line numbers. Do not flag style issues.
```

---

## What NOT to Use AI For (or Use with Heavy Skepticism)

### Security-critical code

OAuth flows, JWT validation, password hashing, permission checks, encryption. AI can produce code that looks correct but has subtle vulnerabilities. Always cross-reference security-sensitive code against official documentation and security advisories.

Example of AI getting it wrong: AI may generate JWT validation that checks the signature but forgets to verify the `exp` (expiration) claim. The code looks fine and passes tests you write, but tokens never expire in production.

**Rule:** For any authentication, authorization, or cryptographic code, write it yourself using official library documentation. Use AI to explain what you wrote, not to write it.

### Architecture decisions

"Should I use microservices or a monolith?" "Should I use Redis or PostgreSQL for this?" "What's the right database schema for this domain?"

AI will give you an answer. The answer will sound confident and reasonable. It may be completely wrong for your specific constraints. Architecture requires knowing things AI does not: your team's expertise, your expected scale, your operational budget, your existing infrastructure.

**Rule:** Use AI to list the tradeoffs of options you are already considering. Do not use it to make the decision.

### Code you plan to ship without understanding

If you paste AI output directly into a file without reading it, you own a codebase you do not understand. This creates:
- Bugs you cannot debug because you do not know how the code works
- Security vulnerabilities you cannot spot because you did not write the security model
- Interview failures when asked to explain your work
- Production incidents where you cannot reason about what went wrong

**Rule:** Read every line. If you do not understand something, ask the AI to explain it. If the explanation does not make sense, rewrite that section yourself.

### Performance-critical paths

AI does not know your production data distribution, your hardware constraints, or your observed bottlenecks. It will write correct code that may be catastrophically slow at your scale. Always profile before optimizing, and do not trust AI estimates of time complexity without verifying them.

---

## Prompting Patterns That Work

### Give full context upfront

**Weak prompt:**
```
Write a function to process subscriptions
```

**Strong prompt:**
```
I am building a subscription management service for a digital media platform.
Subscriptions have a plan (basic, premium, family), a status (active, cancelled,
expired), and a billing cycle (monthly, annual). A subscription expires when
its current period ends and it has not been renewed.

Write a function called process_renewal_batch(subscriptions: list[Subscription])
that takes a list of subscriptions due for renewal today, attempts to charge
each one, updates the status based on the charge result, and returns a summary
dict with keys: renewed (count), failed (count), errors (list of error messages).

The charge function is already implemented: charge_payment_method(subscription_id)
which returns True on success and raises PaymentDeclinedError on failure.

Use Python 3.12 type hints. No external libraries. Raise ValueError if the
input list is empty.
```

The second prompt produces usable code on the first try. The first produces something you will rewrite three times.

### Specify what you do not want

```
Refactor this function to be more readable. Do NOT change the logic, add error
handling, or add logging. Only improve naming and structure.
```

Without constraints, AI will "improve" your code in ways you did not ask for and cannot easily diff.

### Ask for reasoning

```
Write a rate limiter class. After writing it, explain why you chose this
data structure and what the time complexity of each operation is.
```

This forces the AI to be honest about tradeoffs and helps you evaluate whether the approach is right.

### Ask what could go wrong

```
I am about to deploy this database migration to production. What could go wrong?
What should I check before running it? Is there any scenario where this is
not safely reversible?
```

AI is good at adversarial thinking when explicitly asked for it.

### Incremental prompts, not one-shot prompts

Do not ask for the entire feature in one prompt. Build it in layers:

1. "Scaffold the class with the interface I described"
2. "Implement the core logic of the process() method"
3. "Add error handling for these specific failure modes"
4. "Write tests for the happy path"
5. "Now write tests for the edge cases"
6. "Review the full implementation for correctness"

This keeps each AI response focused and reviewable.

---

## Git Workflow with AI

### Commit before and after AI changes

Before asking AI to refactor anything, commit your current state:
```bash
git add -p   # stage selectively, review every hunk
git commit -m "before AI refactor: extract subscription logic"
```

This gives you a clean diff of what the AI changed and a rollback point if the output is wrong.

### Review the diff before committing

After AI writes or modifies code:
```bash
git diff     # read every line before staging anything
git add -p   # stage interactively, hunk by hunk
```

`git add -p` is one of the most important habits for AI-assisted development. It forces you to read every change before it goes into a commit.

### Write your own commit messages

Do not let AI write commit messages. A commit message is a historical record of your judgment about why a change was made. AI-written messages are generic and lose that context.

### Keep AI-generated code in dedicated commits when it helps

If you scaffolded an entire test file with AI, a single commit labeled "scaffold subscription tests via AI, reviewed and modified" is honest and traceable. Mixed commits where some lines are AI-written and some are hand-written are fine — you reviewed them all.

---

## What FAANG Companies Actually Expect

### Snowflake's bar: "AI-native"

Snowflake used this phrase deliberately. To them it means:
- You reach for AI before manual effort for any automatable task
- You use AI to increase the surface area of what you can build, not just to write faster
- You can articulate where AI helped and where your own judgment was essential
- You are curious about AI capabilities and explore new ones proactively

In an interview, if they ask about your development process, talking about how you use Cursor and Claude Code to maintain 70%+ test coverage, scaffold new services that match existing patterns, and do code review before submitting PRs — that is a strong answer.

### Amazon's bar: operational excellence through tooling

Amazon cares about reliability. They want engineers who use AI to make their systems more reliable, not just faster to ship. This means:
- Using AI to generate more comprehensive test cases, not to skip testing
- Using AI to generate monitoring and alerting code alongside feature code
- Using AI to review your own code for production issues before committing

### Apple's bar: applied AI in tools

The Apple SDET role explicitly asks for "AI capabilities to make tools smarter." This means:
- Integrating LLM APIs to add intelligence to internal developer tools
- Prompt engineering as a first-class skill
- AI-assisted workflows that other engineers use, not just yourself

For this role specifically, being able to say "I built a system that uses an LLM to classify test failures and suggest root causes" or "I integrated an LLM to generate test cases from API schemas" is exactly what they are hiring for.

---

## Practical SDET-Specific AI Patterns

### Generate test cases from a function signature

```
Here is the function signature and docstring:

def process_payment(
    amount: float,
    currency: str,
    payment_method_id: str,
    idempotency_key: str
) -> PaymentResult:
    """
    Process a payment. Raises PaymentDeclinedError if the card is declined.
    Raises InvalidCurrencyError if the currency is not supported.
    Returns PaymentResult with status and transaction_id.
    """

Generate a complete list of test cases I should write. Do not write the code yet.
Just list the scenarios, organized by category: happy path, edge cases, error cases,
boundary conditions.
```

AI generates the test plan. You evaluate it, add anything missing, then ask it to write the code.

### Find gaps in your existing test suite

```
Here is a function and its existing test suite: [paste both]

What scenarios are NOT covered by these tests? What inputs or conditions could
cause unexpected behavior that the tests would not catch?
```

This is one of the highest-value uses of AI in SDET work. It acts as a second reviewer looking specifically for gaps.

### Generate realistic test data

```
I need realistic test data for a digital media purchase system. Generate 20
purchase records as Python dicts with these fields: id (UUID format), user_id,
item_type (song/album/app/subscription), amount (realistic for the item type),
currency (mix of USD, GBP, EUR, JPY), storefront_region, timestamp.

Include edge cases: free items (amount=0), very small amounts (0.99), large amounts
(multi-year subscriptions), purchases at midnight, purchases with non-ASCII
characters in metadata.
```

### Mock complex external systems

```
I need to test code that calls the Stripe API to process refunds. I do not want
to call the real Stripe API in tests. Generate a MagicMock object that:

1. Simulates successful refunds: stripe.Refund.create() returns a mock object
   with id, amount, status="succeeded"
2. Simulates insufficient funds: raises stripe.error.InvalidRequestError
3. Simulates network errors: raises stripe.error.APIConnectionError
4. Tracks how many times each endpoint was called so I can assert on call counts

Show me how to set up this mock and use it in three different test scenarios.
```

---

## Red Flags: Signs You Are Using AI Wrong

**You cannot explain your own code in an interview.**
If you cannot walk through a system you built, you built it with AI and did not learn from it. The fix is to read everything and ask the AI to explain anything you do not understand until you could rewrite it from scratch.

**Your AI-generated code passes tests but has bugs in production.**
This usually means your tests are also AI-generated and share the same blind spots. Test generation and implementation generation by the same model will share assumptions. Write at least some tests by hand.

**You use AI to fix the same bug multiple times.**
AI fixes symptoms, not causes. If you are asking AI to fix the same category of bug repeatedly, you need to understand the root cause yourself rather than patching it each time.

**Your codebase has inconsistent style.**
AI from different sessions will write code slightly differently. If you are not reviewing and normalizing, the codebase becomes inconsistent. Cursor Rules and CLAUDE.md fix this by giving the AI a consistent set of constraints.

**You accept the first output.**
The first AI response is often correct but suboptimal. Always ask: "What would you change about this approach to make it more robust?" or "What are the tradeoffs of this implementation?"

---

## Daily Workflow Checklist

**Before starting a new feature:**
- [ ] Is CLAUDE.md up to date with current architecture?
- [ ] Do Cursor rules cover the files I will be touching?
- [ ] Have I read the existing code the feature will interact with?
- [ ] Have I committed the current state before asking AI to make changes?

**While using AI to write code:**
- [ ] Did I give enough context in the prompt?
- [ ] Did I ask for reasoning, not just output?
- [ ] Am I reading every line of the output?
- [ ] Do I understand what every function the AI wrote does?

**Before committing:**
- [ ] Did I run `git diff` and read every change?
- [ ] Did I run the test suite?
- [ ] Did I ask AI to review my own changes for bugs?
- [ ] Can I explain every line to an interviewer?

**Before submitting a PR:**
- [ ] Did I ask AI to look for edge cases I missed?
- [ ] Did I ask AI to identify missing test coverage?
- [ ] Did I write my own commit messages?

---

## Summary: The Standard That Gets You Hired

The engineers these companies want can answer yes to all of these:

1. Do you use AI to move faster without sacrificing quality?
2. Can you explain every line of code in your codebase, including AI-generated code?
3. Do you use AI to find things you missed, not just to do things you would have done anyway?
4. Do you give AI enough context to produce project-consistent output?
5. Do you review AI output as critically as you would review a junior engineer's PR?
6. Do you know where AI falls short and compensate with your own judgment?

The engineers these companies do not want are the ones who produce AI-assisted spaghetti quickly and call it productivity. The bar is not "uses AI" — it is "uses AI well."
