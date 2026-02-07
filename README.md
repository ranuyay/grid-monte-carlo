# Grid Monte Carlo Simulation

Monte Carlo simulation framework for bulk power system resource adequacy analysis.

## Features
- EIA-860 generator data ingestion (PySpark)
- Weather-driven demand modeling
- Planned data center load integration
- Generator forced outage simulation
- LOLP and unserved energy metrics

## Setup
```bash
# Create virtual environment
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt
```

## Structure
- `data/` - Raw and processed datasets
- `notebooks/` - Jupyter notebooks for analysis
- `src/` - Core simulation code
- `outputs/` - Results and visualizations
