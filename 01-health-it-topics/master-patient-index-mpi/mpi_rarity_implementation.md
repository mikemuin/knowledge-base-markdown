# Implementation: Rarity Detection in an MPI

In Fellegi-Sunter theory, the **U-probability** (the chance that two different people share a name) is derived from the **frequency** of that name in the population. A system "knows" what is rare by maintaining a dedicated frequency table.

## 1. The Frequency Table (MySQL)
The system tracks how often every name occurs across the entire patient population. This table is updated via periodic background jobs.

```sql
CREATE TABLE mpi_name_frequencies (
    name_type   ENUM('first', 'last') NOT NULL,
    name_value  VARCHAR(100) NOT NULL, -- Normalized (e.g., 'SMITH')
    occurrence_count INT UNSIGNED DEFAULT 1,
    u_probability    DOUBLE DEFAULT 0.0, -- Calculated field
    PRIMARY KEY (name_type, name_value)
);
```

### The Calculation
*   **Total Records (N)**: 1,000,000
*   **Occurrences of "SMITH" (C)**: 10,000
*   **U-Probability ($U$)**: $C / N = 0.01$ (1%)
*   **Occurrences of "MUIN" (C)**: 5
*   **U-Probability ($U$)**: $5 / 1,000,000 = 0.000005$ (0.0005%)

## 2. Automated Frequency Updates (Laravel)
Since recalculating the entire database on every registration is too slow, use a Laravel Scheduled Task to update frequencies during off-peak hours.

### The Background Job Logic
1.  **Count** every unique first and last name in the `mpi_patient_index`.
2.  **Calculate** the $U$ value for each name.
3.  **Upsert** the results into `mpi_name_frequencies`.

```php
// App\Jobs\UpdateNameFrequencies.php
public function handle()
{
    $total = PatientIndex::count();

    // Example for Last Names
    $frequencies = PatientIndex::select('last_name_norm', DB::raw('count(*) as cnt'))
        ->groupBy('last_name_norm')
        ->get();

    foreach ($frequencies as $f) {
        NameFrequency::updateOrCreate(
            ['name_type' => 'last', 'name_value' => $f->last_name_norm],
            [
                'occurrence_count' => $f->cnt,
                'u_probability' => $f->cnt / $total
            ]
        );
    }
}
```

## 3. Handling "New" Names
If a name is so rare it isn't in the frequency table yet (e.g., a first-time entry), the system defaults to a "Global Rareness Constant" (e.g., $1 / N$) to ensure it receives a high match weight.

---
*Document synthesized on March 12, 2026.*
