# IPL Prediction System — Learning Requirements & Architecture

## 1. Vision & Learning Goals
- Use IPL data as a sandbox to practice end-to-end ML workflows: ingestion, cleaning, feature engineering, modelling, evaluation, and storytelling.
- Build lightweight tools that run comfortably on a local machine (not production infrastructure).
- Document experiments and insights so future iterations (or other learners) can reproduce the journey.

### Success Markers (Learning-Oriented)
- Produce at least one working model for each prediction objective and record its evaluation metrics, even if accuracy is modest.
- Explain why a model behaves the way it does using plots, tables, or narrative summaries.
- Automate the most repetitive steps (e.g., data preparation) with scripts or notebooks.
- Maintain a changelog of experiments, wins, and lessons learned.

## 2. Primary Personas
| Persona | Goals | Tools |
| --- | --- | --- |
| ML Learner (you) | Practice DS/ML pipeline skills, explore ideas safely | Jupyter/VS Code notebooks, Python scripts |
| Curious Peer | Review findings, rerun demos | README, notebooks, lightweight dashboard |
| Future Self | Revisit project later, extend to new techniques | Structured repo, documented decisions |

## 3. Scope Definition
### Must-Haves
- Collect and clean historical IPL ball-by-ball data (Cricsheet JSON) into analysable tables.
- Perform exploratory analysis and visualize key trends.
- Train baseline models for:
  - Match winner prediction
  - Season outcome (e.g., top-4 standings)
  - Player award proxy (Orange/Purple Cap)
- Evaluate models with transparent metrics and error analysis.
- Share results via notebooks and/or a simple interactive demo (Streamlit/FastAPI + minimal UI).

### Nice-to-Have Experiments
- Generative AI assistant for Q&A using project artefacts.
- “What-if” scenario simulator (swap players, adjust toss outcome).
- Additional data enrichments (weather, venues, auction values).
- Re-usable feature repository or experiment tracker (e.g., MLflow).

### Out of Scope (for now)
- Production-grade deployment, autoscaling, or enterprise monitoring.
- Complex CI/CD pipelines.
- Live, real-time predictions during matches.

## 4. Key Use Cases
1. Explore dataset to surface insights (top scorers, venue advantages).
2. Run a notebook that trains a match winner model and visualizes performance.
3. Trigger a script/notebook that simulates a season and compares predictions to actual standings.
4. Use a simple UI or CLI prompt to ask, “Who wins MI vs CSK at Wankhede?” and view model rationale.

## 5. Data Plan
### Source & Storage
- Download Cricsheet IPL JSON and keep in `data/raw/`.
- Transform into tidy tables/parquet stored in `data/processed/` (consider DuckDB for quick queries).
- Maintain a short data dictionary describing columns and assumptions.

### Quality & Versioning
- Write validation helpers (e.g., `pydantic`/assert statements) to catch obvious anomalies.
- Record data preparation steps in notebooks/scripts so they can be replayed.
- Version large processed artefacts with Git LFS or keep regeneration scripts handy.

### Candidate Features
- Team form: rolling win %, net run rate, toss bias by venue.
- Player form: recent runs/wickets, strike rates, economy.
- Match context: venue type, day/night flag, toss decision, head-to-head record.
- Season context: points table after each round.

## 6. Functional Requirements
- Reusable loader that parses raw JSON into pandas/Polars dataframes.
- Feature engineering modules/notebooks that create training datasets per task.
- Training pipelines (notebook or script) for classification (match/award) and simulation for season outcomes.
- Evaluation artefacts: confusion matrices, calibration plots, leaderboards.
- Lightweight app layer:
  - Option A: Streamlit dashboard with inputs for teams/venue and output predictions.
  - Option B: FastAPI endpoint returned in notebook/CLI for quick demos.
- Optional generative notebook that summarises results using an open-source model (offline compatible).

## 7. Non-Functional (Learning Context)
- **Performance:** Keep run times manageable (<5 min) on a laptop by sampling or caching intermediate datasets.
- **Reproducibility:** Use `pyproject.toml` / `requirements.txt` and deterministic seeds; document environment setup.
- **Clarity:** Favor readable code, comments explaining tricky transformations, and markdown narratives.
- **Flexibility:** Structure repo so new experiments can be added without refactoring everything (`notebooks/`, `src/`, `data/`).
- **Safety:** Since this is offline learning, secure secrets by avoiding hard-coding API keys; mock where necessary.

## 8. Assumptions & Constraints
- Python 3.10+ local setup; dependencies managed via `uv` or `pip`.
- No external network during restricted sessions, so cache downloads ahead of time.
- Dataset fits within local storage/memory; use sampling if needed.
- MIT license compliance remains.

## 9. Proposed Architecture (Learning-Friendly)
```
Raw Data (Cricsheet JSON)
    ↓
Ingestion Notebook / Script (parse, validate)
    ↓
Processed Tables (Parquet/DuckDB) + Data Dictionary
    ↓
Feature Engineering Modules (Python scripts)
    ↓
Experiment Notebooks (training, evaluation, visualization)
    ↓                ↘
Saved Models / Metrics          Lightweight App (Streamlit or FastAPI)
    ↓
Documentation (reports, readme updates, changelog)
```

### Components
- **Data Ingestion:** Python script `src/data/ingest.py` or notebook `notebooks/01_ingest.ipynb`.
- **Feature Engineering:** Module `src/features/` with helper functions for rolling stats.
- **Modelling:** `notebooks/2x_*` for specific tasks, optionally mirrored in scripts for repeatability.
- **Serving Demo:** `streamlit_app.py` or `src/app/api.py` to showcase predictions.
- **Experiment Tracking (optional):** Simple CSV/JSON log or MLflow if desired.

## 10. Roadmap
1. **Setup & Data Familiarisation**
   - Organize repo folders (`data/`, `notebooks/`, `src/`, `reports/`).
   - Download Cricsheet data; inspect JSON structure.
2. **Data Cleaning & EDA**
   - Build ingestion + cleaning pipeline.
   - Produce initial EDA notebook with visual insights.
3. **Baseline Models**
   - Construct match winner dataset → train simple classifiers (logistic regression, tree-based).
   - Extend to season simulations and award predictors.
4. **Evaluation & Explainability**
   - Record metrics, plot feature importances, analyse misclassifications.
5. **Demo Application**
   - Build Streamlit/FastAPI demo using saved models.
6. **Stretch Experiments**
   - Add generative summaries, scenario simulations, or advanced models (XGBoost, LSTM).
7. **Wrap-Up**
   - Summarize learnings in README, note future ideas.

## 11. Testing & Validation
- Unit tests for data loaders/feature functions (`pytest`).
- Notebook assertions verifying dataset shapes and key invariants.
- Backtesting seasons (train on 2008–2017, test 2018, etc.).
- Manual smoke tests for Streamlit/API demo.

## 12. Risks & Mitigations
- **Data Volume Overload:** Use season subsets or DuckDB to keep memory under control.
- **Feature Leakage:** Carefully align features with prediction timeline; document assumptions.
- **Model Overfitting:** Reserve holdout seasons, perform cross-validation.
- **Motivation Drift:** Maintain a TODO backlog and celebrate milestones to stay engaged.
- **Tooling Fatigue:** Start simple (pandas, sklearn); only add new tools when the need is clear.

## 13. Open Questions
- Do you prefer Streamlit or FastAPI for the first interactive demo?
- Which experiment tracker (if any) feels worth the setup effort?
- How deep should generative AI experiments go given offline constraints?
- Would you like to compare models season-by-season or focus on the most recent seasons first?

This document will evolve as you progress; treat it as a living guide for your learning journey.
