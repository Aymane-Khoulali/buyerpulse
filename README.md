# BuyerPulse

**Customer Repurchase Prediction & Analytics**

BuyerPulse is an end-to-end machine learning project designed to predict whether an e-commerce customer will make another purchase within the next 60 days.

Developed as part of the 2026–2027 Machine Learning Integrated Project at Université Internationale de Rabat (UIR).

## Project Objectives

- Clean and validate historical e-commerce transactions.
- Transform transaction records into customer-level features.
- Construct leakage-safe, time-aware 60-day repurchase labels.
- Train and compare classification models.
- Evaluate and interpret predictions.
- Build an interactive customer analytics dashboard.

## Dataset

**UCI Online Retail II**

https://archive.ics.uci.edu/dataset/502/online+retail+ii

Contains over one million transaction-line records spanning December 2009 to December 2011.

Raw data files are not included in this repository.

## Planned Machine Learning Approach

**Task:** Binary classification

**Target:** Repeat purchase within 60 days

**Candidate models:**
- Logistic Regression
- K-Nearest Neighbors
- Decision Tree

**Features:**
- Recency, Frequency, Monetary (RFM)
- Purchasing behavior
- Historical transaction patterns

**Evaluation:** Chronological splitting, classification metrics, and leakage prevention.

Final modeling decisions will be determined through experimentation.

## Technology Stack

- Python 3.12
- Pandas and NumPy
- Scikit-learn
- Matplotlib and Seaborn
- Plotly and Streamlit
- Pytest
- Git and GitHub

## Getting Started

Clone the repository:

`git clone https://github.com/Aymane-Khoulali/buyerpulse.git`

`cd buyerpulse`

Create and activate a virtual environment:

`python3.12 -m venv .venv`

`source .venv/bin/activate`

Install dependencies:

`python -m pip install -r requirements.txt`

A full development environment snapshot is provided in `requirements-lock.txt`.

**Note:** The training pipeline and inference commands will be documented once implemented.

## Repository Structure

- `app/` — Interactive dashboard
- `configs/` — Project configuration
- `data/` — Raw and processed data (not tracked)
- `docs/` — Methodology and documentation
- `models/` — Saved trained models (not tracked)
- `notebooks/` — Exploration and experiments
- `reports/` — Figures and academic results
- `src/` — Machine learning pipeline
- `tests/` — Automated tests
- `main.py` — Planned training entry point
- `test.py` — Planned independent inference

## Project Status

**
