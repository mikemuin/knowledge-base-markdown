# Implementation: The MPI Matching Engine (Laravel & MySQL)

The matching engine follows a "Filter → Fetch → Score" pipeline to handle the millions of potential combinations efficiently.

## 1. The "Blocking" Filter (MySQL)
Never compare the incoming record to every record in the database. Instead, use "blocking" to find a small set of candidates (e.g., 50-100) based on high-integrity attributes like **Date of Birth** or **Normalized Full Name**.

```sql
-- Fast initial search
SELECT * FROM mpi_patient_index
WHERE dob = '1985-03-12'
   OR full_name_norm IN ('SMITHJOHN', 'JOHNSMITH');
```

## 2. Real-Time Weight Calculation (Laravel Service)
Once the system has candidates, it iterates through them to calculate the Fellegi-Sunter total score.

### Calculating Field Weight
The system calculates weight using the formula: $log(M / U)$
*   **M-Probability**: A constant for each field (e.g., $0.95$ for First Name, representing that 5% of first names are typos).
*   **U-Probability**: Fetched from the `mpi_name_frequencies` table.

```php
// App\Services\MPI\MatchingEngine.php

public function calculateFieldWeight($fieldName, $value)
{
    $mProb = $this->config->getMProbability($fieldName); // e.g., 0.95
    $uProb = NameFrequency::getUProbability($fieldName, $value); // e.g., 0.00001

    // Weight = log(M / U)
    return log10($mProb / $uProb);
}
```

## 3. The Comparison Flow
The Laravel service performs the following logic for each candidate:

1.  **Iterate** through fields: First, Last, Middle, Suffix.
2.  **Compare** values:
    *   **If Match**: Add `calculateFieldWeight($field, $val)`.
    *   **If Disagree**: Subtract a "Disagreement Weight" (typically pre-configured based on $log((1-M) / (1-U))$).
    *   **If Missing**: Skip (Score = 0) or add a tiny penalty.
3.  **Aggregate** the total score.
4.  **Evaluate Thresholds**: Compare the final score against `AUTO_LINK_THRESHOLD` (e.g., 18.0) and `REVIEW_THRESHOLD` (e.g., 8.0).

## 4. Why this works for Suffixes
A mismatch in suffixes is given a massive **disagreement weight** (e.g., -25.0).
```php
if ($record->suffix !== $candidate->suffix) {
    $totalScore -= $this->config->getSuffixMismatchPenalty(); // -25.0
}
```
This ensures that even if "John Smith Jr" and "John Smith Sr" have identical names and birthdays, they will **never** trigger an auto-merge.

## 5. Summary Implementation Architecture
*   **MySQL**: Indexes for candidate selection and storage of frequencies.
*   **Laravel Service**: The mathematical heavy lifting and business logic.
*   **Laravel Job**: Background worker to refresh name frequencies.
*   **Redis (Optional)**: Cache the `mpi_name_frequencies` for ultra-fast lookup during registration.

---
*Document synthesized on March 12, 2026.*
