# Global Name Handling and Identity Matching in the Master Patient Index (MPI)

**Target Audience:** Project Managers, Systems Analysts, and the Development Team
**Context:** Localized implementation for Philippine naming conventions.

---

## 1. The Challenge of Philippine Naming Conventions

Out-of-the-box Western Master Patient Index (MPI) algorithms often fail when applied to Philippine demographic data. The local context presents unique challenges that break standard "First, Middle, Last" structural expectations:

1.  **Suffix Entanglement:** Legacy systems often lack a dedicated suffix field. Suffixes ("Jr.", "III") are frequently appended to either the First Name ("Michael Richard Jr.") or the Last Name ("Santos Jr.").
2.  **Incomplete First Names:** Dual or compound first names ("Michael Richard") are often abbreviated by registration clerks to just the primary name ("Michael").
3.  **The Maternal Middle Name:** "Middle Name" in the Philippines traditionally refers to the mother's maiden name. It is frequently abbreviated to an initial ("R." instead of "Reyes"), which is a highly vital piece of identity evidence.
4.  **The Ubiquity of Nicknames:** Nicknames are heavily used even in formal settings ("Mike" for "Michael", "Jun" for "Junior", "Bong" for "Ferdinand"). Standard phonetic algorithms (like Soundex or Metaphone) fail to link "Mike" and "Michael" because they sound and are structurally different.
5.  **The "Ma." Prefix:** "Maria" is extraordinarily common and is routinely abbreviated to "Ma." or "Ma". This acts as a prefix to a second first name ("Ma. Theresa"). Furthermore, it is sometimes dropped entirely by the patient or clerk ("Theresa" instead of "Maria Theresa").
6.  **Titles in Name Fields:** Professional or cultural titles ("Atty.", "Dr.", "Engr.", "Datu") are often incorrectly entered into the First Name field.
7.  **The "Enye" (ñ) and Diacritic Corruption:** The letter "ñ" is ubiquitous in Philippine surnames. Legacy systems notoriously corrupt this character, often storing it as `Pe?a`, `Pe a`, `PeÃ±a`, or `Pena`.
8.  **Hyphenated Dual Names:** Modern naming trends sometimes use hyphens to bind dual first names ("Mary-Grace" vs "Mary Grace"). Different systems handle this punctuation differently.
9.  **The "De" / "Delos" Spacing Problem:** Names with prepositions (De La Cruz, Delos Santos, San Juan) are typed with wildly varying spacing across different hospitals (e.g., `Dela Cruz`, `De La Cruz`, `Delacruz`).

If the MPI search engine has to account for all these variations on-the-fly during a database query, performance will collapse. The solution is to handle these variations *during data ingestion*, before the record is saved to the search index layer.

---

## 2. Strategic Solution: The Ingestion Pipeline

To ensure the MPI's `mpi_patient_index` (the perception/search layer) is highly performant and accurate, we must expand the storage representations of a patient's name. We shift the computational burden to an **Ingestion Pipeline**.

When a raw record enters the system, the pipeline cleans, reorganizes, tokenizes, and expands the data *before* inserting it into the explicitly defined database columns.

### The Three Categories of Name Representation

The expanded schema explicitly stores variations across three categories:

#### A. Fidelity Representations (What was actually typed)
These fields preserve the raw input exactly as it arrived from the source system. They are not used for primary matching but are critical for human Data Stewards during manual review.

*   **`raw_full_name`**: The exact concatenated string (e.g., `Michael Richard Reyes Santos Jr.`).

#### B. Structural Representations (Reorganized for the Algorithm)
These fields handle the reorganization of names, stripping punctuation, moving misplaced suffixes/titles, and tokenizing compound names.

*   **`name_first_canon`**: The cleaned First Name. Punctuation stripped, suffixes/titles removed, abbreviations expanded ("Ma." replaced with "MARIA").
*   **`name_middle_canon`**: The cleaned Middle Name.
*   **`name_last_canon`**: The cleaned Last Name.
*   **`name_suffix_ext`**: A dedicated column for extracted suffixes (e.g., `JR`, `III`). If found in the raw First or Last name, the pipeline moves it here.
*   **`name_prefix_ext`**: A dedicated column for extracted titles (e.g., `ENGR`, `ATTY`).
*   **`tokens_first` (JSON)**: An array separating the canonical first name into distinct elements. `["MARIA", "THERESA"]`. Crucial for matching incomplete data.
*   **`tokens_middle` (JSON)**: An array for the middle name. Crucial for matching compound maternal names (e.g., "De Los Reyes" vs "Delos Reyes").
*   **`tokens_last` (JSON)**: An array for the last name.

#### C. Algorithmic Representations (Optimized for Database Speed)
These fields collapse the data into formats optimized for B-tree database indexes, alias matching, and phonetic comparisons.

*   **`aliases_first` (JSON)**: An array populated by a local Nickname Dictionary. If the raw name is "Mike", the dictionary expands this to `["MIKE", "MICHAEL"]`.
*   **`idx_compressed_name`**: The canonical names concatenated with absolutely no spaces or punctuation (e.g., `MARIATHERESAREYESDELOSSANTOS`). This provides an extremely fast, highly selective index for exact global matches.
*   **`idx_alt_order`**: The canonical names concatenated in reverse order (Last, First, Middle). This catches the common clerk error of swapping name fields during entry.
*   **`idx_phonetic_first` / `idx_phonetic_last`**: The phonetic encoding (e.g., Double Metaphone) of the canonical names to catch misspellings.

---

## 3. Database Schema Implementation (`mpi_patient_index`)

The resulting schema for the search index layer looks like this:

```sql
CREATE TABLE mpi_patient_index (
  patient_id            VARCHAR(32) PRIMARY KEY,

  -- Fidelity
  raw_full_name         VARCHAR(300),

  -- Structural (Cleaned & Extracted)
  name_first_canon      VARCHAR(100),
  name_middle_canon     VARCHAR(100),
  name_last_canon       VARCHAR(100),
  name_suffix_ext       VARCHAR(20),  -- e.g., 'JR'
  name_prefix_ext       VARCHAR(20),  -- e.g., 'ENGR'

  -- Structural (JSON Arrays for Token Searching)
  tokens_first          JSON,         -- ["MARIA", "THERESA"]
  tokens_middle         JSON,         -- ["DELOS", "REYES"]
  tokens_last           JSON,         -- ["DE", "LOS", "SANTOS"]

  -- Algorithmic / Indexing
  aliases_first         JSON,         -- ["MIKE", "MICHAEL"]
  idx_compressed_name   VARCHAR(300), -- MARIATHERESAREYESDELOSSANTOS
  idx_alt_order         VARCHAR(300), -- DELOSSANTOSMARIATHERESAREYES
  idx_phonetic_first    VARCHAR(50),
  idx_phonetic_last     VARCHAR(50),

  -- Metadata
  updated_at            TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
);
```

---

## 4. How the Matching Engine Uses the Schema

By explicitly storing these variations, the Fellegi-Sunter matching algorithm handles edge cases through specialized string comparator rules rather than requiring exact text matches.

### Scenario Matrix: Ingestion & Matching

| Scenario | Raw Data Entered | `name_first_canon` | Extracted Elements | JSON Arrays (`tokens_first` / `aliases_first`) | How the Algorithm Matches the Gold Record |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Gold Standard** | `Michael Richard / Reyes / Santos / Jr.` | `MICHAEL RICHARD` | Suffix: `JR` | Tokens: `["MICHAEL", "RICHARD"]` | N/A (Baseline for comparison) |
| **Suffix in First Name** | `Michael Richard Jr. / Reyes / Santos / (null)` | `MICHAEL RICHARD` | Suffix: `JR` | Tokens: `["MICHAEL", "RICHARD"]` | The Ingestion Pipeline strips `Jr.` out of the first name. The `name_first_canon` becomes a 100% exact match to the Gold Standard. |
| **Incomplete First Name** | `Michael / Reyes / Santos / Jr.` | `MICHAEL` | Suffix: `JR` | Tokens: `["MICHAEL"]` | The exact string fails. However, the algorithm queries the `tokens_first` JSON array. `["MICHAEL"]` perfectly intersects with the Gold Standard's tokens, resulting in a high partial-match score. |
| **Nickname** | `Mike / Reyes / Santos / Jr.` | `MIKE` | Suffix: `JR` | Aliases: `["MIKE", "MICHAEL"]` | The algorithm queries the `aliases_first` column. It sees `MICHAEL` in the array and recognizes the transitive relationship, flagging it as a strong match. |
| **The "Ma." Prefix** | `Ma. Theresa / Lim / Sy / (null)` | `MARIA THERESA` | (null) | Tokens: `["MARIA", "THERESA"]` | The pipeline uses **Directional Expansion**. Because the token is exactly "MA" or "MA.", it expands it to "MARIA" prior to indexing. An incoming record of "Maria Theresa" will result in a 100% exact match. |
| **Initial for Middle Name (Mother's Name)** | `Michael Richard / R. / Santos / Jr.` | `MICHAEL RICHARD` | (null) | Tokens: `["MICHAEL", "RICHARD"]` | `name_middle_canon` is exactly 1 string long. The algorithm triggers the "Initial Match" rule, comparing "R" to the first letter of "REYES", granting a high confidence score. |

---

## 5. Requirements for the Development Team

To successfully implement this architecture, the engineering team must build the following components in the middleware layer prior to database insertion:

1.  **Suffix & Prefix Dictionaries:** Maintain static lists of valid suffixes (Jr, Sr, III, IV) and prefixes (Atty, Dr, Engr, Datu) used to strip these values into their explicit extraction columns.
2.  **Directional Expansion Rules:** Implement regex rules on word boundaries to expand recognized abbreviations (e.g., `\bMA\.?\b` → `MARIA`).
3.  **Local Nickname Dictionary:** Build a lookup table pairing common nicknames to canonical names (e.g., `Bong` → `Ferdinand`, `Ting` → `Vicente`) to populate the `aliases_first` JSON column upon ingestion.
4.  **Token Weighting (IDF):** When performing "Token Intersection" matches (Scenario 3), implement logic for **Inverse Document Frequency (IDF)**. Extremely common tokens (like "MARIA" or "JOHN") must be given a significantly lower Fellegi-Sunter weight than rare tokens, preventing false-positive matches based solely on the presence of common names.
5.  **Diacritic Normalization & Corruption Healing:** The pipeline must normalize `ñ` to `n` when generating phonetic and compressed representations. Additionally, it must "heal" common legacy corruptions before they reach the canonical layer:
    *   `Ã±` → `ñ`
    *   `Ã‘` → `ñ`
    *   `?` (within a name string) → `ñ`
6.  **Hyphen Tokenization Rule:** Hyphens must be replaced by a space prior to tokenization, ensuring that `["MARY", "GRACE"]` is generated instead of treating it as a single unbreakable token `["MARYGRACE"]`.
7.  **Preposition Spacing Rule:** The Tokenizer should split on spaces so that `["DE", "LA", "CRUZ"]` and `["DELA", "CRUZ"]` can both be accurately evaluated using the Token Intersection logic.

---

## 6. Registration and Matching Walkthroughs

The following scenarios walk through the lifecycle of patient registration—from the raw data entry at the hospital front desk, through the Ingestion Engine’s transformations, to the Search Engine’s candidate detection queries.

### Scenario 1: The Baseline Registration (Gold Standard)
*The first time this person has ever interacted with the healthcare network.*

**Context:** A clerk enters the patient cleanly with all standard fields.
**Raw Registration Data:**
*   First: `Michael Richard`
*   Middle: `Reyes`
*   Last: `Santos`
*   Suffix: `Jr.`
*   DOB: `1985-10-15`

**Step 1: The Ingestion Engine Pipeline**
The middleware intercepts the HL7 ADT message or API payload before inserting it into `mpi_patient_index`.
```text
// Pseudocode: Ingestion Pipeline
nameFirstCanon = CleanAndStrip(Raw.First)  // Generates "MICHAEL RICHARD"
suffixFound = ExtractSuffix(Raw.First) || Raw.Suffix // Finds "JR"
ExpandAliases("MICHAEL RICHARD") // Yields ["MIKE"]
Tokenize("MICHAEL RICHARD") // Yields ["MICHAEL", "RICHARD"]
GeneratePhonetics("MICHAEL RICHARD") // Yields "MKL RXRT"
```

**Step 2: Database Insertion (Perception Layer)**
The engine inserts the structured data into the `mpi_patient_index`.
```sql
INSERT INTO mpi_patient_index (
  patient_id, name_first_canon, name_middle_canon, name_last_canon,
  name_suffix_ext, tokens_first, aliases_first, idx_compressed_name, dob
) VALUES (
  'PAT-001', 'MICHAEL RICHARD', 'REYES', 'SANTOS',
  'JR', '["MICHAEL", "RICHARD"]', '["MIKE"]', 'MICHAELRICHARDREYESSANTOS', '1985-10-15'
);
```
**Outcome:** A clean baseline record is created. No duplicates found.

---

### Scenario 2: The Nickname & Suffix Mismatch

*Two years later, the same patient arrives at an Emergency Room across town.*

**Context:** The patient is unconscious. The spouse registers him quickly under his nickname, and the ER clerk enters the suffix in the first-name field.
**Raw Registration Data:**

*   First: `Mike Jr.`
*   Middle: `Reyes`
*   Last: `Santos`
*   DOB: `1985-10-15`

**Step 1: The Ingestion Engine Pipeline**

```text
// Pseudocode: Ingestion Pipeline
nameFirstCanon = CleanAndStrip("Mike Jr.") // Finds and removes "Jr.", leaving "MIKE"
suffixFound = "JR"
ExpandAliases("MIKE") // Dictionary lookup yields ["MICHAEL"]
Tokenize("MIKE") // Yields ["MIKE"]
```

**Step 2: The Duplicate Check Query (Before Insertion)**
Before creating a new identity, the system searches the index. Note how the query leverages the JSON arrays, not just the string columns.

```sql
-- Conceptual duplicate search for "Mike"
SELECT
  patient_id,
  name_first_canon,
  name_last_canon,
  dob
FROM mpi_patient_index
WHERE name_last_canon = 'SANTOS'
  AND dob = '1985-10-15'
  AND (
    name_first_canon = 'MIKE' -- Will miss PAT-001
    OR JSON_CONTAINS(aliases_first, '"MIKE"') -- Will hit PAT-001!
  );
```

**Outcome:** The system flags `PAT-001` (`MICHAEL RICHARD`) as a Highly Probable duplicate of the new `Mike Jr.` registration because the aliases intersect seamlessly and the extracted suffix matches, even though the raw `First Name` fields look completely different to the naked eye.

---

### Scenario 3: The Maternal Initial Registration

*The patient registers for a routine lab test at an outpatient clinic.*

**Context:** The clerk only asks for the middle initial.
**Raw Registration Data:**

*   First: `Michael`
*   Middle: `R`
*   Last: `Santos`
*   DOB: `1985-10-15`

**Step 1: The Ingestion Engine Pipeline**

```text
// Pseudocode
nameFirstCanon = "MICHAEL"
nameMiddleCanon = "R" // Suffix/Prefix dictionaries find nothing
Tokenize("MICHAEL") // Yields ["MICHAEL"]
```

**Step 2: The Duplicate Check Query (Partial Matching)**
The engine executes a search. It notices the middle name is only 1 character, triggering the Initial Match logic during the scoring phase.

```sql
-- Conceptual search for incomplete names
SELECT patient_id, name_first_canon, name_middle_canon
FROM mpi_patient_index
WHERE name_last_canon = 'SANTOS'
  AND JSON_CONTAINS(tokens_first, '"MICHAEL"');
  -- Will hit PAT-001, because PAT-001's tokens_first array is ["MICHAEL", "RICHARD"]
```

**Step 3: The Scoring Algorithm (Fellegi-Sunter Application)**
The query returns `PAT-001` as a candidate. The scoring algorithm evaluates the differences:

*   **Last Name:** `SANTOS` == `SANTOS` (100% Match)
*   **First Name:** `MICHAEL` intersects with `["MICHAEL", "RICHARD"]`. (80% Weight for Token Match)
*   **Middle Name:** `R` vs `REYES`. The engine applies the `Length == 1` rule. Does `R` equal `SUBSTRING('REYES', 1, 1)`? Yes. (85% Weight for Initial Match).

**Outcome:** The system confidently surfaces the Gold Standard ID as a high-confidence candidate for merging with the new lab test record, bypassing the structural flaws of the incomplete entry.

---

## 7. Performance Optimization: Multi-Valued Indexing

To maintain high performance during JSON queries (`JSON_CONTAINS`), the database must not perform a full table scan. In **MySQL 8.0.17+**, the engineering team should implement **Multi-Valued Indexes** on the token and alias arrays.

This allows the database to index each individual element within the JSON array as if it were a separate row in a virtual table.

### Recommended Indexing SQL

```sql
-- Index for Token Intersection (First Name)
ALTER TABLE mpi_patient_index
ADD INDEX idx_tokens_first ((CAST(tokens_first AS UNSIGNED ARRAY)));

-- Index for Nickname/Alias Lookup
ALTER TABLE mpi_patient_index
ADD INDEX idx_aliases_first ((CAST(aliases_first AS CHAR(50) ARRAY)));
```

*Note: Use `UNSIGNED` for numeric tokens or `CHAR` for name tokens. This ensures that a query like `WHERE JSON_CONTAINS(aliases_first, '"MICHAEL"')` is fulfilled using an index seek rather than a scan.*

---

## 8. Implementation Detail: Token Weighting & IDF

To prevent common names (like "MARIA" or "DE LA CRUZ") from triggering false-positive matches, the Fellegi-Sunter scoring engine must utilize a **Token Frequency Table**.

Instead of a fixed field weight for First Name, the system calculates a weight for the *specific token* found during the intersection.

### Schema: `mpi_token_frequencies`

| Token | Occurrence Count | Weight (IDF) |
| :--- | :--- | :--- |
| `MARIA` | 450,000 | **0.1** (Extremely Common) |
| `MICHAEL` | 12,000 | **0.6** (Common) |
| `DARLENE` | 450 | **1.2** (Rare) |
| `XAVIERA` | 12 | **2.0** (Unique) |

### Logic for the Scoring Engine
When two records intersect on a token:
1.  Lookup the token in `mpi_token_frequencies`.
2.  If the token is rare (High IDF), award the full match weight.
3.  If the token is common (Low IDF), award only a fractional match weight.

*This ensures that "Maria Theresa" and "Maria Grace" are not incorrectly merged simply because they share the "Maria" token.*

---

## 9. Final Checklist for Handover

Before moving to the build phase, the development team must verify:
- [ ] **Nickname Dictionary:** Is the JSON source for `aliases_first` populated with Philippine-specific pairings (e.g., *Jun-Jun* → *Junior*)?
- [ ] **Diacritic Logic:** Does the ingestion middleware handle the UTF-8 conversion of `ñ` across all source systems?
- [ ] **Threshold Tuning:** Are the Fellegi-Sunter thresholds (`AUTO_LINK` vs `REVIEW`) calibrated to account for the higher partial-match scores generated by token intersections?
