# Walmart Retail Analytics

A university data-engineering project that turns retail datasets into a DuckDB warehouse and interactive analytics.

**Stack:** Python · pandas · DuckDB · React · Vite · Recharts · FastAPI

## Data flow

```text
Raw CSVs → Silver cleaning + staging → Golden dimensions/facts → DuckDB
                                                          → dashboard JSON
```

The project combines customer purchases, weekly store-performance data, and product-catalog data. Analytical views cover revenue, customer behavior, and store performance. These sources have different grains and should be interpreted within their own models.

## Run the pipeline

From the repository root:

```bash
python -m pip install -r requirement.txt
python pipelines/silver/run.py
python pipelines/golden/run_pipeline.py
python pipelines/golden/validate_schema.py
```

Inputs are read from `data/Raw/`. The pipeline writes cleaned data, dimension/fact CSVs, `staging/staging.db`, `database/walmart_analytics.db`, and dashboard JSON exports. The Golden runner rebuilds warehouse tables and executes data-quality checks; inspect its report and exit status.

## Dashboard

```bash
cd WEB
npm install
npm run dev
```

Open <http://localhost:5173>. Run `npm run build` inside `WEB/` to build the frontend.

A separate [FastAPI service](WEB/api/server.py) queries DuckDB:

```bash
python -m pip install -r WEB/api/requirements.txt
python -m uvicorn server:app --app-dir WEB/api --reload --port 8000
```

The optional Groq chart/chat integration is a browser-side demo. Vite's `VITE_*` variables are public in the built application; a deployed secret requires a server-side integration.

## Repository guide

| Path | Purpose |
| :--- | :--- |
| [pipelines/silver/](pipelines/silver/) | Extraction, cleaning, and staging |
| [pipelines/golden/](pipelines/golden/) | Dimensional models and warehouse loading |
| [pipelines/data_quality/](pipelines/data_quality/) | Quality checks and reports |
| [WalmartAPI/](WalmartAPI/) | Optional SerpAPI product collection |
| [WEB/](WEB/) | React analytics and local API |
| [diagrams/](diagrams/) | Architecture and pipeline diagrams |

For model details, see the [Golden architecture](pipelines/golden/ARCHITECTURE.md). Product collection has its own [setup guide](WalmartAPI/ReadME.md) and API-key requirement.

This repository is an academic analytics implementation. Validation output depends on the input snapshot; historical row counts are not a current quality guarantee.
