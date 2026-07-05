# EventGuard: Bengaluru Traffic Command Control & Resource Allocation System

EventGuard is an advanced predictive resource allocation system and dynamic digital twin designed to mitigate event-driven and unplanned traffic gridlocks in Bengaluru. The platform provides real-time predictive triage, resource optimization, and pathfinding by combining OpenStreetMap street network graphs, live incident logging pipelines, and empirical traffic priors from historical GPS probe data.

---
Project Scope & Contributions
This repository contains the standalone engineering architecture I built for this collaborative project. 

* **My Contributions:** Architected the full REST API backend (**Flask**), engineered the custom **Leaflet.js Map-HUD** interface from scratch, and integrated the resource-allocation engine using **PuLP** to solve multi-objective dispatch prioritization.
* **Team Scope:** The machine learning ensemble models and traffic route-optimization logic were developed by my teammate.

---

git branch -M main

## System Architecture & Key Components

1. **Live Incident Predictor (app.py, model.py)**:
   - Uses a soft-voting VotingClassifier ensemble of optimized LightGBM and CatBoost classifiers to predict whether an active incident will result in a High Impact (>120 mins) or Low Impact (<30 mins) clearance window.
   - Evaluates with an overall classification accuracy of 85.49% (Macro F1 of 0.8448) on the chronological split.
   - Features target-encoded junction_risk tracking.

2. **Resource Allocation Engine (recommendation_engine.py)**:
   - Formulates and solves a multi-objective fractional knapsack problem via PuLP linear programming to dynamically allocate scarce police and barricade supplies when concurrent traffic gridlocks exceed local station capacities.
   - Snaps coordinates dynamically to identify high-friction spatial zones using rolling haversine density counts.

3. **Route Optimizer & Digital Twin (digital_twin.py)**:
   - Merges OpenStreetMap city network graphs with a 4-month matrix of observed GPS velocity logs and probe density indicators.
   - Computes dynamic route cost updates under active incident shockwaves (using Dijkstra and A* pathfinding) to guide emergency dispatches to incident epicenters.

4. **Unified API Backend & Custom Map-HUD Dashboard (server.py, static/)**:
   - A modern decoupled architecture serving a high-fidelity geospatial web interface.
   - Built with Vanilla HTML/CSS/JS and Leaflet.js, incorporating clean typography, frosted glass overlays (glassmorphism), and dynamic route animation rendering.
   - Exposes clean REST API endpoints for metadata retrieval, dynamic pathfinding, queue optimization, and CSV batch processing.

---

## Exploratory Data Analysis (EDA) Insights

Here are the key structural findings extracted from the 4-month historical incident registry:

### 1. Hourly Operations Pulse & Peak Commuting Shockwaves
This dual-axis temporal trend visualizes how incident volume spikes concurrently with peak commute windows (8:00-11:00 AM and 5:00-8:00 PM), while showing the average clearance timeline (minutes) across the operational day.

![Hourly Operations Pulse](charts/chart_6_dual_axis_pulse.png)

### 2. Chronological Profile (Weekday x Hour Density Heatmap)
A detailed heatmap matrix mapping incident volume concentration across weekdays and hours. This highlights localized congestion bottlenecks and recurring patterns, such as severe weekend evening traffic.

![Chronological Heatmap Profile](charts/chart_7_temporal_heatmap.png)

### 3. Resource Pressure & Concurrency Matrix
This composite matrix scales ground-level concurrency demands across the 15 highest-burden police jurisdictions. It identifies localized resource saturation (such as in Whitefield) where incident concurrency exceeds baseline station supply.

![Resource Pressure Matrix](charts/chart_21_resource_pressure_matrix.png)

### 4. Priority x Event Cause Severity Matrix
Normalizing categorical priorities across specific event causes shows that "High Priority" classifications carry massive internal clearance variances. This justifies BTP's shift toward multi-objective predictive triage over simple priority labels.

![Priority by Cause Severity Heatmap](charts/chart_3_severity_heatmap.png)

---

## Installation & Usage

### Prerequisites
Ensure Python (3.9-3.12) is installed. Install required packages using pip:

```bash
pip install streamlit pandas numpy scikit-learn xgboost lightgbm catboost joblib osmnx networkx folium streamlit-folium pyproj pulp scipy matplotlib seaborn flask
```

### Running the Custom HTML/JS Dashboard (Recommended)
To run the high-fidelity Map-HUD control panel, start the Flask web application server:

```bash
python server.py
```

The application will initialize the Bengaluru network graph (taking ~10-15 seconds) and run on:
- Frontend: `http://localhost:5000`

The frontend features:
- **Triage Predictor**: Click on the map to snap coordinates, input incident parameters, predict clearance impact, and queue events.
- **Batch Optimizer**: Drag and drop incident CSV sequence logs to solve station capacity constraints.
- **Route Optimizer**: Drag origin/destination markers on the map and simulate dynamic pathfinding comparisons (Dijkstra vs A*) around simulated congestion decay zones.
- **EDA Insights**: View structural analytics charts and breakdown heatmaps.
- **Station Config**: Mutate police officer and barricade capacities dynamically in memory.

### Running the Streamlit Application
Alternatively, you can launch the classic Streamlit layout:

```bash
streamlit run app.py
```

The Streamlit dashboard will run on:
- URL: `http://localhost:8501`
