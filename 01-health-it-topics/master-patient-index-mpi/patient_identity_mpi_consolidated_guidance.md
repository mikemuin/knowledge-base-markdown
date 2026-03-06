# Patient Identity & MPI — Consolidated Guidance

## 1. The core problem (why this exists)

- Healthcare systems create **multiple patient records for the same human**
- Duplicates are silent, cumulative, and dangerous
- You cannot safely fix this by deleting or merging patient records in place

**Key insight**

> *Patient IDs represent records.
> Person IDs represent reality.*

------

## 2. The only safe identity model

**Correct direction**

```
person (canonical human)
  ├─ patient_id (HIS)
  ├─ patient_id (EMR)
  ├─ patient_id (LIS)
  ├─ patient_id (RIS)
```

**Never**

- Merge patient_ids directly
- Rewrite clinical history
- Make person_id the EMR primary key early

**Always**

- Merge persons
- Link records
- Preserve history
- Allow unmerge

------

## 3. Where person_id lives (critical)

- Do **not** retrofit EMR/HIS first
- Build an **external MPI service**
- EMR remains operationally unchanged
- MPI owns identity logic and lifecycle

------

## 4. Minimum MPI data model

```
person
person_id (UUID)
status (active | retired)

person_patient_xref
person_id
patient_id
source_system
status
link_reason

mpi_patient_index
patient_id
normalized_demographics
```

This gives you:

- Identity anchor
- Safe linking
- Rollback
- Auditability

------

## 5. Registration-time matching (assistive only)

**What registration matching is**

- Duplicate prevention
- Human-assisted selection

**What it is NOT**

- Identity resolution
- Auto-merge

**Rules**

- Show possible matches
- Group by person
- Always allow “Create new”
- Auto-block only on exact national ID / MRN

------

## 6. Matching happens in phases

| Phase             | Behavior                  |
| ----------------- | ------------------------- |
| Registration      | Light, fast, suggest-only |
| Post-registration | Full probabilistic        |
| Batch MPI         | Authoritative             |
| Merge             | Human-approved            |

Risk tolerance increases over time.

------

## 7. Name handling (non-negotiable)

### Structured fields ≠ matching fields

You must store:

- raw (for display/legal)
- canonical (uppercase, clean)
- normalized (collapsed)
- alternate order keys
- phonetic codes

See below

**Prefix**

- Ignore completely

**Suffix (Jr, Sr, III)**

- Use as a safety brake
- Never merge across mismatch blindly

**Middle name**

- Weak signal
- Absence ≠ mismatch

------

## 8. Capitalization truth

- Uppercasing helps **performance and consistency**
- It does **not** solve matching
- Normalization + phonetics + workflow do

------

## 9. Matching algorithm principles

- Deterministic first (IDs)
- Probabilistic second (name + DOB + phone)
- Negative weights and guardrails matter
- Source-system trust matters
- All merges must be reversible

------

## 10. Registration data flow (clean version)

1. Registrar enters data
2. MPI search suggests possible persons
3. User selects existing or creates new
4. EMR creates patient record
5. MPI creates person + link
6. No merges occur at registration

------

## 11. Updates & corrections (critical discipline)

- EMR owns demographic corrections
- MPI listens to updates
- MPI re-evaluates identity
- MPI never overwrites EMR

Every update is a **claim**, not a truth.

------

## 12. Why a person table is mandatory

Without person:

- You can detect duplicates
- You cannot fix them safely

With person:

- No data deletion
- No history rewrite
- No clinical risk
- Full rollback

This is the single most important architectural decision.

------

## 13. Governance before automation

Before auto-merge:

- Define merge authority
- Define thresholds
- Define unmerge SOP
- Separate clinical vs billing trust

No governance → no automation.

------

## 14. One-paragraph executive summary

> A Master Patient Index must treat patient records as immutable facts and identity as a resolvable hypothesis. Introducing a person layer allows duplicates to be detected, reviewed, merged, and reversed without rewriting clinical history or risking patient safety. Registration-time matching should assist users, not decide identity. All authoritative resolution belongs in a dedicated MPI service with auditability, reversibility, and governance.

------

Below is a **deep, concrete breakdown** of the five name representations — what each one is **for**, **how it’s built**, **where it’s stored**, and **how it’s used**. This is the level you want in a design spec.

------

## 1. RAW — *display / legal truth*

### What it is

Exactly what the registrar typed.

### Example

```
prefix_raw = "Dr."
first_raw  = "Chen"
middle_raw = ""
last_raw   = "Wei"
suffix_raw = ""
```

### Purpose

- Legal record
- Patient-visible display
- Audit & corrections
- Source of truth for disputes

### Rules

- Never altered by MPI
- Never used for matching
- Always preserved

### Storage

```
patients.prefix
patients.first
patients.middle
patients.last
patients.suffix
```

------

## 2. CANONICAL — *clean, consistent, human-readable*

### What it is

Raw data cleaned for consistency:

- Uppercase
- Trimmed
- Punctuation removed
- Controlled vocab where applicable

### Example

```
first_canon  = "CHEN"
middle_canon = null
last_canon   = "WEI"
suffix_canon = null
```

### Purpose

- UI consistency
- Simple filtering
- Ops review
- Reporting hygiene

### Rules

- Generated on write
- Stored explicitly
- Not enough for matching by itself

### Storage

```
mpi_patient_index.first_canon
mpi_patient_index.middle_canon
mpi_patient_index.last_canon
mpi_patient_index.suffix_canon
```

------

## 3. NORMALIZED — *fast, deterministic matching*

### What it is

Canonical fields **collapsed into a single string**:

- No spaces
- No punctuation
- No prefixes
- No suffixes

### Example

```
full_name_norm = "CHENWEI"
```

### Purpose

- Primary match key
- Index-friendly
- Fast equality checks
- Registration search

### Rules

- Always indexed
- Order-sensitive
- Loses semantic structure (by design)

### Storage

```
mpi_patient_index.full_name_norm
```

------

## 4. ALTERNATE ORDER KEYS — *field-swap defense*

### What they are

Additional normalized strings where name components are rearranged.

### Example

```
Primary:  CHENWEI
Alternate: WEICHEN
```

### Purpose

- Catch first/last swaps
- Handle East Asian order
- Handle inconsistent data entry

### Rules

- Generated at write time
- Stored as a set / array
- Indexed (GIN / JSON / join table)
- Not displayed

### Storage options

**Option A – Array column**

```
alt_name_keys = ["CHENWEI", "WEICHEN"]
```

**Option B – Child table**

```
person_name_key
  person_id
  name_key
```

------

## 5. PHONETIC CODES — *fuzzy, language-aware matching*

### What they are

Algorithm-generated codes representing how names **sound**, not how they’re spelled.

### Example

```
CHEN WEI → XNWI
```

### Purpose

- Catch typos
- Catch transliteration variance
- Expand candidate pool safely

### Rules

- Low–medium weight
- Used only after narrowing
- Never decisive alone

### Locale matters

- Soundex / Metaphone → Western bias
- Chinese, Arabic, Indic names → need custom logic or lighter weighting

### Storage

```
mpi_patient_index.phonetic_code
```

------

## How they work together (registration search example)

### Input

```
first = Chen
last = Wei
dob = 1985-03-12
```

### Pipeline

1. Generate all five representations
2. Query by:
   - DOB
   - `full_name_norm` OR `alt_name_keys`
3. Score:
   - Exact normalized match (high)
   - Alternate match (medium)
   - Phonetic match (low)

### Why this layering matters

- Normalized = precision
- Alternate = recall
- Phonetic = resilience

------

## What each representation must NEVER do

| Representation | Must not           |
| -------------- | ------------------ |
| Raw            | Be altered         |
| Canonical      | Decide identity    |
| Normalized     | Be shown to users  |
| Alternate      | Be human-edited    |
| Phonetic       | Trigger auto-merge |

------

# Performance & indexing guidance

| Column         | Index             |
| -------------- | ----------------- |
| full_name_norm | B-tree            |
| alt_name_keys  | GIN / child table |
| phonetic_code  | B-tree            |
| dob            | B-tree            |

Never compute these at query time.

------

## One-sentence summary per type (use these in docs)

- **Raw**: legal truth, human-facing
- **Canonical**: clean and consistent
- **Normalized**: fast and exact
- **Alternate keys**: swap-proof
- **Phonetic**: typo-proof

------

## Final takeaway

> Identity matching works when you respect the difference between how names are written, how they are stored, and how they are compared.

