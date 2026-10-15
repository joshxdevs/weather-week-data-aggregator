# Weather Week Data Aggregator

[![Python](https://img.shields.io/badge/Python-3.10%2B-blue.svg?logo=python&logoColor=white)](https://www.python.org/)
[![API](https://img.shields.io/badge/API-Open--Meteo-brightgreen.svg?logo=cloud&logoColor=white)](https://open-meteo.com/)
[![Pandas](https://img.shields.io/badge/Library-Pandas-150458.svg?logo=pandas&logoColor=white)](https://pandas.pydata.org/)
[![Matplotlib](https://img.shields.io/badge/Visualization-Matplotlib-11557c.svg)](https://matplotlib.org/)

A lightweight Python data pipeline that aggregates the past 7 days of daily maximum and minimum temperature records for Bangalore (or any target location) using the [Open-Meteo API](https://open-meteo.com/). The script automatically cleans and transforms the data with **Pandas**, visualizes temperature trends with **Matplotlib**, and saves the results to both an image chart and a CSV dataset.

---

## Features

- **Zero API Key Required:** Connects directly to the Open-Meteo public weather forecast and historical API without sign-ups or credentials.
- **Dynamic Date Window:** Dynamically computes a rolling 7-day date window relative to execution time (`today - 7 days`).
- **Data Transformation:** Converts nested JSON metrics into a structured [Pandas](https://pandas.pydata.org/) DataFrame with formatted datetime timestamps.
- **Visual Analytics:** Generates a dual-line plot (`weather_chart.png`) contrasting maximum and minimum temperatures across the week.
- **Automated Data Persistence:** Ensures output directories exist and writes tabular records to `data/bangalore_weather.csv`.

---

## System Sequence Diagram

The following sequence diagram illustrates the complete end-to-end flow of data retrieval, processing, plotting, and file export:

```mermaid
sequenceDiagram
    autonumber
    actor User as User / Developer
    participant Script as weather-request-api.py
    participant API as Open-Meteo API
    participant Engine as Pandas & Matplotlib
    participant Storage as Local File System

    User->>Script: Run script (python weather-request-api.py)
    activate Script

    Script->>Script: Calculate date range (today - 7 days to today)
    Script->>API: HTTP GET /v1/forecast<br/>(lat=12.97, lon=77.59, start_date, end_date)
    activate API
    API-->>Script: 200 OK (JSON with daily temperature arrays)
    deactivate API

    Script->>Engine: Construct DataFrame (date, max_temp, min_temp)
    activate Engine
    Engine-->>Script: Formatted DataFrame
    deactivate Engine

    Script->>User: Print DataFrame preview to stdout

    Script->>Engine: Render Dual-Line Plot (Max Temp vs Min Temp)
    activate Engine
    Engine->>Storage: Save plot to weather_chart.png
    Engine->>User: Display GUI window (plt.show)
    deactivate Engine

    Script->>Storage: Check/Create data/ directory
    Script->>Storage: Export DataFrame to data/bangalore_weather.csv
    Storage-->>Script: File write confirmed

    Script->>User: Print completion confirmation
    deactivate Script
```

---

## Repository Structure

```text
weather-week-data-aggregator/
│
├── data/
│   └── bangalore_weather.csv    # Aggregated 7-day historical weather CSV
│
├── weather-request-api.py       # Core execution script (API fetch, plot, export)
├── weather_chart.png            # Generated temperature trend graph
├── requirements.txt             # Python dependencies
├── .gitignore                   # Ignored files (virtualenv, caches, logs)
└── README.md                    # Project documentation
```

---

## Getting Started

### Prerequisites

Ensure you have **Python 3.10+** installed on your system.

### 1. Clone the Repository

```bash
git clone https://github.com/joshxdevs/weather-week-data-aggregator.git
cd weather-week-data-aggregator
```

### 2. Create and Activate a Virtual Environment

- **macOS / Linux:**
  ```bash
  python3 -m venv .venv
  source .venv/bin/activate
  ```
- **Windows:**
  ```powershell
  python -m venv .venv
  .venv\Scripts\activate
  ```

### 3. Install Dependencies

```bash
pip install -r requirements.txt
```

---

## Usage
Run the aggregation script:
```bash
python weather-request-api.py
```
### Script Execution Flow:
1. Queries the Open-Meteo API for Bangalore (`lat: 12.97`, `lon: 77.59`).
2. Prints the structured table directly in your terminal:
   ```text
            date  max_temp  min_temp
   0  2026-09-28      28.6      19.8
   1  2026-09-29      28.1      19.5
   ...
   ```
3. Generates and displays `weather_chart.png` depicting maximum and minimum temperature trends.
4. Exports the table to `data/bangalore_weather.csv`.
---
## Sample Output
### CSV Record Format (`data/bangalore_weather.csv`)
| date | max_temp (°C) | min_temp (°C) |
| :--- | :--- | :--- |
| `2026-09-28` | 28.6 | 19.8 |
| `2026-09-29` | 28.1 | 19.5 |
| `2026-09-30` | 28.2 | 20.3 |
| `2026-10-01` | 28.4 | 20.5 |
| `2026-10-02` | 28.8 | 20.1 |
| `2026-10-03` | 29.3 | 20.2 |
| `2026-10-04` | 30.1 | 20.9 |
| `2026-10-05` | 29.4 | 20.3 |
---
## Customization
To target a different city or change metrics, open [`weather-request-api.py`](weather-request-api.py) and modify:
- **Coordinates:**
  ```python
  # Example: New York City
  latitude = 40.7128
  longitude = -74.0060
  ```
- **Date Duration:** Change `timedelta(days=7)` to any desired range (e.g., `14` or `30`).
- **Additional Metrics:** Add parameters to the query URL such as `precipitation_sum` or `windspeed_10m_max`.
---

