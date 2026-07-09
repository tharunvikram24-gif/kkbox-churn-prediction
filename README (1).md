# KKBox Churn Prediction — Subscription Renewal Analysis

## Business Problem
KKBox, Asia's leading music streaming platform (Taiwan, Hong Kong, Japan, Singapore, Malaysia),
faces a critical retention challenge: identifying which subscribers will not renew their
membership before it's too late to intervene.

**Churn definition:** A user churns if they do not renew within 30 days of membership expiry.

**Subscription pricing (2017 era):** ~$7.90 USD/month  
*(Source: Singtel/KKBox carrier agreement pricing)*

With 970,960 subscribers and a 9% churn rate, ~87,330 users churn per prediction period,
representing ~$690,000 in monthly recurring revenue at risk.

## The One-Sentence Business Case
Our model identifies 96.5% of churners by targeting just 20% of users —
saving **$310,706 in intervention budget** and generating **$241,703 in net revenue**
per cohort vs. contacting all users indiscriminately.

## Dataset
Real data from the [KKBox Churn Prediction Challenge](https://www.kaggle.com/competitions/kkbox-churn-prediction-challenge):
- `train_v2.csv` — 970,960 users with real churn labels
- `members_v3.csv` — 6.7M user demographic records
- `transactions_v2.csv` — 1.4M subscription transaction records
- `user_logs_v2.csv` — NOT used (3GB daily listening logs — documented limitation)

## Key Findings

### Finding 1: No-Transaction Users (Pre-Model — No ML Needed)
Users with zero transaction history churn at **78.7%** vs **6.2%** for active users.
**Business action:** Getting new users to complete their first transaction reduces
churn risk by 12.7x — more impactful than any retention campaign.

### Finding 2: Two Dominant Behavioral Signals
- Auto-renew OFF → **36.1% churn** vs Auto-renew ON → **7.65% churn** (4.7x difference)
- Ever cancelled → **59.6% churn** vs Never cancelled → **8.55% churn** (7x difference)

### Finding 3: Acquisition Channel Quality Varies 5x
- Channel 7: **4.5% churn** (462,684 users — best quality, largest volume)
- Channel 4: **23.1% churn** (52,744 users — worst quality, 5x higher churn)
**Business action:** Shift acquisition budget toward Channel 7, investigate Channel 4.

### Finding 4: Plan Length is a Measurement Artifact
Longer plans appear to churn more — but this is a metric definition issue,
not a real behavioral difference. Demonstrates the importance of understanding
how metrics are defined before drawing conclusions.

### Finding 5: Churn is Behavior-Driven, Not Tenure-Driven
Churn rate is flat across account age (7.3% to 10.4%) — unlike typical SaaS patterns.
KKBox churn is driven by behavior (auto-renewal, cancellations), not by tenure.

## Model Results

| Model | AUC | Recall |
|---|---|---|
| Baseline A (majority class) | 0.500 | 0% |
| Baseline B (auto-renew only) | 0.775 | 0% |
| Logistic Regression | 0.953 | 87.5% |
| **Random Forest** | **0.980** | **94.0%** |

## Risk Tiering

| Tier | % of Users | Actual Churn Rate | Action |
|---|---|---|---|
| Low Risk | 50% | 0.02% | No action |
| Medium Risk | 30% | 1.02% | Monitor |
| **High Risk** | **20%** | **43.4%** | **Prioritize retention** |

## Business Impact

| Intervention Success Rate | Users Saved | Net Revenue |
|---|---|---|
| 10% (pessimistic) | 1,684 | $81,965 |
| **20% (base case)** | **3,369** | **$241,703** |
| 30% (optimistic) | 5,054 | $401,441 |

Budget saved vs untargeted approach: **$310,706 (5x more efficient)**

*Pricing assumption: $7.90/month × 12 months LTV, $2/user intervention cost*
*Source: Singtel/KKBox carrier agreement, 2017 era*

## A/B Test Design (Proposed Production Validation)
- Control: 495 High Risk users — no intervention
- Treatment: 495 High Risk users — retention offer
- Remaining 37,849 High Risk users: treat all immediately
- Duration: 30 days | Primary metric: renewal rate | Secondary: revenue per user
- Required sample: 990 users (well-powered with 38,839 available)

## Data Quality Decisions
- `bd` (age): dropped — 67% zero or negative values
- `gender`: dropped — 65% missing
- `discount` field: dropped — only 0.67% of users received one
- No-transaction users: flagged (not dropped) — 78.7% churn rate is a real signal
- Missing members data: filled with -1 (unknown), not dropped

## Limitations
- `user_logs_v2.csv` not incorporated — would improve model further
- Intervention success rate (20%) is an industry assumption — A/B test produces real number
- Payment method and registration channel codes not decoded — lookup table not available
- Single transaction snapshot — time-series features would strengthen signals

## Tech Stack
Python, pandas, numpy, scikit-learn, matplotlib, Google Colab

## How to Reproduce
1. Join the [KKBox Churn Prediction Challenge](https://www.kaggle.com/competitions/kkbox-churn-prediction-challenge)
2. Download and extract: `members_v3.csv`, `transactions_v2.csv`, `train_v2.csv`
3. Upload to Google Drive in a folder called `kkbox_project`
4. Open `kkbox_churn_analysis.ipynb` in Google Colab
5. Runtime → Restart and Run All

## Author
End-to-end analytics project demonstrating: data quality assessment, exploratory analysis,
feature engineering, model comparison with honest baselines, business translation,
and experiment design.
