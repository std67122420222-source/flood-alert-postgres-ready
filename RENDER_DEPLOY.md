# Deploy FloodAlert to Render with persistent PostgreSQL

## 1) Push this project to GitHub

Push the contents of this folder to the root of your GitHub repository. Do not upload `.env` or any real API token.

## 2) Create a PostgreSQL database

Recommended for a persistent free-tier-style classroom deployment: create a PostgreSQL project in Supabase, then use its connection string. Supabase provides the connection string from **Connect → Session pooler / Direct connection** depending on your network and plan.

For a Render-hosted Flask service, store the connection string as the `DATABASE_URL` environment variable.

## 3) Create / update the Render Web Service

Render Dashboard → Web Service → connect the GitHub repository.

- Runtime: Python
- Build Command: `pip install -r requirements.txt`
- Start Command: `python -m gunicorn --bind 0.0.0.0:$PORT --workers 1 --timeout 120 run:app`
- Health Check Path: `/healthz`
- Plan: Free

## 4) Environment Variables

Set these in Render:

- `SECRET_KEY` — a strong random secret
- `DATABASE_URL` — your PostgreSQL connection string
- `TMD_ACCESS_TOKEN` — your TMD Personal Access Token
- `TMD_WARNING_URL` — the warning API URL used by the project

For an external PostgreSQL URL, keep SSL enabled when the provider includes it (for example `?sslmode=require`). Do not commit the database URL or API token to GitHub.

## 5) First deploy

On the first PostgreSQL-backed boot, the app runs `db.create_all()` and creates the project tables automatically. Existing user accounts or tracked areas in a previous SQLite deployment are not copied automatically; they must be recreated in the new PostgreSQL database.

## 6) Why the data now persists

User accounts, tracked areas, forecast data, and warnings are stored in PostgreSQL instead of the Render service filesystem. Restarting or redeploying the web service therefore does not remove the database contents, as long as the external PostgreSQL database remains available.
