# CLAUDE.md

Behavioral guidelines to reduce common LLM coding mistakes. Merge with project-specific instructions as needed.

**Tradeoff:** These guidelines bias toward caution over speed. For trivial tasks, use judgment.

## 1. Think Before Coding

**Don't assume. Don't hide confusion. Surface tradeoffs.**

Before implementing:
- State your assumptions explicitly. If uncertain, ask.
- If multiple interpretations exist, present them - don't pick silently.
- If a simpler approach exists, say so. Push back when warranted.
- If something is unclear, stop. Name what's confusing. Ask.

## 2. Simplicity First

**Minimum code that solves the problem. Nothing speculative.**

- No features beyond what was asked.
- No abstractions for single-use code.
- No "flexibility" or "configurability" that wasn't requested.
- No error handling for impossible scenarios.
- If you write 200 lines and it could be 50, rewrite it.

Ask yourself: "Would a senior engineer say this is overcomplicated?" If yes, simplify.

## 3. Surgical Changes

**Touch only what you must. Clean up only your own mess.**

When editing existing code:
- Don't "improve" adjacent code, comments, or formatting.
- Don't refactor things that aren't broken.
- Match existing style, even if you'd do it differently.
- If you notice unrelated dead code, mention it - don't delete it.

When your changes create orphans:
- Remove imports/variables/functions that YOUR changes made unused.
- Don't remove pre-existing dead code unless asked.

The test: Every changed line should trace directly to the user's request.

## 4. Goal-Driven Execution

**Define success criteria. Loop until verified.**

Transform tasks into verifiable goals:
- "Add validation" → "Write tests for invalid inputs, then make them pass"
- "Fix the bug" → "Write a test that reproduces it, then make it pass"
- "Refactor X" → "Ensure tests pass before and after"

For multi-step tasks, state a brief plan:
```
1. [Step] → verify: [check]
2. [Step] → verify: [check]
3. [Step] → verify: [check]
```

---

## Project: Portfolio Tracker

Investment portfolio tracker with secure authentication and Google Authenticator 2FA.

### Stack
- **Backend:** Python, FastAPI, SQLAlchemy, Pydantic, JWT, bcrypt, TOTP
- **Frontend:** Vanilla HTML/JS (`frontend/index.html`, `frontend/dashboard.html`)
- **DB:** SQLite (local) / configurable via env
- **Deploy:** Render (auto-deploy on push to GitHub)
- **Prices:** Alpha Vantage API (25 req/day free tier)

### Project Structure
```
portfolio-tracker/
├── backend/
│   ├── main.py          # FastAPI app + auth routes
│   ├── models.py        # SQLAlchemy models (User, Holding, Transaction)
│   ├── schemas.py       # Pydantic schemas
│   ├── database.py      # DB setup + admin user init
│   ├── auth.py          # JWT, bcrypt, TOTP
│   ├── api_prices.py    # Alpha Vantage integration
│   └── .env.example     # Env var template
├── frontend/
│   ├── index.html       # Login + 2FA setup
│   └── dashboard.html   # Protected dashboard
└── CLAUDE.md
```

### Key env vars (never hardcode)
- `ALPHA_VANTAGE_API_KEY`
- `JWT_SECRET`
- `ADMIN_EMAIL` / `ADMIN_PASSWORD`
- `CORS_ORIGINS`
- `ENVIRONMENT`

### Running locally
```bash
cd backend
pip install -r requirements.txt
cp .env.example .env  # fill in keys
python main.py        # or: uvicorn main:app --reload
```

### Security rules
- API keys and secrets always via env vars
- CORS locked to specific origins
- Passwords hashed with bcrypt
- All inputs validated via Pydantic schemas
- JWT tokens expire in 24h
