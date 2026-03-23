# Fellegi-Sunter (F-S) Theory: The Foundation of Probabilistic Linkage

## 1. Introduction
**Fellegi-Sunter (F-S) Theory** is the foundational statistical framework for probabilistic record linkage. Developed in 1969 by Ivan Fellegi and Alan Sunter, it provides the mathematical basis for deciding whether two records (e.g., patient records in different clinical systems) refer to the same real-world entity when the data is noisy, incomplete, or inconsistent.

In the context of a Master Patient Index (MPI), F-S theory moves beyond simple "equals/not-equals" matching to a weighted scoring system that quantifies the likelihood of a match.

---

## 2. Core Concepts: The Probability Framework
The theory is built on the relationship between two probabilities for any given data field (like Date of Birth, Last Name, or Phone Number):

### M-Probability (Match Probability)
*   **Definition**: The probability that a field agrees given that the two records **are** a match.
*   **Intuition**: If two records belong to the same person, how likely is it that the field is identical? High-integrity fields like National ID have a very high M-probability.

### U-Probability (Unmatch Probability)
*   **Definition**: The probability that a field agrees given that the two records **are not** a match.
*   **Intuition**: How likely is it that two different people share this value by pure chance? For example, the name "John" has a high U-probability in many populations, while a unique phone number has a very low U-probability.

---

## 3. The Scoring Mechanism
The "strength" of a match for a specific field $i$ is calculated as a weight based on the ratio of these probabilities:

$$ \text{Weight}_i = \log \left( \frac{P(\text{field}_i \text{ agrees} \mid \text{Match})}{P(\text{field}_i \text{ agrees} \mid \text{Unmatch})} \right) $$

### Cumulative Total Score
The **Total Score** for a pair of records is the sum of weights across all compared fields.
*   **Positive Weights**: Awarded when fields match. Rare values (like a unique surname) award higher positive weights than common values.
*   **Negative Weights**: Awarded when fields disagree. A disagreement in a stable field (like Year of Birth) carries a heavy negative weight, while a disagreement in a volatile field (like Phone Number) may carry a lighter penalty.

---

## 4. Decision Thresholds: The Three Zones
F-S theory does not force a binary "Yes/No" decision. Instead, it defines three operational zones based on the total score:

| Score Range | Meaning | MPI Operational Action |
| :--- | :--- | :--- |
| **High Score** | **Match** | **Auto-Link**: The system automatically asserts identity truth. |
| **Middle Score** | **Clerical Review** | **Stewardship Queue**: A human data steward must adjudicate the potential link. |
| **Low Score** | **Non-Match** | **Ignore**: The records are treated as distinct individuals. |

---

## 5. Why F-S Theory is Used in Modern MPIs
Despite the rise of machine learning, F-S theory remains the industry standard for several reasons:

1.  **Explainability**: Unlike "black box" AI models, F-S scoring is fully transparent. An auditor can see exactly which fields contributed to a match and why.
2.  **Statistical Rigor**: It provides a mathematical basis for setting thresholds that minimize both **false positives** (incorrectly merging two people) and **false negatives** (failing to identify a duplicate).
3.  **Efficiency**: It allows systems to quickly "block" or "index" candidates, ensuring that the heavy math is only performed on likely pairs rather than comparing every record to every other record.

## 6. Name Matching Use Cases & Samples
In a Master Patient Index, Fellegi-Sunter theory is most commonly applied to name components (First, Middle, Last, and Suffix). Each field is weighted differently based on its "uniqueness" and "reliability."

### Field-Level Logic for Names

| Name Component | M-Probability (Reliability) | U-Probability (Uniqueness) | F-S Role |
| :--- | :--- | :--- | :--- |
| **Last Name** | **High** | **Low (Variable)** | The primary anchor. Matches on rare surnames (e.g., "Muin") award higher scores than common ones (e.g., "Smith"). |
| **First Name** | **High** | **Moderate** | Strong secondary signal. "John" has a high U-prob (low weight), while "Zebulon" has a low U-prob (high weight). |
| **Middle Name** | **Low** | **Variable** | **Weak Signal**. Often missing or initialized. A match adds a small bonus; a missing middle name should NOT penalize the score. |
| **Suffix** | **Moderate** | **Low** | **The Safety Brake**. A mismatch between "Jr" and "Sr" should trigger a massive negative weight to prevent accidental father-son merges. |

---

### Sample Scenarios & Scoring Intuition

#### Scenario A: High-Confidence Match (Rare Name)
*   **Record 1**: Zebulon Muin
*   **Record 2**: Zebulon Muin
*   **Scoring**:
    *   **Last Name ("Muin")**: Rare name = **+15.0** (Low U-prob)
    *   **First Name ("Zebulon")**: Rare name = **+12.0** (Low U-prob)
    *   **Total Score**: **27.0** (Result: **AUTO-LINK**)

#### Scenario B: Uncertain Match (Common Name)
*   **Record 1**: John Smith
*   **Record 2**: John Smith
*   **Scoring**:
    *   **Last Name ("Smith")**: Common name = **+4.0** (High U-prob)
    *   **First Name ("John")**: Common name = **+3.0** (High U-prob)
    *   **Total Score**: **7.0** (Result: **STEWARDSHIP REVIEW**)

#### Scenario C: The "Father-Son" Trap
*   **Record 1**: John Smith **Jr.**
*   **Record 2**: John Smith **Sr.**
*   **Scoring**:
    *   **Last Name ("Smith")**: Match = **+4.0**
    *   **First Name ("John")**: Match = **+3.0**
    *   **Suffix (Jr vs Sr)**: **Mismatch** = **-25.0** (Heavy penalty)
    *   **Total Score**: **-18.0** (Result: **DISCARD/NON-MATCH**)

## 7. Summary in an MPI Context
> **Fellegi-Sunter tells you how confident you should be. The MPI system (via its governance and workflow) decides what to do with that confidence.**

---
*Document synthesized on March 12, 2026.*
