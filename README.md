# PKR Rate Tracker

A small ETL pipeline that fetches PKR exchange rates from a public API and stores them in PostgreSQL, building up a history over time.

**Status:** work in progress. The database setup is done. Extract and load scripts are next.

## Stack

- Python 3.14 (pandas, psycopg2, SQLAlchemy, python-dotenv)
- PostgreSQL 16 in Docker Compose

## Setup

1. Clone the repo and enter the folder.

2. Create your env file and set your own password:

   ```bash
   cp .env.example .env
   ```

3. Start the database:

   ```bash
   docker compose up -d
   docker compose exec db psql -U pkr -d pkr -c "SELECT 1;"
   ```

4. Create a virtual environment and install dependencies:

   ```bash
   python3 -m venv .venv
   source .venv/bin/activate
   pip install -r requirements.txt
   ```

## Configuration

All settings live in `.env` (not committed). See `.env.example` for the required variables:

| Variable | Purpose |
|---|---|
| `POSTGRES_USER` | Database user |
| `POSTGRES_PASSWORD` | Database password |
| `POSTGRES_DB` | Database name |

## Planned pipeline

1. **Extract:** fetch the latest USD/PKR rate from a free exchange-rate API
2. **Transform:** validate and clean the response
3. **Load:** insert into the `rates` table in Postgres
4. **Schedule:** run daily so history accumulates

## Useful commands

```bash
docker compose stop        # stop the DB, keep data
docker compose down        # remove the container, keep data
docker compose down -v     # remove the container and wipe data
```