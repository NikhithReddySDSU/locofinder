# LocoFinder

A business location recommendation engine that combines demographic, economic, and market data to help entrepreneurs find optimal locations for their business. Powered by AI-driven scoring and local market analysis.

## Quick Start

### Prerequisites

- **Python 3.11+** (backend)
- **Node.js 18+** (frontend)
- **Redis** (optional—gracefully handled if unavailable)

### Local Development (Simple Setup)

1. **Clone and navigate to the project:**
   ```bash
   cd locofinder
   ```

2. **Start the backend** (in one terminal):
   ```bash
   cd backend
   python -m uvicorn app.main:app --reload --host 0.0.0.0 --port 8001
   ```
   - Health check: http://127.0.0.1:8001/health
   - API docs (Swagger UI): http://127.0.0.1:8001/docs

3. **Start the frontend** (in another terminal):
   ```bash
   cd frontend
   npm run dev
   ```
   - Open http://127.0.0.1:3001 in your browser
   - The frontend automatically routes API calls to the backend on port 8001

### Docker Compose (Full Stack)

To run the complete stack with Docker (backend, frontend, Redis):

```bash
cd locofinder
docker compose up --build
```

- Frontend: http://127.0.0.1:3000
- Backend: http://127.0.0.1:8000
- Backend docs: http://127.0.0.1:8000/docs

Press `Ctrl+C` to stop. Use `docker compose down` to clean up containers.

### Reset Dummy Data (Admin Endpoint)

If you need to regenerate the sample location dataset:

```bash
curl -X POST http://127.0.0.1:8001/admin/reset-dummy-data
```

Optional: Specify number of rows (default 10,000):
```bash
curl -X POST http://127.0.0.1:8001/admin/reset-dummy-data?rows=5000
```

---

## Project Structure

```
locofinder/
├── backend/              # FastAPI application (Python)
│   ├── app/
│   │   ├── main.py       # Application entry point
│   │   ├── api/          # API routes (health, locations, scoring, admin)
│   │   ├── db/           # Database & Redis connections
│   │   ├── scoring/      # Scoring engine (normalization, weighting)
│   │   └── services/     # Business logic layer
│   ├── requirements.txt
│   └── pytest.ini
├── frontend/             # Next.js application (React + TypeScript)
│   ├── src/
│   │   ├── pages/        # Next.js pages (dashboard)
│   │   ├── components/   # React components (filters, map, results table)
│   │   ├── hooks/        # Custom hooks (useLocations, useRedux)
│   │   └── store/        # Redux state (filters store)
│   ├── package.json
│   └── next.config.js
├── data-platform/        # Data ingestion & ETL (currently minimal)
├── docs/                 # Architecture, API contracts, methodology
├── infra/                # Docker, Nginx, Terraform configs
└── docker-compose.yml    # Multi-container orchestration
```

---

## Features

### Business Filtering
Select by **business type** (Coffee, Health, Retail, Restaurant, Beauty, General) and **budget tier** ($100k–$1M) to get location recommendations tailored to your business model.

### Intelligent Scoring
Locations are ranked based on:
- **Median Income** (customer spending power)
- **Safety Index** (crime statistics)
- **Market Growth** (population trends)
- **Commercial Rent** (qualitative "Rent Bands": Economical → Value → Mid-range → Premium → High-end)

All scores are normalized and weighted according to business-type profiles defined in the system.

### Score Explainability
View exact feature weights and normalized scores for each ranked location.

### Interactive Map & Results
- Map-based location picker
- Ranked results table (default: top 5 results) with scrollable view
- Detailed location cards showing all metrics

---

## Development

### Backend Setup

```bash
cd backend
pip install -r requirements.txt
```

**Run tests:**
```bash
pytest
```

**Generate dummy data from scratch:**
```bash
python scripts/generate_dummy_data.py
```

### Frontend Setup

```bash
cd frontend
npm install
npm run dev        # Dev server (http://127.0.0.1:3001)
npm run build      # Production build
npm run start      # Production server
```

---

## Key Environment Variables

| Variable | Default | Purpose |
|----------|---------|---------|
| `BACKEND_INTERNAL_URL` | `http://localhost:8001` | Backend API URL (used by frontend proxy) |
| `REDIS_URL` | `redis://localhost:6379` | Redis cache connection |
| `DATABASE_URL` | (In-memory DuckDB) | Analytics database |

---

## Troubleshooting

### Frontend can't reach backend
- Ensure backend is running on port 8001
- Check `next.config.js` for correct `BACKEND_INTERNAL_URL` rewrites
- Verify firewall allows `localhost:8001` access

### Redis not available
- Application gracefully falls back to DuckDB queries (no data loss)
- To use Redis, ensure it's running: `redis-server` or `docker run -d -p 6379:6379 redis`

### Port already in use
- Backend: Change port in uvicorn command (e.g., `--port 8002`)
- Frontend: Use `npm run dev -- -p 3002`
- Docker: Modify port mappings in `docker-compose.yml`

---

## Tech Stack

- **Frontend**: Next.js 14, React 18, TypeScript, Redux
- **Backend**: FastAPI, Uvicorn, Python 3.11
- **Data**: DuckDB (analytics), Parquet (dummy data), Redis (caching)
- **Infrastructure**: Docker, Docker Compose, Nginx

---

## Next Steps

1. Explore sample locations in the dashboard
2. Filter by business type and budget
3. View detailed scoring breakdowns
4. See the matched locations on the interactive map

For questions or contributions, refer to [backend/README.md](backend/README.md) and [frontend/README.md](frontend/README.md) for subsystem details.
