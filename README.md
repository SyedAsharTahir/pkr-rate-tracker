# PKR Rate Tracker

A data pipeline for the Pakistani rupee, built on State Bank of Pakistan (SBP) data.

**Question this project answers:** How big is the gap between the official and open-market dollar rate, and how reliable is each data source?

**Status:** work in progress. Database setup is done. Ingestion is next.

## What the pipeline does

1. **Ingest:** load SBP's daily exchange rates into PostgreSQL, first from CSV downloads and later from the EasyData API.
2. **Validate:** run data-quality checks, such as missing business days, odd values, and a dollar-peg test on the dirham (the AED is pegged to the USD, so USD/PKR divided by AED/PKR should stay near 3.6725).
3. **Reconcile:** compare the daily rates against SBP's own monthly averages and month-end rates.
4. **Model:** build dbt tables on top of the raw data.
5. **Schedule:** run the pipeline regularly with Airflow, and alert when no new data has arrived.
6. **Report:** a thin dashboard showing the official vs open-market gap and any flagged issues.

## Stack

- Python (pandas, psycopg2, SQLAlchemy, python-dotenv)
- PostgreSQL 16 in Docker Compose
- Planned: dbt, Airflow, a dashboard layer

## Setup

1. Create your env file and set your own password:

   ```bash
   cp .env.example .env
   ```

2. Start the database and check the connection:

   ```bash
   docker compose up -d
   docker compose exec db psql -U pkr -d pkr -c "SELECT 1;"
   ```

3. Create a virtual environment and install dependencies:

   ```bash
   python3 -m venv .venv
   source .venv/bin/activate
   pip install -r requirements.txt
   ```

## Configuration

All settings live in `.env` (not committed). See `.env.example`:

| Variable | Purpose |
|---|---|
| `POSTGRES_USER` | Database user |
| `POSTGRES_PASSWORD` | Database password |
| `POSTGRES_DB` | Database name |

## Roadmap

- [x] Postgres in Docker Compose, env config, repo setup
- [ ] Ingest SBP daily rates from CSV into a raw schema
- [ ] Validation checks with results stored in a table
- [ ] Reconciliation against SBP monthly averages and month-end rates
- [ ] dbt models
- [ ] Airflow scheduling and missing-data alerts
- [ ] Dashboard
- [ ] Switch ingestion to the EasyData API

## Data sources

- State Bank of Pakistan: daily exchange rates, monthly averages, month-end rates
- SBP EasyData API (planned)

## Useful commands

```bash
docker compose stop        # stop the DB, keep data
docker compose down        # remove the container, keep data
docker compose down -v     # remove the container and wipe data
```