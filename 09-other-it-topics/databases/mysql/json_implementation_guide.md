# MySQL JSON Mastery: Implementation Guide

## Table of Contents
1. [Getting Started](#getting-started)
2. [Quick Reference Card](#quick-reference)
3. [Common Patterns](#common-patterns)
4. [Performance Benchmarks](#performance)
5. [Migration Strategies](#migration)
6. [Troubleshooting](#troubleshooting)

---

## Getting Started

### Prerequisites
```sql
-- Check MySQL version (8.0.13+ recommended for full JSON support)
SELECT VERSION();

-- Enable optimizer trace for JSON query analysis
SET optimizer_trace='enabled=on';
```

### Load Your Data
```bash
# Import the 300-documents.sql file
mysql -u your_user -p your_database < /path/to/300-documents.sql

# Verify import
mysql -u your_user -p -e "SELECT COUNT(*) FROM documents;"
```

---

## Quick Reference Card

### JSON Extraction Operators
```sql
-- Extract with JSON type (returns JSON)
SELECT values->'$.age' FROM documents;

-- Extract as unquoted string (returns VARCHAR)
SELECT values->>'$.age' FROM documents;

-- Traditional function syntax
SELECT JSON_EXTRACT(values, '$.age') FROM documents;
SELECT JSON_UNQUOTE(JSON_EXTRACT(values, '$.age')) FROM documents;
```

### JSON Predicates
| Function | Purpose | Example |
|----------|---------|---------|
| `JSON_CONTAINS()` | Check if value exists | `JSON_CONTAINS(values, '"Yes"', '$.is_pregnant')` |
| `JSON_SEARCH()` | Find path to value | `JSON_SEARCH(values, 'one', '%diabetes%')` |
| `JSON_VALID()` | Validate JSON | `WHERE JSON_VALID(values) = 1` |
| `JSON_LENGTH()` | Array/object size | `JSON_LENGTH(values->'$.prescriptions')` |

### JSON Modification
| Function | Behavior | Use Case |
|----------|----------|----------|
| `JSON_SET()` | Update or insert | Default modification |
| `JSON_INSERT()` | Insert only if missing | Preserve existing data |
| `JSON_REPLACE()` | Update only if exists | Safe updates |
| `JSON_REMOVE()` | Delete paths | Cleanup |
| `JSON_ARRAY_APPEND()` | Add to array end | Logs, histories |
| `JSON_ARRAY_INSERT()` | Insert at position | Priority items |

### JSON Construction
```sql
-- Build object from columns
SELECT JSON_OBJECT(
    'id', id,
    'name', patient_name,
    'age', values->>'$.age'
) FROM documents;

-- Aggregate into array
SELECT JSON_ARRAYAGG(patient_name)
FROM documents
GROUP BY site_code;

-- Key-value aggregation
SELECT JSON_OBJECTAGG(patient_id, values->>'$.bmi')
FROM documents
GROUP BY site_code;
```

---

## Common Patterns

### Pattern 1: Extract Multiple Fields Efficiently
**Bad - Repeated JSON Parsing:**
```sql
-- Anti-pattern: Parser hits JSON 3 times
SELECT
    id,
    CAST(values->>'$.age' AS UNSIGNED) AS age,
    values->>'$.sex' AS gender,
    CAST(values->>'$.bmi' AS DECIMAL(5,2)) AS bmi
FROM documents
WHERE CAST(values->>'$.age' AS UNSIGNED) > 50  -- 4th parse
    AND CAST(values->>'$.bmi' AS DECIMAL(5,2)) > 25;  -- 5th parse
```

**Good - CTE with Single Parse:**
```sql
-- Pattern: Parse once, reference multiple times
WITH parsed_data AS (
    SELECT
        id,
        CAST(values->>'$.age' AS UNSIGNED) AS age,
        values->>'$.sex' AS gender,
        CAST(values->>'$.bmi' AS DECIMAL(5,2)) AS bmi
    FROM documents
    WHERE values IS NOT NULL
)
SELECT *
FROM parsed_data
WHERE age > 50 AND bmi > 25;
```

**Best - Generated Columns:**
```sql
-- One-time setup, permanent speedup
ALTER TABLE documents
ADD COLUMN age_computed INT AS (CAST(values->>'$.age' AS UNSIGNED)) STORED,
ADD COLUMN bmi_computed DECIMAL(5,2) AS (CAST(values->>'$.bmi' AS DECIMAL(5,2))) STORED;

CREATE INDEX idx_age_bmi ON documents(age_computed, bmi_computed);

-- Lightning fast queries
SELECT id, age_computed, bmi_computed
FROM documents
WHERE age_computed > 50 AND bmi_computed > 25;
```

### Pattern 2: Array Processing with JSON_TABLE
**Scenario:** Extract all prescriptions from all documents

**Bad - Application-side loop:**
```python
# Don't do this - pulls all JSON to application
results = db.execute("SELECT id, values FROM documents")
for row in results:
    prescriptions = json.loads(row['values'])['e_prescription_section_array']
    for rx in prescriptions:
        process(rx)
```

**Good - JSON_TABLE in SQL:**
```sql
-- Single query, server-side processing
SELECT
    d.id,
    d.patient_name,
    d.visit_id,
    rx.generic_name,
    rx.quantity,
    rx.dosage
FROM documents d
CROSS JOIN JSON_TABLE(
    d.values,
    '$.e_prescription_section_array[*]'
    COLUMNS(
        generic_name VARCHAR(255) PATH '$.e_prescription_section_generic_name',
        quantity VARCHAR(50) PATH '$.e_prescription_section_quantity',
        dosage VARCHAR(100) PATH '$.e_prescription_section_strength_dosage_form'
    )
) AS rx
WHERE rx.generic_name IS NOT NULL;
```

### Pattern 3: Conditional JSON Updates
**Scenario:** Add calculated fields only for valid records

```sql
-- Update with validation
UPDATE documents
SET values = JSON_SET(
    values,
    '$.risk_score',
    (
        CASE WHEN CAST(values->>'$.age' AS UNSIGNED) > 60 THEN 2 ELSE 0 END +
        CASE WHEN CAST(values->>'$.bmi' AS DECIMAL(5,2)) > 30 THEN 2 ELSE 0 END
    )
)
WHERE values IS NOT NULL
    AND JSON_VALID(values) = 1
    AND values->>'$.age' REGEXP '^[0-9]+$'  -- Validate numeric
    AND values->>'$.bmi' REGEXP '^[0-9.]+$';
```

### Pattern 4: Hierarchical JSON Aggregation
**Scenario:** Build nested summary by site and age group

```sql
SELECT
    site_code,
    JSON_OBJECT(
        'site_code', site_code,
        'age_groups', JSON_OBJECTAGG(
            age_group,
            JSON_OBJECT(
                'count', patient_count,
                'avg_bmi', avg_bmi
            )
        )
    ) AS site_summary
FROM (
    SELECT
        site_code,
        CASE
            WHEN CAST(values->>'$.age' AS UNSIGNED) < 30 THEN 'young'
            WHEN CAST(values->>'$.age' AS UNSIGNED) < 60 THEN 'middle'
            ELSE 'senior'
        END AS age_group,
        COUNT(*) AS patient_count,
        ROUND(AVG(CAST(values->>'$.bmi' AS DECIMAL(5,2))), 2) AS avg_bmi
    FROM documents
    WHERE values->>'$.age' IS NOT NULL
    GROUP BY site_code, age_group
) AS grouped
GROUP BY site_code;
```

---

## Performance Benchmarks

### Baseline: No Optimization
```sql
-- Test query without indexes
EXPLAIN ANALYZE
SELECT COUNT(*)
FROM documents
WHERE CAST(values->>'$.age' AS UNSIGNED) > 50
    AND values->>'$.sex' = 'F';
-- Expected: ~100-500ms for 1000 rows (full table scan)
```

### Optimization Level 1: Functional Index
```sql
CREATE INDEX idx_age_func ON documents(
    (CAST(values->>'$.age' AS UNSIGNED))
);

CREATE INDEX idx_sex_func ON documents(
    (JSON_UNQUOTE(values->'$.sex'))
);

-- Retest
EXPLAIN ANALYZE
SELECT COUNT(*)
FROM documents
WHERE CAST(values->>'$.age' AS UNSIGNED) > 50
    AND values->>'$.sex' = 'F';
-- Expected: ~10-50ms (index range scan)
-- Speedup: 10-50x
```

### Optimization Level 2: Generated Columns
```sql
ALTER TABLE documents
ADD COLUMN age_gen INT AS (CAST(values->>'$.age' AS UNSIGNED)) STORED,
ADD COLUMN sex_gen VARCHAR(10) AS (JSON_UNQUOTE(values->'$.sex')) STORED;

CREATE INDEX idx_age_sex_gen ON documents(age_gen, sex_gen);

-- Retest
EXPLAIN ANALYZE
SELECT COUNT(*)
FROM documents
WHERE age_gen > 50 AND sex_gen = 'F';
-- Expected: ~1-5ms (index scan, no JSON parsing)
-- Speedup: 100-500x from baseline
```

### JSON_TABLE Performance
```sql
-- Slow: Correlated subquery per row
SELECT
    d.id,
    (SELECT generic_name
     FROM JSON_TABLE(d.values, '$.e_prescription_section_array[0]'
     COLUMNS(generic_name VARCHAR(255) PATH '$.e_prescription_section_generic_name')) AS t
    ) AS first_med
FROM documents d;

-- Fast: Single pass with CROSS JOIN
SELECT d.id, t.generic_name
FROM documents d
CROSS JOIN JSON_TABLE(
    d.values,
    '$.e_prescription_section_array[0]'
    COLUMNS(generic_name VARCHAR(255) PATH '$.e_prescription_section_generic_name')
) AS t
WHERE t.generic_name IS NOT NULL;
-- Speedup: 5-10x
```

---

## Migration Strategies

### Strategy 1: Phased Generated Column Rollout
```sql
-- Step 1: Add columns without populating (instant)
ALTER TABLE documents
ADD COLUMN age_cached INT AS (CAST(values->>'$.age' AS UNSIGNED)) VIRTUAL;

-- Step 2: Test queries with new column
SELECT age_cached FROM documents WHERE age_cached > 50 LIMIT 10;

-- Step 3: Convert to STORED for production (requires table rewrite)
ALTER TABLE documents
MODIFY COLUMN age_cached INT AS (CAST(values->>'$.age' AS UNSIGNED)) STORED;

-- Step 4: Add index
CREATE INDEX idx_age_cached ON documents(age_cached);
```

### Strategy 2: Parallel Table for JSON Denormalization
```sql
-- Create parallel structure
CREATE TABLE documents_search (
    document_id BIGINT PRIMARY KEY,
    patient_age INT,
    patient_sex VARCHAR(10),
    patient_bmi DECIMAL(5,2),
    icd10_code VARCHAR(50),
    diagnosis_text TEXT,
    INDEX idx_age_sex (patient_age, patient_sex),
    INDEX idx_bmi (patient_bmi),
    INDEX idx_icd10 (icd10_code),
    FULLTEXT INDEX ft_diagnosis (diagnosis_text)
) ENGINE=InnoDB;

-- Populate from documents table
INSERT INTO documents_search
SELECT
    id,
    CAST(values->>'$.age' AS UNSIGNED),
    values->>'$.sex',
    CAST(values->>'$.bmi' AS DECIMAL(5,2)),
    values->>'$.op_soap_notes_icd_10_code_primary',
    values->>'$.op_soap_notes_diagnosis_text'
FROM documents
WHERE values IS NOT NULL;

-- Keep synchronized with trigger
DELIMITER //
CREATE TRIGGER sync_documents_search
AFTER INSERT ON documents
FOR EACH ROW
BEGIN
    IF NEW.values IS NOT NULL THEN
        INSERT INTO documents_search VALUES (
            NEW.id,
            CAST(NEW.values->>'$.age' AS UNSIGNED),
            NEW.values->>'$.sex',
            CAST(NEW.values->>'$.bmi' AS DECIMAL(5,2)),
            NEW.values->>'$.op_soap_notes_icd_10_code_primary',
            NEW.values->>'$.op_soap_notes_diagnosis_text'
        );
    END IF;
END//
DELIMITER ;

-- Use search table for analytics, documents table for detail retrieval
SELECT d.id, d.patient_name, d.values
FROM documents_search s
JOIN documents d ON d.id = s.document_id
WHERE s.patient_age > 50
    AND s.patient_bmi > 30
    AND MATCH(s.diagnosis_text) AGAINST('hypertension');
```

### Strategy 3: Incremental JSON Path Extraction
```sql
-- Create staging table for new extractions
CREATE TABLE json_extraction_queue (
    document_id BIGINT,
    extraction_status ENUM('pending', 'completed', 'failed'),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    PRIMARY KEY (document_id)
);

-- Populate queue
INSERT IGNORE INTO json_extraction_queue (document_id, extraction_status)
SELECT id, 'pending' FROM documents;

-- Process in batches
UPDATE documents d
JOIN json_extraction_queue q ON q.document_id = d.id
SET d.values = JSON_SET(
        d.values,
        '$.extracted_metrics',
        JSON_OBJECT(
            'age', CAST(d.values->>'$.age' AS UNSIGNED),
            'bmi_category', CASE
                WHEN CAST(d.values->>'$.bmi' AS DECIMAL(5,2)) < 18.5 THEN 'underweight'
                WHEN CAST(d.values->>'$.bmi' AS DECIMAL(5,2)) < 25 THEN 'normal'
                WHEN CAST(d.values->>'$.bmi' AS DECIMAL(5,2)) < 30 THEN 'overweight'
                ELSE 'obese'
            END
        )
    ),
    q.extraction_status = 'completed'
WHERE q.extraction_status = 'pending'
LIMIT 1000;  -- Process 1000 at a time
```

---

## Troubleshooting

### Issue 1: Slow JSON Extraction Queries
**Symptoms:** Query takes >1s, EXPLAIN shows "Using where" with no index

**Diagnosis:**
```sql
EXPLAIN
SELECT * FROM documents
WHERE CAST(values->>'$.age' AS UNSIGNED) > 50;
```

**Solution:**
```sql
-- Option A: Functional index
CREATE INDEX idx_json_age ON documents(
    (CAST(values->>'$.age' AS UNSIGNED))
);

-- Option B: Generated column
ALTER TABLE documents
ADD COLUMN age_indexed INT AS (CAST(values->>'$.age' AS UNSIGNED)) VIRTUAL;
CREATE INDEX idx_age ON documents(age_indexed);
```

### Issue 2: JSON_TABLE Returns Empty Results
**Symptoms:** Query with JSON_TABLE returns 0 rows, but JSON exists

**Common Causes:**
1. Wrong JSON path
2. NULL handling
3. Implicit filtering

**Diagnosis:**
```sql
-- Check JSON structure
SELECT values->'$.e_prescription_section_array'
FROM documents
WHERE id = 1829248;

-- Test JSON_TABLE in isolation
SELECT *
FROM JSON_TABLE(
    '{"e_prescription_section_array":[{"generic_name":"Amoxicillin"}]}',
    '$.e_prescription_section_array[*]'
    COLUMNS(generic_name VARCHAR(255) PATH '$.e_prescription_section_generic_name')
) AS t;
```

**Solution:**
```sql
-- Use LEFT JOIN instead of CROSS JOIN for optional arrays
SELECT d.id, COALESCE(t.generic_name, 'No prescription') AS medication
FROM documents d
LEFT JOIN JSON_TABLE(
    d.values,
    '$.e_prescription_section_array[*]'
    COLUMNS(generic_name VARCHAR(255) PATH '$.e_prescription_section_generic_name')
) AS t ON TRUE;
```

### Issue 3: JSON Update Not Persisting
**Symptoms:** JSON_SET appears to work but value doesn't change

**Common Causes:**
1. WHERE clause filters out target rows
2. Transaction not committed
3. Validation constraint blocking update

**Diagnosis:**
```sql
-- Check current value
SELECT id, values->>'$.risk_score' FROM documents WHERE id = 1829248;

-- Test update with explicit output
SELECT
    id,
    values->>'$.risk_score' AS old_value,
    JSON_SET(values, '$.risk_score', 10)->>'$.risk_score' AS new_value
FROM documents
WHERE id = 1829248;
```

**Solution:**
```sql
-- Ensure WHERE clause matches
UPDATE documents
SET values = JSON_SET(values, '$.risk_score', 10)
WHERE id = 1829248
    AND JSON_VALID(values) = 1;  -- Add explicit validation check

-- Verify
SELECT id, values->>'$.risk_score' FROM documents WHERE id = 1829248;
```

### Issue 4: CHECK Constraint Violation on JSON
**Symptoms:** "ERROR 3819: Check constraint is violated"

**Diagnosis:**
```sql
-- Find failing constraints
SHOW CREATE TABLE documents\G

-- Test constraint manually
SELECT
    id,
    values->>'$.age',
    CAST(values->>'$.age' AS UNSIGNED) AS age_value,
    (CAST(values->>'$.age' AS UNSIGNED) >= 0 AND
     CAST(values->>'$.age' AS UNSIGNED) <= 150) AS valid_age
FROM documents
WHERE values->>'$.age' IS NOT NULL;
```

**Solution:**
```sql
-- Option A: Fix data before enabling constraint
UPDATE documents
SET values = JSON_REMOVE(values, '$.age')
WHERE CAST(values->>'$.age' AS UNSIGNED) NOT BETWEEN 0 AND 150;

-- Option B: Add constraint with validation
ALTER TABLE documents
ADD CONSTRAINT chk_age_valid
CHECK (
    values IS NULL
    OR values->>'$.age' IS NULL
    OR values->>'$.age' REGEXP '^[0-9]+$'  -- Must be numeric
    AND CAST(values->>'$.age' AS UNSIGNED) BETWEEN 0 AND 150
);
```

### Issue 5: Memory Issues with Large JSON
**Symptoms:** Query killed, "Out of memory" error

**Diagnosis:**
```sql
-- Check JSON size distribution
SELECT
    id,
    LENGTH(values) AS json_size_bytes,
    LENGTH(values) / 1024 AS json_size_kb
FROM documents
ORDER BY json_size_bytes DESC
LIMIT 10;
```

**Solution:**
```sql
-- Option A: Process in batches
SELECT * FROM documents
WHERE id BETWEEN 1829000 AND 1829100
    AND values IS NOT NULL;

-- Option B: Extract only needed fields
SELECT
    id,
    values->'$.age' AS age,
    values->'$.sex' AS sex
FROM documents;  -- Don't retrieve entire JSON

-- Option C: Increase memory limits (temporary)
SET SESSION max_heap_table_size = 1024 * 1024 * 1024;  -- 1GB
SET SESSION tmp_table_size = 1024 * 1024 * 1024;  -- 1GB
```

---

## Advanced Tips

### Tip 1: JSON Schema Versioning
```sql
-- Add schema version to JSON
UPDATE documents
SET values = JSON_SET(values, '$.schema_version', 'v2.0')
WHERE values->>'$.schema_version' IS NULL;

-- Query by schema version
SELECT * FROM documents
WHERE values->>'$.schema_version' = 'v2.0';
```

### Tip 2: Audit Trail in JSON
```sql
-- Add audit log to existing JSON
UPDATE documents
SET values = JSON_SET(
    values,
    '$.audit_log',
    JSON_ARRAY_APPEND(
        COALESCE(values->'$.audit_log', JSON_ARRAY()),
        '$',
        JSON_OBJECT(
            'action', 'data_validation',
            'user', USER(),
            'timestamp', NOW()
        )
    )
)
WHERE id = 1829248;
```

### Tip 3: JSON Compression
```sql
-- For archival, compress JSON to save space
ALTER TABLE documents_archive
ADD COLUMN values_compressed BLOB;

-- Compress
UPDATE documents_archive
SET values_compressed = COMPRESS(values);

-- Decompress when needed
SELECT
    id,
    CAST(UNCOMPRESS(values_compressed) AS JSON) AS values
FROM documents_archive;
```

### Tip 4: JSON in Stored Procedures
```sql
DELIMITER //

CREATE PROCEDURE get_patient_summary(IN doc_id BIGINT)
BEGIN
    SELECT JSON_OBJECT(
        'patient_name', patient_name,
        'age', CAST(values->>'$.age' AS UNSIGNED),
        'gender', values->>'$.sex',
        'bmi', CAST(values->>'$.bmi' AS DECIMAL(5,2)),
        'diagnosis', values->>'$.op_soap_notes_diagnosis_text',
        'prescription_count', JSON_LENGTH(values->'$.e_prescription_section_array')
    ) AS summary
    FROM documents
    WHERE id = doc_id;
END//

DELIMITER ;

-- Usage
CALL get_patient_summary(1829248);
```

---

## Next Steps

1. **Practice Queries**: Execute queries from `mysql_json_mastery.sql` on your dataset
2. **Monitor Performance**: Use `EXPLAIN ANALYZE` to measure improvements
3. **Implement Indexes**: Start with functional indexes, migrate to generated columns
4. **Build Denormalized Views**: Create `documents_materialized` for analytics
5. **Optimize Hot Paths**: Profile most-used queries and optimize their JSON access patterns

## Resources
- MySQL JSON Functions: https://dev.mysql.com/doc/refman/8.0/en/json-functions.html
- Generated Columns: https://dev.mysql.com/doc/refman/8.0/en/create-table-generated-columns.html
- JSON Path Syntax: https://dev.mysql.com/doc/refman/8.0/en/json-path-syntax.html
