# MPI MySQL Script and Merge/UnMerge

Below is **one complete MySQL SQL file** you can hand to DBAs.
After that, I’ll **walk you through merge and unmerge step by step**, using concrete examples and explaining *why each step exists*.

------

## PART 1 — FULL SQL SCRIPT (MySQL 8+)

```sql
-- ============================================
-- MASTER PATIENT INDEX (MPI) SCHEMA
-- MySQL 8+
-- ============================================

-- ----------------------------
-- PERSON (identity anchor)
-- ----------------------------
CREATE TABLE person (
  person_id   CHAR(36) PRIMARY KEY,
  status      ENUM('active','retired') NOT NULL DEFAULT 'active',
  created_at  TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP
) ENGINE=InnoDB;

-- ----------------------------
-- PERSON ↔ PATIENT CROSS-REFERENCE
-- ----------------------------
CREATE TABLE person_patient_xref (
  person_id     CHAR(36) NOT NULL,
  patient_id    VARCHAR(32) NOT NULL,
  source_system VARCHAR(20) NOT NULL,
  link_reason   VARCHAR(50),
  status        ENUM('active','superseded') NOT NULL DEFAULT 'active',
  created_at    TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,

  PRIMARY KEY (person_id, patient_id),

  INDEX idx_xref_patient (patient_id),
  INDEX idx_xref_person (person_id),

  CONSTRAINT fk_xref_person
    FOREIGN KEY (person_id)
    REFERENCES person(person_id)
) ENGINE=InnoDB;

-- ----------------------------
-- MPI PATIENT INDEX (search-only)
-- ----------------------------
CREATE TABLE mpi_patient_index (
  patient_id          VARCHAR(32) PRIMARY KEY,

  -- canonical (clean, readable)
  first_canon         VARCHAR(100),
  middle_canon        VARCHAR(100),
  last_canon          VARCHAR(100),
  suffix_canon        VARCHAR(20),

  -- normalized
  full_name_norm      VARCHAR(300),

  -- alternate order keys (stored as JSON array)
  alt_name_keys       JSON,

  -- phonetic
  phonetic_code       VARCHAR(50),

  dob                 DATE,
  phone_normalized    VARCHAR(30),

  updated_at          TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP
                              ON UPDATE CURRENT_TIMESTAMP
) ENGINE=InnoDB;

-- ----------------------------
-- INDEXES FOR SEARCH PERFORMANCE
-- ----------------------------
CREATE INDEX idx_mpi_dob
  ON mpi_patient_index (dob);

CREATE INDEX idx_mpi_full_name_norm
  ON mpi_patient_index (full_name_norm);

CREATE INDEX idx_mpi_phonetic
  ON mpi_patient_index (phonetic_code);

CREATE INDEX idx_mpi_phone
  ON mpi_patient_index (phone_normalized);

-- JSON index for alternate name keys
CREATE INDEX idx_mpi_alt_name_keys
  ON mpi_patient_index ((CAST(alt_name_keys AS CHAR(300))));

-- ============================================
-- END OF SCHEMA
-- ============================================
```

### Design notes (important)

- `mpi_patient_index` **does not reference person_id**
- `person_patient_xref` is the **only identity assertion**
- JSON is used for alternate keys because MySQL lacks native array types
- All tables are **InnoDB** for transactional safety

------

## PART 2 — MERGE WALKTHROUGH (THE SAFE WAY)

### Scenario

You discover these patient records belong to the same human:

```
P001
P145
P388
```

Current state:

```
person_A → P001
person_B → P145
person_C → P388
```

------

### Step 1 — Pick the survivor person

This is a **human decision**, not SQL logic.

Example:

```
Survivor = person_A
Retired  = person_B, person_C
```

------

### Step 2 — Re-point cross-references (THE MERGE)

```sql
UPDATE person_patient_xref
SET person_id = 'person_A'
WHERE person_id IN ('person_B', 'person_C');
```

What just happened:

- No patient rows touched
- No index rows touched
- No clinical data touched

You have **logically merged identity**.

------

### Step 3 — Retire losing persons

```sql
UPDATE person
SET status = 'retired'
WHERE person_id IN ('person_B', 'person_C');
```

Why retire instead of delete:

- Audit trail
- Rollback safety
- Legal defensibility

------

### Step 4 — Resulting state

```
person_A → P001, P145, P388
person_B → (retired)
person_C → (retired)
```

That’s it.
**This is why the model is safe.**

------

## PART 3 — UNMERGE WALKTHROUGH (THIS WILL HAPPEN)

## Scenario

You discover:

- P145 is actually the patient’s son
- Merge was wrong

You must undo **without data loss**.

------

### Step 1 — Create a new person (reactivation)

```sql
INSERT INTO person (person_id, status)
VALUES ('person_D', 'active');
```

------

### Step 2 — Move patient record back

```sql
UPDATE person_patient_xref
SET person_id = 'person_D'
WHERE patient_id = 'P145';
```

Only **one row** moves.

------

### Step 3 — Optional: document correction

You typically also:

- log an audit event
- mark merge decision as reversed

No DB rewrite needed.

------

### Step 4 — Final state

```
person_A → P001, P388
person_D → P145
```

No deletes.
No reprocessing.
No clinical downtime.

------

## PART 4 — WHY THIS WORKS (AND OTHER DESIGNS DON’T)

### What was NOT touched

- `patients` table
- clinical encounters
- orders
- lab results
- billing records

### What moved

- **only identity assertions**

This is the **minimum blast radius** possible.

------

## PART 5 — OPERATIONAL GUARDRAILS (DO NOT SKIP)

1. **Never delete persons**
2. **Never merge patient_ids**
3. **Always merge via xref**
4. **Always allow unmerge**
5. **Always log merge decisions**

If you violate any of these, you will eventually cause patient harm.

------

## Final line to remember

> **Merging people is reversible.
> Merging records is not.**

