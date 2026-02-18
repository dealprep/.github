# DealPrep

Automated grocery deal tracking. A cron worker periodically triggers the scraper to collect flyer data from supported retailers.

## Repositories

| Repo | Description | Tech |
|------|-------------|------|
| [main](https://github.com/dealprep/main) | Cron worker that schedules and triggers scrape jobs | Cloudflare Workers, TypeScript |
| [scraper](https://github.com/dealprep/scraper) | REST API that scrapes retailer flyers and returns structured product data | Python, FastAPI, Crawl4AI, Playwright |

## Architecture

```
Cloudflare Cron Trigger
        │
        ▼
┌───────────────────┐         ┌───────────────────────────┐
│   main (Worker)   │──POST──▶│   scraper (FastAPI)       │
│  cron.dealprep.app│         │  Loblaws, ...             │
└───────────────────┘         └───────────────────────────┘
```

## Scraper API — `dealprep/scraper`

Runs on port `8000`. Docs available at `/docs` (Swagger) and `/redoc`.

| Method | Endpoint | Description |
|--------|----------|-------------|
| `GET` | `/` | API info |
| `GET` | `/health` | Health check |
| `POST` | `/scrape` | Scrape any URL, returns markdown content |
| `POST` | `/scrape/loblaws` | Scrape Loblaws flyer favourites, returns structured product list |

### Tech Stack

- **FastAPI** — web framework
- **Uvicorn** — ASGI server
- **Crawl4AI** — web scraping framework
- **Playwright / Patchright** — browser automation
- **BeautifulSoup4** — HTML parsing
- **Pydantic** — data validation
- **Docker** — containerized deployment

## Cron Worker — `dealprep/main`

Runs on Cloudflare Workers. Triggers scrape jobs on a schedule.

| Method | Endpoint | Description |
|--------|----------|-------------|
| `GET` | `/healthcheck` | Health check |
| `POST` | `/trigger` | Manually trigger a scrape job |
