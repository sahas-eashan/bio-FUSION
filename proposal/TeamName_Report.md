# BioFusion Hackathon Report
**Team Name**: [Insert Team Name Here]
**Problem Title**: AI-Driven Medication Follow-Up Triage

## 1. Literature Review
Patient adherence and post-prescription monitoring are significant challenges in modern healthcare. Research by *Zullig et al. (2019)* highlights that 50% of chronic disease medications are not taken as prescribed. *Topaz et al. (2016)* demonstrated the utility of mining clinical notes, yet patient-reported outcomes (PROs) from online forums remain underutilized. Existing solutions often lack "closing the loop" (providing actionable advice). Our solution addresses this gap by combining predictive modeling with a rule-based decision agent.

## 2. Problem Identification
**Who is affected?**: Patents on long-term medication who experience side effects or lack of efficacy but hesitate to visit a doctor immediately.
**Importance**: Unaddressed medication issues lead to disease progression and adverse drug events (ADEs).
**Unmet Need**: An accessible, automated "first line of defense" that can interpret unstructured patient feedback and recommend urgency-appropriate actions.

## 3. Dataset Justification
We utilized the **UCI Drug Reviews (Druglib.com)** dataset.
- **Appropriateness**: It contains rich unstructured text (`benefitsReview`, `sideEffectsReview`) paired with structured outcomes (`rating`, `effectiveness`), making it ideal for training a hybrid NLP-tabular model.
- **Compliance**: Open-source (CC BY 4.0) and meets competition "Allowed Resources" criteria.

## 4. Methodology
### Data Preprocessing
- **Text Cleaning**: Lowercasing, removal of special characters.
- **Feature Engineering**: TF-IDF (1-2 ngrams, max 5000 features) combined with Sentiment Analysis (VADER) and metadata (Review Length).
- **Encoding**: Target variable `effectiveness` mapped to 3 classes (Ineffective, Moderate, Effective).

### Model Architecture
- **Classifier**: LightGBM (Gradient Boosting Decision Trees).
- **Training**: Stratified 80/20 Split. Trained with `multi_logloss` objective for 1000 rounds with early stopping.

### Agentic Workflow (Uniqueness)
We implemented a 5-step Agentic Framework:
1.  **Integrity Check**: Flags inconsistencies (e.g., High Rating vs. "Ineffective" label).
2.  **Retrieval**: Fetches similar historical cases using Cosine Similarity.
3.  **Prediction**: Generates class probabilities.
4.  **Verification**: Checks confidence thresholds.
5.  **Decision**: Outputs a final recommendation (e.g., "Consult Doctor").

## 5. Pretrained Model Usage
- **Approach**: Random Initialization (Training from Scratch).
- **Rationale**: Given the tabular nature of the metadata and the specific vocabulary of drug reviews, a lightweight GBDT model with TF-IDF often outperforms heavy BERT models for simple classification tasks on small datasets (<5k samples), providing better interpretability and speed.

## 6. Results
*(See Notebook for generated charts)*
- **Accuracy**: ~70-75% (Baseline)
- **Primary Metric**: Maco-F1 Score.
- **Error Analysis**: The model struggles most with "Mixed" effectiveness, which is subjective. The Integrity Agent mitigates this by flagging uncertain cases.

## 7. Real-world Application
- **Scenario**: Integrated into a Patient Portal app. A patient logs a diary entry about their medication.
- **Workflow**: The Agent analyzes the entry. If "Ineffective" is predicted with high confidence, the app prompts: *"It seems this medication isn't working as expected. Would you like to schedule a follow-up?"*
- **Risks**: False negatives (missing severe side effects). Addressed by a "Red Flag" keyword list in the Integrity module.

## 8. Future Improvements
- **Model**: Fine-tune a ClinicalBERT model for better context understanding.
- **Data**: Incorporate time-series info (multiple reviews from same patient).
- **Deployment**: Deploy as a simple Streamlit web app.
