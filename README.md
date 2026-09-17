# Seismic-Hazard-Triage-Explosion-Discrimination-Tsunami-Risk-Prediction
This repository holds the analysis pipelines used to produce every figure, table, and reported number in the paper, so that results can be reproduced end to end from the raw data.
What's here

Two parallel pipelines, built with an identical structure (data loading → EDA → feature engineering → train/test split & scaling → model zoo → 10-fold CV benchmark → statistical significance testing → hold-out evaluation → explainability), applied to two tasks with very different class balance:

	Explosion discrimination	Tsunami risk
Notebook	notebooks/explosion_discrimination_pipeline.ipynb	notebooks/tsunami_risk_pipeline.ipynb (add here — see note below)
Source	USGS real-time earthquake feed, mirrored on Kaggle	Global Earthquake-Tsunami Risk Assessment Dataset, Kaggle
Rows	~10,505	782
Positive class	1.85% (explosive/blast)	38.7% (tsunami)
Best model	XGBoost	GradientBoosting

Note: only the explosion-discrimination notebook has been added to this repo so far. Add the tsunami-risk notebook (referenced throughout the paper's Methods and Results as the parallel pipeline) to notebooks/ under the same naming convention, and update the table above and the dataset link in data/README.md once it's in.

Repository structure
.
├── README.md                  ← you are here
├── requirements.txt           ← pinned-enough pip install list
├── environment.yml            ← optional conda equivalent
├── LICENSE
├── CITATION.cff                ← machine-readable citation metadata
├── data/
│   └── README.md              ← where the raw data comes from, how to fetch it
├── notebooks/
│   └── explosion_discrimination_pipeline.ipynb
└── outputs/
    └── .gitkeep                ← generated figures/tables land here (gitignored)
Setup
bash
git clone https://github.com/<your-username>/<repo-name>.git
cd <repo-name>
python -m venv .venv && source .venv/bin/activate   # or: conda env create -f environment.yml
pip install -r requirements.txt

Each notebook installs its own extra dependencies (%pip install ...) in its first cell as well, so a plain Jupyter/Colab launch also works without manually running requirements.txt first.

Running the pipeline
Open notebooks/explosion_discrimination_pipeline.ipynb in Jupyter, JupyterLab, or Google Colab.
Run all cells top to bottom. The first code cell installs dependencies; the second downloads the dataset via kagglehub (requires a free Kaggle account and API token — see data/README.md).
All figures, tables, and CSVs described in the notebook's final "Summary" cell are written to OUTDIR (default: a local outputs/ folder, or /content/pipeline_outputs if run in Colab, zipped and downloaded at the end of the notebook).

Expect the full run (including the optional TabPFN and multi-snapshot replication sections) to take a few minutes on a standard Colab CPU runtime; skip the RUN_LLM_NARRATIVE cell (off by default) unless you want to download a multi-gigabyte language model just for the optional narrated SHAP explanation.

Reproducibility notes
Random seed. Every stochastic step (train/test split, cross-validation folds, SMOTE resampling, model initialisation) uses the same RANDOM_STATE defined near the top of the notebook, so results should match the paper's reported numbers up to floating-point and library-version differences.
Data snapshot dependence. The explosion-discrimination dataset is a rolling 30-day feed, not a fixed historical catalogue — re-running the notebook on a different day will pull a different snapshot, with a different class balance and sample size. This is called out explicitly in the paper's Limitations section. The notebook's Section 14 (Multi-Snapshot Temporal Replication) exists specifically to let you check whether results hold up on a different window.
Known notebook issue. Cell under section 15 (TabPFN baseline) has a stray import OS (should be lowercase os) — harmless if that cell isn't re-run in isolation, but worth fixing before this goes fully public.
Outputs

Running the explosion-discrimination notebook end to end reproduces, among other things:

model_benchmark_results.csv, pairwise_significance_results.csv — the 10-fold CV leaderboard and Holm-Bonferroni-corrected pairwise comparisons (paper Section 4.2–4.3, Tables 2, 4)
Hold-out classification report, bootstrap confidence intervals, and calibration curve (Section 4.4, Tables 5–6)
SHAP, LIME, permutation importance, and PDP/ICE explainability outputs (Section 4.5)
The reporting-network leakage check and depth-blind robustness check (Sections 4.6–4.7, Tables 7–8)
Compute-cost benchmark (Section 4.8, Table 9)
Citation

See CITATION.cff, or cite directly:

[Eram Mahamud]. "Machine Learning for Seismic Hazard Triage: Comparing Explosion Discrimination and Tsunami Risk Prediction Under Contrasting Class-Imbalance Regimes."

License

Code is released under the MIT License (see LICENSE). This does not cover the third-party datasets referenced in data/README.md, which retain their own original licenses.
