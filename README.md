# Disentangle Soccer ⚽  
**Minimalist Bayesian Disentanglement of Player Ability and Opposition Strength**

## Overview
This repository implements a **minimal, interpretable Bayesian model** to disentangle individual attacking ability and opponent defensive strength in professional football (soccer).  
Unlike most football analytics approaches, this work relies **only on goals, assists, and minutes played**, avoiding engineered features such as xG, shot locations, or tracking data.

Despite its simplicity, the model achieves strong calibration and meaningful player rankings through a **hierarchical Poisson formulation** trained using **variational inference**.

---

## Key Idea
We model goal contributions as a Poisson process whose rate decomposes into:
- **Player attacking strength**
- **Opponent defensive strength**
- **Exposure (minutes played)**

This enables clean separation of *who scored* from *who they scored against*.

---

## Data
The dataset is constructed from **StatsBomb open data** using `statsbombpy`, covering multiple La Liga seasons.

For each match and player, we extract:
- Goals  
- Assists  
- Minutes played (converted to exposure)  
- Opponent identity  

No positional, spatial, or event-sequence information is used.

---

## Models

### 1. Player-Only Poisson Model
Each player is assigned a single attacking parameter.

**Limitation:**  
Confounds player ability with match difficulty.

---

### 2. Player + Global Intercept (α)
Adds a league-wide intercept to absorb overall scoring rate.

**Limitation:**  
Still cannot account for opponent-specific defensive strength.

---

### 3. Hierarchical Player–Opponent Model (Main Model)
The final model decomposes the scoring rate as:
\[
\lambda_{i,m} = \exp(\theta_i - \gamma_{o(m)}) \times \text{exposure}_{i,m}
\]

Where:
- \( \theta_i \): player attacking strength  
- \( \gamma_o \): opponent defensive strength  

This formulation enables true disentanglement and counterfactual analysis.

---

## Inference
Parameters are estimated using **Coordinate Ascent Variational Inference (CAVI)** under a mean-field Gaussian approximation.

- Fast and deterministic
- ELBO tracked during optimization
- Scales well to multi-season data

---

## Evaluation
We evaluate the model using:
- Poisson log-likelihood vs a global-rate null model
- Calibration plots (mean predicted λ vs mean observed goals)
- Player-level correlation with goals per 90
- Ablation studies across model variants
- Counterfactual analysis of opposition strength

Low per-row correlation is expected due to Poisson noise; player-level aggregation yields strong agreement with observed scoring rates.

