# MedFollow — Agentic Decision-Support for Medication Follow-Up

**Medical disclaimer:** Prototype clinical decision-support tool. No diagnoses, prescriptions, or medical advice. When confidence is low, the system defaults to conservative recommendations and defers to clinicians.

## BioFusion Hackathon goals
- Build a transparent ML pipeline that predicts medication effectiveness from patient-reported reviews and symptom changes.
- Showcase an explainable, rule-guided agent stack (integrity → retrieval → prediction → verification → decision).
- Deliver a runnable notebook and a concise report that satisfy BioFusion submission rules (open data, clear preprocessing, explicit training steps, seeds set, reproducibility).

## Dataset
- Source: **Drug Reviews (Druglib.com)** from UCI ML Repo (ID 461), CC BY 4.0, research use only, do not redistribute raw data beyond this project.
- Local copy: `data/drug_reviews/data.csv` (4,143 rows; fields: `reviewID`, `urlDrugName`, `rating`, `effectiveness`, `sideEffects`, `condition`, `benefitsReview`, `sideEffectsReview`, `commentsReview`).
- Re-download (optional): `curl -L "https://archive.ics.uci.edu/static/public/461/data.csv" -o data/drug_reviews/data.csv` or `from ucimlrepo import fetch_ucirepo`.
- Note: Not time-series; supports NLP + tabular features.

## Tasks & modeling plan
- Primary: Medication effectiveness classification (`effectiveness`) — start with binary (Effective vs Not) and optionally 3-class (Effective / Mixed / Not).
- Secondary: Side-effect risk (`sideEffects`) and overall satisfaction (`rating`) regression or ordinal classification.
- Baselines: TF-IDF + Logistic Regression; LightGBM on text + categorical encodings; optional DistilBERT text encoder (pretrained usage must be disclosed).
- Data split: train/validation/test or k-fold; stratify by `effectiveness`; set seeds for reproducibility.

## Agentic workflow (core contribution)
1) **Integrity checks:** consistency prompts, plausibility/range screens, OOD distance, perturbation stability flags; low integrity → confidence down-weighted.  
2) **Symptom change:** extract day-0 vs current symptoms; tag improved/persistent/worsened.  
3) **Retrieval:** TF-IDF cosine similarity to surface top-k similar drug/condition cases for context.  
4) **Prediction:** ML model outputs effectiveness probability + confidence.  
5) **Verification:** ensemble sanity checks + stability scores; abstain when unstable.  
6) **Decision:** combine prediction + integrity flags → {`continue`, `schedule follow-up`, `seek urgent advice`} with rationale and disclaimer.

## Notebook requirements (aligns with BioFusion rubric)
- Problem statement and clinical relevance.
- Dataset documentation: citation, schema, distribution, preprocessing steps.
- Pretrained disclosure: model name, source, task, layers frozen/trainable, weights used vs random init.
- Training loop shown explicitly (forward, loss, backprop, optimizer updates); justify hyperparameters.
- Validation strategy (train/val/test or k-fold) and leakage prevention.
- Metrics: accuracy/F1/ROC-AUC, confusion matrix, calibration plot; robustness/perturbation + OOD checks; error analysis.
- Reproducibility: seeds set, paths intact, environment deps listed; logs/curves visible.
- Outputs: metrics, integrity flags, similar-case snippets, final recommendation with disclaimer.

## Report outline (≤5 pages, TNR 12, 1.15)
- Literature review (≥3 papers on patient-reported outcomes/adherence); problem importance.
- Dataset justification and limitations (non–time-series, self-reported biases).
- Methodology with workflow diagram description; pretrained rationale/risks (domain mismatch, bias).
- Results: metrics tables, calibration, error analysis; practical limitations.
- Real-world application: telehealth/portal follow-up, human-in-the-loop; future improvements.

## Repo layout
- `data/drug_reviews/data.csv` — UCI Druglib reviews (local copy).
- `README.md` — this document.
- (Add) `notebooks/medfollow.ipynb` — modeling + agent workflow (to be created).

## Next steps
- Build `notebooks/medfollow.ipynb` implementing preprocessing, splits, baselines, retrieval, robustness/OOD checks, and the decision agent.
- Draft the 5-page report following the outline above and include dataset/license citation (CC BY 4.0, UCI Drug Reviews — Druglib.com).
