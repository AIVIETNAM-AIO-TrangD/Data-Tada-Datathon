Predicting Production Cessation in Heavy Oil Wells

DSMLC F26 Datathon (Chirality Research & Canary Labs) | Theme: Optimization

One-sentence summary: we forecast equipment failures and production downtime in heavy oil wells using an NLinear model, and present the results in an interactive dashboard.

Research Question

Replace with your final question, e.g. "Can daily operating patterns identify PCP wells at risk of production cessation within the next 14 days, and how much lost oil can early intervention avoid?"

Why it matters: (2-3 sentences on business value: reduced downtime, lower workover cost, better maintenance planning.)

Dataset

Daily operating records for 2,905 heavy oil wells in Western Canada (Jan 2021 - Oct 2026), plus job and failure logs.

File	Rows	Description
daily_production.parquet	5,075,590	Daily time series, one row per well per day
well_jobs.parquet	15,610	One row per service job
production_failures.parquet	2,873	One row per equipment failure

The data is not included in this repo. See data/README.md.

Data Cleaning

Key issues found and how they were handled:

Issue	Handling
29 duplicate well-day rows	Deduplicated on uwi + production_date
Placeholder values (999999, 999)	Replaced with NaN
Negative volumes and pressures	Replaced with NaN
Implausible RPM, amps, torque	Capped using domain limits / quantiles
Mixed scale in pump_efficiency_pct	Values > 1 divided by 100
Inconsistent labels (Oil / Heavy Oil)	Standardised
Missing days in 168 wells	Left as gaps (dashboard); filtered out of windows (model)
Volumes blank when shut in	Filled with 0 only for Suspended/Abandoned wells

Data quality

Method
Target: (e.g. failure or shut-in within the next H days)
Model: NLinear (lookback window = L days, horizon = H days)
Features: (water cut, uptime %, torque per RPM, rolling statistics, days since last job, ...)
Split: time-based hold-out (last N months) plus well-based hold-out
Metrics: PR-AUC, recall at fixed precision, lead time
Results
Model	PR-AUC	Precision	Recall
Baseline (last value)			
NLinear			

Results

Key findings: 1. 2. 3.

Dashboard

Dashboard

Link or run instructions: streamlit run dashboard/app.py

Repository Structure
data/         Data notes (raw and processed data are gitignored)
notebooks/    EDA, cleaning, modeling
src/          clean.py, features.py, train.py, utils.py
dashboard/    Dashboard app
reports/      Figures and final report
docs/         Data dictionary
How to Run
bash
# 1. Set up environment
python -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\activate
pip install -r requirements.txt

# 2. Place the three .parquet files in data/raw/

# 3. Clean the data
python src/clean.py --input data/raw --output data/processed

# 4. Build features and train
python src/features.py
python src/train.py

# 5. Launch dashboard
streamlit run dashboard/app.py
Limitations and Next Steps
Heavy class imbalance (about 2,900 failures across about 5M well-days)
Sensor columns are 50-95% missing, so models rely on a subset of wells
Add your own
Team
Name	Role
	
Acknowledgements

Dataset provided by Chirality Research & Canary Labs for the DSMLC F26 Datathon.

License

MIT (see LICENSE)
