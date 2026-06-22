# JSON Path Projection

> In the projection, I want to use the JSON path of the key:value to be part of the saved projected columns. I think it has unique id value when projecting similar fields, e.g. name.

Yes — include the JSON path in every projected row. That is the right move.

But I would frame it this way:

> JSON path is excellent for **source lineage and same-label disambiguation**.
> It should not be the main clinical identity of the projected fact.

The attached schema already supports this idea: each EAV row has `source_json_path`, described as the exact JSONPath that produced the row, and the JSON document remains the source of truth for the projection.

## Recommended rule

Use all of these together:

```text
attribute_id             -- what clinical/data field this is
concept_id               -- what clinical concept it belongs to
concept_instance_id      -- which occurrence/event/group this belongs to
source_json_path         -- exact source location in the JSON document
source_json_path_hash    -- indexed/hash version of the path
source_field_key         -- optional stable UI/form field key
source_group_path        -- optional path of the repeating parent group
source_group_ordinal     -- optional sequence number within repeated group
```

## Why JSON path helps

For similar field names like `name`, JSON path disambiguates them:

```json
{
  "patient": {
    "name": "Juan Dela Cruz"
  },
  "informant": {
    "name": "Maria Santos"
  },
  "attending_physician": {
    "name": "Dr. Reyes"
  }
}
```

Projected rows:

| attribute_code           | value          | source_json_path             |
| ------------------------ | -------------- | ---------------------------- |
| PATIENT_NAME             | Juan Dela Cruz | `$.patient.name`             |
| INFORMANT_NAME           | Maria Santos   | `$.informant.name`           |
| ATTENDING_PHYSICIAN_NAME | Dr. Reyes      | `$.attending_physician.name` |

So yes, the path helps distinguish identical keys.

## But JSON path alone is not enough

It becomes risky with repeated structures:

```json
{
  "family_history": [
    {
      "name": "Father",
      "condition": "Hypertension"
    },
    {
      "name": "Mother",
      "condition": "Diabetes"
    }
  ]
}
```

You may get:

```text
$.family_history[0].name
$.family_history[1].name
```

That is unique inside that saved JSON document, but it is not a stable clinical identity. If the UI reorders rows before saving, the index can change. For an immutable submitted document, the path is stable enough for lineage. For cross-document analytics, it is not enough.

That is why the better identity is:

```text
document_id
+ concept_instance_id
+ attribute_id
+ source_json_path
```

For repeated groups, even better:

```text
document_id
+ concept_id
+ source_group_path
+ source_group_ordinal
+ attribute_id
```

or, if your form engine can generate it:

```text
document_id
+ source_node_instance_id
+ attribute_id
```

## MySQL-friendly column pattern

Add this common source block to every `obs_*` table:

```sql
source_json_path          VARCHAR(1000) NOT NULL,
source_json_path_hash     BINARY(32) NOT NULL,
source_field_key          VARCHAR(150) NULL,
source_node_instance_id   VARCHAR(100) NULL,
source_group_path         VARCHAR(1000) NULL,
source_group_ordinal      INT NULL,
```

Example in `obs_text`:

```sql
CREATE TABLE obs_text (
    obs_id                  BINARY(16) PRIMARY KEY,
    document_id             BINARY(16) NOT NULL,
    concept_instance_id     BINARY(16) NOT NULL,
    concept_id              BINARY(16) NOT NULL,
    attribute_id            BINARY(16) NOT NULL,

    patient_id              BINARY(16) NOT NULL,
    encounter_id            BINARY(16) NOT NULL,

    text_value              TEXT NOT NULL,

    observation_datetime_utc DATETIME(6) NOT NULL,
    recorded_at_utc          DATETIME(6) NOT NULL,
    valid_from_utc           DATETIME(6) NOT NULL,
    valid_to_utc             DATETIME(6) NULL,
    row_status               VARCHAR(20) NOT NULL DEFAULT 'ACTIVE',

    source_json_path         VARCHAR(1000) NOT NULL,
    source_json_path_hash    BINARY(32) NOT NULL,
    source_field_key         VARCHAR(150) NULL,
    source_node_instance_id  VARCHAR(100) NULL,
    source_group_path        VARCHAR(1000) NULL,
    source_group_ordinal     INT NULL,

    created_at_utc           DATETIME(6) NOT NULL,

    INDEX idx_obs_text_patient_attr_time
        (patient_id, attribute_id, observation_datetime_utc),

    INDEX idx_obs_text_concept_instance
        (concept_instance_id),

    INDEX idx_obs_text_source_path_hash
        (document_id, source_json_path_hash)
);
```

The hash is useful because long JSON paths are awkward to index directly.

Your projection service can compute:

```text
source_json_path_hash = SHA2(source_json_path, 256)
```

In MySQL:

```sql
UNHEX(SHA2(source_json_path, 256))
```

## Attribute catalog should still keep the design-time path

Keep both:

```text
attribute_catalog.json_path
obs_*.source_json_path
```

They are related but not identical.

| Column                        | Meaning                                       |
| ----------------------------- | --------------------------------------------- |
| `attribute_catalog.json_path` | Expected path from the form/schema definition |
| `obs_*.source_json_path`      | Actual path that produced this projected row  |

That distinction matters for repeated groups, versioned forms, and debugging projection errors. The schema already treats `attribute_catalog` as the registry of fields that may be projected and includes `json_path` there.

## Best practice

I would use this identity model:

```text
obs_id = row identity
attribute_id = field identity
concept_id = clinical concept identity
concept_instance_id = clinical event/group identity
source_json_path = source lineage
source_node_instance_id = UI/form node identity, if available
```

So for your example, `name` may appear many times, but each one is uniquely understood through:

```text
attribute_code: INFORMANT_NAME
source_json_path: $.informant.name
concept_instance_id: informant section instance
```

or:

```text
attribute_code: FAMILY_MEMBER_NAME
source_json_path: $.family_history[1].name
concept_instance_id: family history member instance #2
```

## Final recommendation

Yes, save the JSON path. I would make it mandatory.

But do not rely on it alone as the unique projected identifier. Use it as **provenance and disambiguation**, while `attribute_id + concept_instance_id + document_id` remains the safer clinical/data identity.

The most robust projected row identity is:

```text
document_id
+ concept_instance_id
+ attribute_id
+ source_json_path_hash
```

That gives you traceability back to JSON, handles repeated concepts, and protects you from ambiguity when many fields share generic names like `name`, `date`, `remarks`, or `status`.