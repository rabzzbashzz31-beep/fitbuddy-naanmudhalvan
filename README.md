# FitBuddy

FitBuddy is a responsive fitness-plan builder powered by FastAPI. It saves plans in SQLite and can use Google's Gemini API for generated plans and daily tips. Without a Gemini API key, it remains usable with a built-in local plan and tip generator.

## Requirements

- Python 3.11 or newer
- VS Code and the Microsoft Python extension
- A Gemini API key only if you want AI-generated content

## Set up in VS Code on Windows

1. Open the `FitBuddy-AI` folder in VS Code.
2. Open **Terminal > New Terminal** and choose PowerShell.
3. Create and activate a virtual environment:

   ```powershell
   py -m venv .venv
   .\.venv\Scripts\Activate.ps1
   ```

   If `py` is unavailable, use `python -m venv .venv`. If PowerShell blocks activation, run `Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass` in that terminal and activate again, or use `.\.venv\Scripts\python.exe` in place of `python` in the remaining commands.

4. Install dependencies:

   ```powershell
   python -m pip install --upgrade pip
   python -m pip install -r requirements.txt
   ```

5. Copy `.env.example` to `.env`. The app runs without a Gemini key. To use Gemini, add your key to `GEMINI_API_KEY`. Set `SESSION_SECRET` to a private random value; one can be generated with:

   ```powershell
   python -c "import secrets; print(secrets.token_urlsafe(32))"
   ```

   Set `ADMIN_PASSWORD` to require sign-in before accessing the plan builder, AI tip, or saved plans. Leave it blank to disable the login gate during local development.

   Keep `.env` private. The default SQLite database is created as `fitbuddy.db` on first startup.

6. In VS Code, press `Ctrl+Shift+P`, choose **Python: Select Interpreter**, and select `.venv\Scripts\python.exe`.
7. Start the app:

   ```powershell
   python -m uvicorn app.main:app --reload
   ```

8. Open <http://127.0.0.1:8000>. Interactive API documentation is at <http://127.0.0.1:8000/docs>.

## Configuration

| Variable | Purpose | Default |
| --- | --- | --- |
| `APP_NAME` | Application name | `FitBuddy` |
| `DEBUG` | Debug setting | `false` |
| `DATABASE_URL` | SQLAlchemy database URL | `sqlite:///./fitbuddy.db` |
| `SESSION_SECRET` | Cookie-signing secret | Development placeholder |
| `ADMIN_PASSWORD` | Optional password protecting the workspace and plan APIs | Empty (no login gate) |
| `GEMINI_API_KEY` | Optional Google Gemini API key | Empty (local generation) |
| `GEMINI_PLAN_MODEL` | Gemini model for plans | `gemini-2.5-flash` |
| `GEMINI_TIP_MODEL` | Gemini model for tips | `gemini-2.5-flash` |

Gemini failures fall back to local generation. The app sends the user's plan preferences and notes to Gemini when a key is configured, so do not enter sensitive health information.

## API

- `GET /api/health` checks that the service is responding.
- `GET /api/auth/status`, `POST /api/auth/login`, and `POST /api/auth/logout` manage the optional admin session.
- `GET /api/tip` returns a daily fitness tip.
- `POST /api/plans` builds and saves a plan.
- `GET /api/plans` lists the 20 most recent plans.
- `GET /api/plans/{id}` returns a saved plan.
- `GET /docs` provides interactive OpenAPI documentation.

## Test

With the virtual environment active, run:

```powershell
python -m pytest
```

Tests use an isolated temporary SQLite database and do not call Gemini.

## Notes

FitBuddy provides general fitness guidance, not medical advice. Review the generated movements and adjust for your situation; stop if an exercise causes pain and seek qualified advice for health concerns.