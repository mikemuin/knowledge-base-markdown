# MPI Status ENUMS

Good call. Getting enums right early saves years of pain.
Below is a **practical, conservative enum set** — not academic, not over-engineered. This is what I’d put in front of ops, compliance, and DBAs.

I’ll group by table and explain **why each enum exists** and **what it should \*not\* be used for**.

------

## 1. `person.status`

**What is the lifecycle of a resolved human identity**

#### Proposed ENUM

```sql
ENUM(
  'active',
  'retired',
  'merged',
  'error'
)
```

#### Meaning

| Value     | Meaning                               |
| --------- | ------------------------------------- |
| `active`  | Canonical, usable person              |
| `retired` | No longer canonical (lost merge)      |
| `merged`  | Explicitly merged into another person |
| `error`   | Created in error, never valid         |

#### Notes

- `merged` vs `retired` helps audits (“why is this inactive?”)
- **Never delete persons**
- `error` is rare but useful for accidental creations

------

## 2. `person_patient_xref.status`

**What is the state of the linkage, not the person**

#### Proposed ENUM

```sql
ENUM(
  'active',
  'superseded',
  'revoked'
)
```

#### Meaning

| Value        | Meaning                             |
| ------------ | ----------------------------------- |
| `active`     | Valid link                          |
| `superseded` | Replaced by another link (merge)    |
| `revoked`    | Explicitly undone (unmerge / error) |

#### Notes

- This lets you **preserve link history**
- Do NOT overload `status` to mean person lifecycle
- `revoked` is critical for legal defensibility

------

## 3. `person_patient_xref.link_reason`

**Why this link exists (very important for trust)**

#### Proposed ENUM

```sql
ENUM(
  'registration_new',
  'registration_selected_existing',
  'mpi_auto_link',
  'mpi_manual_merge',
  'mpi_manual_unmerge',
  'system_import',
  'correction'
)
```

#### Meaning

| Value                            | When used                       |
| -------------------------------- | ------------------------------- |
| `registration_new`               | New person + patient            |
| `registration_selected_existing` | Registrar chose existing person |
| `mpi_auto_link`                  | Deterministic auto-link (rare)  |
| `mpi_manual_merge`               | Human-approved merge            |
| `mpi_manual_unmerge`             | Human-approved unmerge          |
| `system_import`                  | Backfill / migration            |
| `correction`                     | Post-registration fix           |

#### Notes

- This field is gold during investigations
- Never free-text this

------

## 4. `mpi_patient_index` (implicit statuses)

**Recommendation: do NOT add a status enum here**

Why:

- This table is **derived**
- Its lifecycle follows `patients`
- Status belongs upstream

If you must add one (edge cases):

```sql
ENUM(
  'current',
  'stale'
)
```

Used only to:

- Mark lagging sync
- Debug ingestion issues

------

## 5. Merge decision tracking (separate table – strongly recommended)

If you do merges seriously, add this table.

#### `person_merge_event.status`

```sql
ENUM(
  'proposed',
  'approved',
  'executed',
  'reversed',
  'rejected'
)
```

#### Meaning

| Value      | Meaning             |
| ---------- | ------------------- |
| `proposed` | Algorithm suggested |
| `approved` | Human approved      |
| `executed` | Merge applied       |
| `reversed` | Unmerge happened    |
| `rejected` | Human said no       |

This keeps merge logic **out of core tables**.

------

## 6. Registration override tracking (optional but realistic)

#### `registration_override.reason`

```sql
ENUM(
  'name_common',
  'insufficient_data',
  'patient_denied_match',
  'time_pressure',
  'system_latency',
  'other'
)
```

Why this matters:

- Tells you **why duplicates still occur**
- Feeds algorithm tuning
- Protects staff (“they followed process”)

------

## 7. What enums you should NOT add (yet)

Avoid these early:

`confidence_level` as ENUM
`match_strength` as ENUM
`data_quality` as ENUM

Use **numeric scores + rules**, not enums, for matching.

Enums are for **state**, not **math**.

------

## 8. Minimal enum set (if you want to start smaller)

If leadership wants “lean”:

**Required**

- `person.status`
- `person_patient_xref.status`
- `person_patient_xref.link_reason`

Everything else can wait.

------

## 9. One-page summary (exec-friendly)

> - `person.status` = lifecycle of identity
> - `xref.status` = lifecycle of linkage
> - `link_reason` = why we trust this link
> - Merge state lives outside core tables
> - Derived data does not get business status

------

## Why not `inactive`?

Great question. This gets right to the heart of why identity systems rot over time.

Short answer: **`inactive` is too vague to be safe**. It hides *why* something is no longer usable, and that ambiguity breaks audits, unmerge, and trust.

Below is the precise, practical reasoning.

------

### The core problem with `inactive`

`inactive` answers **only one question**:

> “Can I use this?”

It does **not** answer the questions you *will* be asked later:

- *Why* is it inactive?
- *What happened* to it?
- *Can it be reactivated?*
- *Is this an error, a merge, or a business decision?*
- *Who decided and when?*

In healthcare, those questions are **not optional**.

------

### Concrete failure scenario

You see this six months later:

```text
person_id = 123
status = inactive
```

Now answer:

- Was this person merged?
- Was it created by mistake?
- Was it revoked for legal reasons?
- Was it temporarily disabled?
- Can I unmerge?

You can’t. The system has lost intent.

------

### Why this is especially dangerous in MPI

#### 1. Merges and unmerges become ambiguous

If you merge person B into person A and mark B as `inactive`:

- How do you distinguish:
  - merged-away vs
  - created-in-error vs
  - legal suppression?

You can’t safely unmerge without knowing.

------

#### 2. Auditors will reject it

Auditors expect **semantic state**, not UI state.

“Inactive” is a UI word.

They will ask:

> “Inactive *because of what*?”

If you can’t answer with a field, you’ll end up with:

- ad-hoc notes
- tribal knowledge
- undocumented scripts

------

#### 3. Ops teams misuse it

Given `inactive`, teams will:

- reuse it
- flip it back and forth
- overload it with meanings

Within a year:

- nobody trusts the data
- nobody knows what inactive really means

------

### Why specific states are better

Compare:

#### Vague

```sql
status = 'inactive'
```

#### Explicit

```sql
status = 'merged'
status = 'retired'
status = 'error'
```

Now:

- merge logic knows exactly what happened
- unmerge is deterministic
- reporting is accurate
- governance is enforceable

------

### Table-by-table reasoning

### `person.status`

Why **not** `inactive`:

- A person is either:
  - canonical
  - merged away
  - created in error
  - legally suppressed

Each case has different rules.

**Better**

```text
active | merged | retired | error
```

------

### `person_patient_xref.status`

Why **not** `inactive`:

Links fail for different reasons:

- replaced by another link
- explicitly revoked
- temporarily disabled

**Better**

```text
active | superseded | revoked
```

------

### When `inactive` *is* acceptable

There *are* places where `inactive` is fine.

### Acceptable use cases

- UI feature flags
- Reference/master data
- Configuration tables
- Non-audited business entities

### Why it’s OK there

- No unmerge
- No legal impact
- No patient safety risk

Identity is not one of those places.

------

### A compromise (if your org insists)

If leadership insists on `inactive`, **force a reason code**.

Example:

```sql
status = 'inactive'
inactive_reason = 'merged'
```

But be clear:

- This is a second-best solution
- Two fields must always move together
- Enforcing consistency is harder than enums

Enums encode intent better.

------

### The guiding rule (remember this)

> **If a state can be reversed, it must explain \*why\* it changed.**

`inactive` explains *nothing*.

------

### Bottom line

You don’t use `inactive` because:

- It collapses distinct lifecycle states
- It destroys auditability
- It makes the unmerge unsafe
- It invites misuse

Identity systems fail quietly, not loudly.
Vague states are how that happens.

