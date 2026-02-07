# Grid Monte Carlo Simulation

Monte Carlo simulation framework for evaluating bulk power system resource adequacy under evolving energy landscapes.

## Overview

This project models the electric grid's capacity to meet demand using Monte Carlo methods to evaluate **Loss of Load Probability (LOLP)** and **unserved energy** under realistic uncertainty scenarios. The framework integrates:

- **EIA-860 generator data** — operational capacity by technology and location
- **Weather-driven demand modeling** — temperature-based load estimation using HDD/CDD
- **Planned data center load** — anticipated demand from new computational infrastructure
- **Generator forced outages** — probabilistic outage modeling by technology type

The goal is to assess grid reliability under different futures: high renewable penetration, extreme weather cycles, and data center expansion.

## Methodology

### Resource Adequacy Model
This is a **capacity sufficiency** model, not a transmission/OPF model. The system is treated as a "copper plate" (no transmission constraints). Each Monte Carlo iteration:

1. **Samples generator availability** — Bernoulli draws based on forced outage rates by technology
2. **Calculates available capacity** — sum of operational generators
3. **Estimates demand** — weather-indexed baseline + data center load
4. **Records shortfall** — if demand exceeds supply

After N iterations (10k–1M+), we compute:
- **LOLP** — probability of insufficient capacity
- **Unserved energy distribution** — magnitude and frequency of shortfalls
- **Sensitivity by season, technology mix, and weather extremes**

### Data Sources

| Source | Description | Status |
|--------|-------------|--------|
| **EIA-860** | Operating generator capacity (4M records, 2011–2025) | ✅ Ingested |
| **NOAA Weather API** | Historical temperature data for demand modeling | 🔄 Planned |
| **Data Center Sites** | Planned DC locations and expected load (interconnection queues, DOE filings) | 🔄 Researching |
| **Outage Rates** | Technology-specific forced outage probabilities (literature review) | 🔄 Researching |

## Project Structure

```
grid-monte-carlo/
├── data/
│   ├── raw/              # EIA API pulls, weather data, DC coordinates
│   ├── processed/        # Cleaned parquet files
│   └── scenarios/        # Constraint configs (renewable %, DC demand, weather)
├── notebooks/
│   ├── 01_eia_data_ingestion_spark.ipynb    # ✅ Complete
│   ├── 02_data_exploration.ipynb            # 🔄 Next
│   ├── 03_outage_model.ipynb                # 🔄 Planned
│   ├── 04_demand_model.ipynb                # 🔄 Planned
│   └── 05_monte_carlo_simulation.ipynb      # 🔄 Planned
├── src/
│   ├── data_loaders/     # Weather API, data center scrapers
│   ├── models/           # Outage rates, demand estimation
│   └── simulations/      # Monte Carlo engine
├── outputs/
│   └── results/          # LOLP curves, distributions, summary stats
└── README.md
```

## Technology Stack

- **PySpark** — distributed data processing (local mode → cluster later)
- **Pandas** — data ingestion and lightweight analysis
- **Parquet** — columnar storage for efficient I/O
- **Python** — simulation logic, statistical modeling

**Why PySpark for local development?**  
Write once, scale later. The code runs locally now but is ready for distributed compute (Databricks, EMR, etc.) when simulation scale increases (100k+ iterations, multi-region models).

## Current Progress

- ✅ EIA-860 data ingestion pipeline (4M generator records)
- ✅ Checkpoint/resume capability for API resilience
- ✅ Parquet-based data storage
- 🔄 Data exploration and technology classification
- 🔄 Outage probability model
- 🔄 Weather API integration
- 🔄 Data center demand integration
- 🔄 Monte Carlo simulation engine

## Planned Scenarios

| Scenario | Description | Constraints |
|----------|-------------|-------------|
| **Baseline 2025** | Current generator mix | Existing capacity, historical weather |
| **High Renewable 2030** | 50% solar + wind penetration | Increased intermittency, storage modeling |
| **Data Center Boom 2028** | Planned DC load spikes | Concentrated demand, site-specific capacity |
| **Extreme Weather 2035** | Climate-driven load variability | Heat waves, cold snaps, HDD/CDD extremes |

## Setup

```bash
# Clone the repository
git clone https://github.com/ranuyay/grid-monte-carlo.git
cd grid-monte-carlo

# Create virtual environment
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt

# Configure environment variables
# Create .env file with:
# EIA_API_KEY=your_key_here

# Run notebooks
jupyter notebook
```

## Next Steps

1. **Data exploration** — summarize capacity by technology, region, vintage
2. **Technology classification** — map EIA fuel codes to renewable/fossil/nuclear/storage
3. **Outage model** — assign forced outage rates by technology from literature
4. **Weather integration** — pull historical temperature data, build HDD/CDD demand index
5. **Data center sourcing** — scrape utility interconnection queues, aggregate planned load
6. **Monte Carlo engine** — parallelize simulations across scenarios

## Learning Goals

This project is a learning exercise in:
- **Large-scale data engineering** — handling millions of records with PySpark
- **Probabilistic modeling** — Monte Carlo methods for uncertainty quantification
- **Energy systems** — resource adequacy, grid reliability metrics
- **Distributed computing** — local → cluster workflow design

## References

- [EIA-860 Data Documentation](https://www.eia.gov/electricity/data/eia860/)
- [NERC Resource Adequacy Methods](https://www.nerc.com/)
- [Loss of Load Probability (LOLP) Overview](https://en.wikipedia.org/wiki/Loss_of_load_probability)

## License

MIT

## Contact

Questions or suggestions? Open an issue or reach out via [LinkedIn](https://www.linkedin.com/in/ranhamid).