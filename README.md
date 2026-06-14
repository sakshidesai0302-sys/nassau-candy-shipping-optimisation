# nassau-candy-shipping-optimisation# Factory Reallocation & Shipping Optimization Recommendation System

### 📌 Client: Nassau Candy Distributor

A data-driven optimization and recommendation system designed to streamline supply chain logistics, optimize factory resource allocation, and minimize shipping overhead for Nassau Candy Distributor.

---

## 📖 Project Overview

This project provides an intelligent recommendation engine that analyzes distribution networks, manufacturing capacities, and shipping logistics. By leveraging advanced optimization techniques, the system helps Nassau Candy determine the most cost-effective strategies for shifting production loads and routing shipments to maximize efficiency and reduce operational costs.

## ❓ Problem Statement

Logistics and manufacturing environments face volatile shipping costs, varying factory capacities, and shifting regional demands. Nassau Candy requires a system that can:
* Dynamically reallocate factory production based on real-time constraints.
* Optimize shipping routes and carrier selection to minimize transit costs and times.
* Provide actionable, data-backed recommendations to supply chain managers.

## 🏗️ Solution Architecture

The system processes historical logistics and production data, runs mathematical optimization models, and outputs structured recommendations via a user-friendly interface.[ Input Data ] ──> [ Data Preprocessing ] ──> [ Optimization Engine ] ──> [ Recommendation Output ]

Factory Cap.      - Pandas / NumPy          - Linear Programming       - Dashboard / Reports

Shipping Rates                              - Heuristics               - API Payload[ Input Data ] ──> [ Data Preprocessing ] ──> [ Optimization Engine ] ──> [ Recommendation Output ]

---

## ✨ Key Features

* **Dynamic Factory Reallocation:** Balances manufacturing workloads across facilities based on capacity, labor costs, and regional demand.
* **Shipping Route Optimization:** Calculates the lowest-cost shipping paths and methods (LTL, FTL, Parcel) considering delivery deadlines.
* **Scenario Simulation ("What-If" Analysis):** Allows managers to simulate fuel price spikes, factory shutdowns, or demand surges.
* **Automated Recommendation Reports:** Generates clear, executive-ready summaries of recommended logistics shifts.

---

## 🛠️ Technologies & Libraries Used

* **Language:** Python 3.x
* **Data Manipulation:** `pandas`, `numpy`
* **Optimization Engine:** `scipy.optimize`, `PuLP`, or `Pyomo` *(Choose/edit as applicable)*
* **Visualization & UI:** `matplotlib`, `seaborn`, `streamlit` or `dash` *(Choose/edit as applicable)*
* **Data Storage:** `SQLite` / `PostgreSQL` or CSV files

---

## 🚀 Installation & Setup

### Prerequisites
Ensure you have Python 3.8+ installed on your system.

### 1. Clone the Repository
```bash
git clone [https://github.com/sakshidesai0302-sys/nassau-candy-shipping-optimisation.git](https://github.com/sakshidesai0302-sys/nassau-candy-shipping-optimisation.git)
cd nassau-candy-shipping-optimisation

# Windows
python -m venv venv
venv\Scripts\activate

# macOS/Linux
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
python main.py --config config.yamlInput Data FormatThe system expects data files to be placed in the data/input/ directory:factories.csv: Contains factory locations, capacities, and base production costs.shipping_rates.csv: Matrix of shipping costs between factories and distribution hubs.orders.csv: Current customer demands and destination zip codes.📊 Methodology & Optimization ModelThe system treats the supply chain network as a Capacitated Transportation Problem.Objective FunctionThe primary goal is to minimize total operational cost:$$\text{Minimize } Z = \sum_{i} \sum_{j} (C_{ij} \cdot X_{ij}) + \sum_{i} (P_{i} \cdot Y_{i})$$Where:$C_{ij}$ = Cost of shipping from factory $i$ to destination $j$$X_{ij}$ = Quantity shipped from factory $i$ to destination $j$$P_{i}$ = Production cost per unit at factory $i$$Y_{i}$ = Total units produced at factory $i$Constraints Managed:Supply Constraints: Total production cannot exceed a factory's maximum capacity.Demand Constraints: Total shipments to a hub must satisfy regional demand.Lead Time Constraints: Shipments must arrive within the designated delivery window.
