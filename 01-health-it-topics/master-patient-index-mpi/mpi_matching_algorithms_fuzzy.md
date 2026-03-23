# Beyond F-S: Fuzzy Matching with Levenshtein Distance

While **Fellegi-Sunter (F-S)** provides the statistical framework for *combining* scores, the system needs a way to compare strings that are similar but not identical (e.g., typos). The most common algorithm for this, starting with **L**, is **Levenshtein Distance**.

## 1. What is Levenshtein Distance?
Also known as **Edit Distance**, it measures the minimum number of single-character changes required to transform one string into another. These changes include:
*   **Insertions** (adding a character)
*   **Deletions** (removing a character)
*   **Substitutions** (replacing a character)

### Example Scores:
| String A | String B | Levenshtein Distance | Intuition |
| :--- | :--- | :--- | :--- |
| `SMITH` | `SMITH` | **0** | Exact Match |
| `SMITH` | `SMTH` | **1** | Minor Typo (Deletion) |
| `SMITH` | `SMYTH` | **1** | Variation (Substitution) |
| `JOHN` | `JONATHON` | **4** | Major Difference |

---

## 2. Integration with Fellegi-Sunter
In a basic F-S implementation, you use **Binary Matching** (Equals = Full Weight, Not Equals = Negative Weight). With Levenshtein, you can use **Fuzzy Weighting**.

### The Logic:
Instead of a binary choice, the system awards a **partial weight** based on the distance.
*   **Distance 0**: Award 100% of the field weight.
*   **Distance 1**: Award 70% of the field weight (Assume a typo).
*   **Distance 2+**: Award 0% (or negative weight).

```php
// Laravel Implementation Detail
public function getFuzzyWeight($fieldWeight, $dist)
{
    if ($dist === 0) return $fieldWeight;
    if ($dist === 1) return $fieldWeight * 0.7; // "Close enough"
    return -$this->config->getMismatchPenalty();
}
```

---

## 3. Other "L" Algorithms and Concepts

### A. k-mers (or n-grams)
Used for **Blocking** (the "Filter" step). By breaking names into overlapping chunks (e.g., `SMIT`, `MITH`), the system can quickly find records that share "pieces" of a name, even if the whole string is slightly different.

### B. Linear / Logistic Regression
In advanced "Next-Gen" MPIs, machine learning models (like **Logistic Regression**) are used to "learn" the F-S weights automatically from a set of known matches, rather than having a human tune them manually.

### C. Lahiri-Larsen Method
A specialized extension of the Fellegi-Sunter theory that provides more robust estimates for the $M$ and $U$ probabilities when the true match status is unknown.

---

## 4. Why Levenshtein is the "Gold Standard" for Names
1.  **Typo Tolerance**: It captures common data entry errors (transposing letters, missing vowels).
2.  **Transliteration Variance**: It handles minor spelling variations in global contexts (e.g., "Mohammad" vs. "Mohammed").
3.  **Low Complexity**: It is computationally cheap enough to run on a set of 50-100 candidates fetched from a MySQL query.

---
*Document synthesized on March 12, 2026.*
