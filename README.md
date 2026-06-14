This system provides:
- **Factory reallocation recommendations** — which products should be made where
- **Shipping route optimization** — lowest-cost, fastest, or balanced delivery paths
- **What-if scenario modeling** — test the impact of adding/removing a facility or lane
- **A real-time dashboard** — live KPI monitoring and one-click recommendation reports

---

## Problem Statement

• Nassau Candy currently assigns products to factories using static rules and legacy processes. This leads to:
- ● Suboptimal shipping distances
- ● High lead times for certain regions
- ● Margin erosion due to logistics inefficiencies
• There is no system to:
- ● Simulate factory–product reassignment scenarios
- ● Quantify the operational impact before execution
- ● Recommend optimal configurations at scale

## 📋 Table of Contents

- [Overview](#overview)
- [Problem Statement](#problem-statement)
- [Solution Architecture](#solution-architecture)
- [Features](#features)
- [Project Structure](#project-structure)
- [Installation](#installation)
- [Usage](#usage)
- [Models & Algorithms](#models--algorithms)
- [Data Pipeline](#data-pipeline)
- [API Reference](#api-reference)
- [Dashboard](#dashboard)
- [Contributing](#contributing)
- [Roadmap](#roadmap)

---

## Overview

Nassau Candy Distributor operates multiple production facilities and serves a wide geographic network of retail accounts. As demand shifts, fuel costs fluctuate, and capacity constraints evolve, manually managing **which factory produces which SKUs** and **how finished goods are routed to customers** becomes increasingly error-prone and costly.


| Suboptimal factory assignments | Excess freight cost, capacity underutilization |
| Manual routing decisions | Delayed shipments, missed SLAs |
| No scenario planning tool | Reactive responses to disruptions |
| Siloed data across WMS, ERP, TMS | Blind spots in end-to-end cost visibility |

**Key Question:** *Given demand forecasts, factory capacities, and freight rates — what is the optimal production allocation and shipment routing strategy for the next 30/60/90 days?*

---

## Solution Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                        DATA SOURCES                             │
│   ERP (SAP/NetSuite)   WMS   TMS   Demand Forecast   Fuel API  │
└──────────────────────────────┬──────────────────────────────────┘
                               │
                    ┌──────────▼──────────┐
                    │   ETL / Data Lake    │
                    │  (src/data/ingest)   │
                    └──────────┬──────────┘
                               │
          ┌────────────────────▼────────────────────┐
          │            OPTIMIZATION ENGINE           │
          │                                          │
          │  ┌─────────────────┐  ┌───────────────┐ │
          │  │ Factory Alloc.  │  │ Route Optimizer│ │
          │  │ (MILP / ILP)    │  │  (VRP / GA)   │ │
          │  └────────┬────────┘  └───────┬───────┘ │
          │           └──────────┬─────────┘         │
          │              ┌───────▼───────┐            │
          │              │  Recommender  │            │
          │              │   Engine      │            │
          │              └───────────────┘            │
          └────────────────────┬────────────────────┘
                               │
               ┌───────────────┼───────────────┐
               │               │               │
      ┌────────▼───┐   ┌───────▼───┐   ┌──────▼──────┐
      │  REST API  │   │ Dashboard │   │  PDF/Excel  │
      │  (FastAPI) │   │ (Streamlit│   │   Reports   │
      └────────────┘   └───────────┘   └─────────────┘
```

---

## Features

### 🏭 Factory Reallocation
- **Multi-factory MILP model** — assigns SKUs to production lines subject to capacity, changeover, and minimum run constraints
- **Cost-aware allocation** — incorporates per-unit production cost, labor rates, and inbound raw material freight
- **Disruption handling** — instantly re-optimizes when a facility goes offline or capacity changes
- **SKU affinity rules** — respects product family groupings and equipment certifications

### 🚚 Shipping Optimization
- **Vehicle Routing Problem (VRP) solver** — Google OR-Tools backend with time-window and load constraints
- **Multi-modal support** — LTL, FTL, parcel, and intermodal lanes
- **Dynamic carrier rate integration** — live or contract rate lookup per lane
- **Zone skipping & consolidation** — automatically identifies consolidation opportunities

### 📊 Scenario Modeling
- Clone any baseline plan and adjust parameters (demand ±%, factory close/open, fuel cost shock)
- Side-by-side KPI comparison (total cost, CO₂, service level, capacity utilization)
- Sensitivity analysis charts for key decision levers

### 📈 Dashboard & Reporting
- Real-time KPI tiles: total freight cost, avg. transit days, factory utilization %
- Geospatial map of active shipping lanes with cost heat overlay
- One-click PDF/Excel recommendation export for S&OP review
- Alert center for SLA breach risk and capacity overloads

---

## Project Structure

```
nassau-optimizer/
│
├── README.md
├── pyproject.toml
├── requirements.txt
├── .env.example
│
├── src/
│   ├── data/
│   │   ├── ingest.py          # ERP/WMS/TMS connectors
│   │   ├── transform.py       # Data cleaning & feature engineering
│   │   ├── validate.py        # Great Expectations data quality checks
│   │   └── schemas.py         # Pydantic data models
│   │
│   ├── models/
│   │   ├── factory_allocator.py    # MILP factory assignment model (PuLP)
│   │   ├── route_optimizer.py      # VRP solver (OR-Tools)
│   │   ├── demand_forecast.py      # Prophet / XGBoost demand forecasting
│   │   ├── cost_calculator.py      # End-to-end landed cost computation
│   │   └── recommender.py          # Synthesizes outputs → ranked recommendations
│   │
│   ├── api/
│   │   ├── main.py            # FastAPI application entry point
│   │   ├── routers/
│   │   │   ├── allocations.py
│   │   │   ├── routes.py
│   │   │   ├── scenarios.py
│   │   │   └── reports.py
│   │   └── middleware.py
│   │
│   └── dashboard/
│       ├── app.py             # Streamlit dashboard
│       ├── pages/
│       │   ├── overview.py
│       │   ├── factory_map.py
│       │   ├── route_planner.py
│       │   └── scenarios.py
│       └── components/        # Reusable UI widgets
│
├── notebooks/
│   ├── 01_eda_demand.ipynb
│   ├── 02_factory_capacity_analysis.ipynb
│   ├── 03_route_baseline.ipynb
│   └── 04_model_validation.ipynb
│
├── tests/
│   ├── test_factory_allocator.py
│   ├── test_route_optimizer.py
│   ├── test_api.py
│   └── fixtures/
│
├── docs/
│   ├── architecture.md
│   ├── data_dictionary.md
│   ├── model_specs.md
│   └── runbook.md
│
└── .github/
    └── workflows/
        ├── ci.yml
        └── deploy.yml
```

---

## Installation

### Prerequisites
- Python 3.11+
- Docker & Docker Compose (for local services)
- Access credentials for Nassau ERP / WMS / TMS (see `.env.example`)

### Quick Start

```bash
# 1. Clone the repository
git clone https://github.com/nassau-candy/shipping-optimizer.git
cd shipping-optimizer

# 2. Create virtual environment
python -m venv .venv
source .venv/bin/activate  # Windows: .venv\Scripts\activate

# 3. Install dependencies
pip install -r requirements.txt

# 4. Configure environment
cp .env.example .env
# Edit .env with your credentials and data source URLs

# 5. Run the data ingestion pipeline
python -m src.data.ingest --mode full

# 6. Launch the API
uvicorn src.api.main:app --reload --port 8000

# 7. Launch the dashboard (separate terminal)
streamlit run src/dashboard/app.py
```

### Docker Compose (Recommended)

```bash
docker-compose up --build
# API:       http://localhost:8000
# Dashboard: http://localhost:8501
# Docs:      http://localhost:8000/docs
```

---

## Usage

### Run a Factory Reallocation

```python
from src.models.factory_allocator import FactoryAllocator
from src.data.schemas import AllocationInput

allocator = FactoryAllocator()

result = allocator.optimize(
    AllocationInput(
        planning_horizon_days=30,
        factories=["Hicksville", "Melville", "Hauppauge"],
        demand_forecast="forecasts/2025_Q3.parquet",
        cost_matrix="data/production_costs.csv",
    )
)

print(result.summary())
result.to_excel("outputs/allocation_recommendation.xlsx")
```

### Run a Shipping Optimization

```python
from src.models.route_optimizer import RouteOptimizer

optimizer = RouteOptimizer(solver="ortools")

routes = optimizer.solve(
    depot="Hicksville, NY",
    deliveries="data/open_orders.csv",
    vehicles="data/fleet.json",
    objective="minimize_cost",  # or "minimize_time" / "balanced"
)

routes.plot_map("outputs/optimized_routes.html")
routes.export("outputs/route_plan.xlsx")
```

### Scenario Comparison via API

```bash
curl -X POST http://localhost:8000/scenarios/compare \
  -H "Content-Type: application/json" \
  -d '{
    "baseline_id": "plan_2025_Q3",
    "scenarios": [
      {"name": "Fuel +20%", "fuel_cost_multiplier": 1.2},
      {"name": "Close Melville", "disabled_factories": ["Melville"]},
      {"name": "Demand +15%", "demand_multiplier": 1.15}
    ]
  }'
```

---

## Models & Algorithms

### Factory Allocation — Mixed Integer Linear Program (MILP)

**Objective:** Minimize total production + inbound freight cost

**Variables:**
- `x[f,s]` — binary: factory `f` produces SKU `s`
- `q[f,s]` — continuous: quantity of SKU `s` produced at factory `f`

**Key Constraints:**
- Total production ≥ demand forecast per SKU
- Factory capacity not exceeded (units, labor hours, line-hours)
- Minimum run quantity per SKU per changeover
- Equipment certification / allergen segregation rules

**Solver:** [PuLP](https://coin-or.github.io/pulp/) with CBC backend (HiGHS optional for large instances)

---

### Route Optimization — Vehicle Routing Problem (VRP)

**Variant:** Capacitated VRP with Time Windows (CVRPTW)

**Inputs:** delivery locations, demand volumes, time windows, vehicle capacity/cost

**Algorithm:** Google OR-Tools `routing` module with guided local search metaheuristic

**Extensions implemented:**
- Multi-depot support
- Split deliveries
- Carrier lane constraints (preferred carriers per geographic zone)

---

### Demand Forecasting

| Model | Use Case |
|---|---|
| Facebook Prophet | SKU-level weekly demand with seasonality |
| XGBoost | Promotions / event-driven demand spikes |
| Ensemble | Weighted blend for final recommendation |

---

## Data Pipeline

```
Source Systems
    │
    ▼
src/data/ingest.py          ← Connectors: REST/SFTP/JDBC
    │
    ▼
src/data/validate.py        ← Schema & quality checks (Great Expectations)
    │
    ▼
src/data/transform.py       ← Feature engineering, normalization
    │
    ▼
data/processed/             ← Parquet files, ready for modeling
```

**Key data inputs:**

| Dataset | Source | Refresh Cadence |
|---|---|---|
| Open Sales Orders | ERP (NetSuite) | Hourly |
| Factory Capacity | WMS | Daily |
| Carrier Rates | TMS / Rate APIs | Weekly |
| Fuel Index | EIA API | Weekly |
| Demand Forecast | Internal model | Weekly |
| Historical Shipments | TMS | Daily |

---

## API Reference

Full interactive docs available at `http://localhost:8000/docs` (Swagger UI).

| Endpoint | Method | Description |
|---|---|---|
| `/allocations/optimize` | POST | Run factory reallocation |
| `/allocations/{id}` | GET | Retrieve saved allocation |
| `/routes/optimize` | POST | Run shipping route optimization |
| `/routes/{id}/map` | GET | GeoJSON map of optimized routes |
| `/scenarios` | POST | Create a what-if scenario |
| `/scenarios/compare` | POST | Compare multiple scenarios |
| `/reports/allocation` | GET | Export allocation report (PDF/XLSX) |
| `/reports/routes` | GET | Export route report (PDF/XLSX) |
| `/health` | GET | Service health check |

---

## Dashboard

The Streamlit dashboard provides four views:

| Page | Contents |
|---|---|
| **Overview** | Cost KPIs, utilization gauges, alert center |
| **Factory Map** | Interactive map of facilities, current allocation, capacity heat map |
| **Route Planner** | Live route visualization, cost breakdown by lane |
| **Scenarios** | Side-by-side scenario comparison with sensitivity charts |

---

## Contributing

1. Fork the repo and create your feature branch: `git checkout -b feature/your-feature`
2. Follow the [coding standards](docs/CONTRIBUTING.md) — Black formatting, type hints required
3. Add tests for new model logic (`pytest tests/` must pass)
4. Open a pull request with a description of the change and its business impact

---

## Roadmap

- [x] MILP factory allocation model (v1)
- [x] OR-Tools VRP solver integration
- [x] FastAPI REST layer
- [x] Streamlit dashboard (MVP)
- [ ] Real-time ERP webhook integration
- [ ] Multi-objective Pareto frontier (cost vs. CO₂ vs. service level)
- [ ] ML-based carrier rate prediction
- [ ] Mobile-friendly dashboard
- [ ] Automated weekly S&OP report generation
- [ ] Kubernetes deployment manifests

---

## License

MIT License — see [LICENSE](LICENSE) for details.

---

*Built for Nassau Candy Distributor · Maintained by the Supply Chain Engineering team*


