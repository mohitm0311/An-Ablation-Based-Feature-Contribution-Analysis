# Meaningful Mentor–Entrepreneur Matching  
## A Feature Contribution Analysis via Ablation Study

This repository documents a research project conducted to **identify which factors materially contribute to meaningful mentor–entrepreneur matches** on an entrepreneurial mentoring platform.

The goal of this work is **not** to maximize classification accuracy, but to understand **which feature groups are *necessary* for a match to result in real mentor engagement**, and to identify the limits of commonly assumed matching signals under real platform constraints.

---

## Context and Research Background

This project is part of a broader research line examining mentor–entrepreneur interactions on digital platforms.

Specifically:

> This repository documents a research project conducted to construct and evaluate mentor–entrepreneur interactions using modern data-driven methods.

> The goal of this work is not to maximize predictive performance, but to understand whether meaningful and interpretable drivers of mentor engagement can be identified from sparse, self-reported platform data — and to identify the intrinsic limits of such analysis.

> This research was conducted as part of a research project at **Cambridge Judge Business School**.

Building on that research direction, the present project shifts focus from **semantic analysis** to **behavioral outcomes**, asking a complementary question:

> **Once a mentor–entrepreneur match exists, what information actually determines whether the mentor meaningfully engages?**

---

## What This Repository Intentionally Documents

This repository is designed as a **research artifact**, not a production ML system. It intentionally documents:

- the final methodology and empirical results  
- the experimental reasoning behind key design choices  
- rejected or abandoned approaches (and why they failed)  
- the conceptual insights gained during the process  

The emphasis is on **understanding**, not optimization.

---

## 1. Research Motivation

Most matching systems implicitly assume that better matching can be achieved by:
- maximizing similarity (industry, language, demographics)
- increasing feature volume
- optimizing predictive accuracy

However, these assumptions are rarely tested under:
- real platform constraints  
- extreme class imbalance  
- sparse, voluntary user-entered data  
- human behavioral outcomes  

This project asks a more fundamental question:

> **What information is actually *necessary* for a mentor–entrepreneur match to become meaningful?**

---

## 2. Dataset Overview

The original dataset consists of approximately **2 million conversation-level records** from an entrepreneurial mentoring platform.

Each record may include:
- two participants (`person1`, `person2`)
- role indicators (mentor / entrepreneur)
- country codes
- optional profile metadata
- conversation logs and timestamps

---

## 3. Regional Scope: Indonesia

To reduce cross-regional heterogeneity and control for institutional and cultural effects, the analysis is restricted to **Indonesia-involved matches**, defined as dyads where **at least one participant reports country code `ID`**.

After regional filtering:
- **17,690 rows** remained

---

## 4. Role Consistency Constraint

To ensure interpretability, the dataset is further restricted to **valid dyads** containing:

- exactly **one mentor**
- exactly **one entrepreneur**

Rows containing mentor–mentor or entrepreneur–entrepreneur pairings were excluded.

After enforcing this constraint:
- **9,829 valid mentor–entrepreneur dyads** remained

---

## 5. Defining “Meaningful Matching”

Conversation creation does not guarantee engagement.

Message counts alone are insufficient, as conversations may be entirely one-sided.

### Final operational definition

> **A match is considered meaningful if the mentor sends at least one message.**

This definition:
- captures bidirectional engagement  
- avoids arbitrary message thresholds  
- reflects the true platform bottleneck  

Resulting label distribution:
- ~98% successful matches  
- ~2% failed or one-sided matches  

This extreme imbalance is **expected and informative**, not a modeling flaw.

---

## 6. Why Accuracy-Driven ML Is Inappropriate

Given:
- severe class imbalance  
- behavioral (human) outcomes  
- platform-induced selection effects  

Standard ML pipelines (e.g., tree-based importance, accuracy metrics) would produce misleading conclusions.

Instead, this project adopts an **ablation-based framework** to measure *necessity*, not correlation.

---

## 7. Feature Availability and Missingness

A systematic missingness audit revealed:
- many profile fields with **80–99% missing values**
- optional free-text fields largely absent
- strong heterogeneity in data quality

### Key interpretation
> The primary limitation of the analysis is **data availability**, not model capability.

To ensure stability and interpretability:
- features with >80% missingness were excluded  
- analysis focuses on **high-coverage, reliable signals**

---

## 8. Final Feature Groups (Ablation Units)

Features are grouped conceptually rather than analyzed individually.

### Language & Communication
- mentor preferred language  
- entrepreneur preferred language  

### Industry Alignment
- mentor industry  
- entrepreneur venture industry  

### Mentor Experience & Credibility
- management experience  
- ownership experience  
- number of endorsements  

### Venture Maturity
- venture stage  

These groups reflect **theoretical matching dimensions**, not arbitrary variables.

---

## 9. Modeling Framework

- Penalized logistic regression  
- Class-weighted loss  
- No interaction terms  
- No text embeddings (initial phase)  

### Evaluation metric
- **ROC–AUC**
- Accuracy intentionally avoided due to imbalance

---

## 10. Baseline Performance

Using all retained feature groups:
Baseline ROC–AUC ≈ 0.664
This confirms that:
- non-trivial signal exists  
- the model is not learning noise  
- ablation comparisons are meaningful  

---

## 11. Ablation Study Methodology

The ablation protocol follows a strict procedure:

1. Train baseline model with all feature groups  
2. Remove one feature group at a time  
3. Retrain the same model  
4. Measure decrease in ROC–AUC (Δ AUC)  

### Interpretation rule

> **Larger Δ AUC ⇒ greater necessity for meaningful mentor engagement**

---

## 12. Ablation Results

| Feature Group | AUC After Removal | Δ AUC |
|--------------|------------------|-------|
| Mentor Experience | 0.6288 | **0.0352** |
| Venture Maturity | 0.6402 | **0.0238** |
| Industry Alignment | 0.6467 | **0.0173** |
| Language Alignment | 0.6601 | **0.0039** |

---

## 13. Key Findings

- Mentor experience is the dominant driver of engagement  
- Venture maturity matters more than industry similarity  
- Industry alignment is helpful but secondary  
- Language alignment acts as a gatekeeper, not a differentiator  

Once basic constraints are satisfied, **who the mentor is** matters more than surface similarity.

---

## 14. Ablation Plot

![Ablation Feature Importance](Graphs/ablation_feature_importance.png)

*Decrease in ROC–AUC after removing each feature group. Larger decreases indicate greater contribution to meaningful mentor engagement.*

---

## 15. Why Ablation Was Chosen

Ablation was preferred over SHAP or tree-based importance because it:
- avoids bias under correlated features  
- remains stable under class imbalance  
- operates at a conceptual group level  
- aligns with causal reasoning  

This approach answers:
> *What information is actually necessary?*

---

## 16. Limitations

- Outcome reflects engagement, not long-term success  
- Text features excluded from primary analysis  
- Platform design suppresses failure cases  
- No counterfactual acceptance data  

These are **structural limitations**, not modeling failures.

---

## 17. Future Work

- Text-based feature ablation (goals, challenges)  
- Multi-stage matching funnel analysis  
- Cross-region comparison  
- Causal modeling with acceptance logs  

---

## 18. Data Availability and Ethics

The dataset contains sensitive, proprietary information and cannot be shared.

This repository includes:
- preprocessing logic  
- modeling code  
- evaluation scripts  
- derived figures  

ensuring reproducibility under appropriate data access constraints.

---

## 19. Conclusion

This work demonstrates that **meaningful mentor–entrepreneur matching is driven primarily by mentor experience and venture maturity**, rather than surface-level similarity signals.

The primary contribution is not a classifier, but a **methodologically sound diagnosis of what truly matters — and what does not — in real mentoring interactions**.