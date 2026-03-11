# MPI Reference Architecture

Below is a **reference architecture** you can drop into a design doc. I’ll give you:

1. the **conceptual diagram (what talks to what)**
2. the **data ownership boundaries (why this is safe)**
3. a **registration + update sequence overlay**

------

## 1. Reference architecture (conceptual)

### Logical view

```
┌──────────────────┐
│ Registration UI  │
│ (Front Desk)     │
└───────┬──────────┘
        │ search / select
        ▼
┌──────────────────────────────┐
│        MPI Service            │
│                              │
│  ┌──────────────┐            │
│  │  person      │◄── merge ──┤
│  ├──────────────┤            │
│  │ person_xref  │            │
│  ├──────────────┤            │
│  │ mpi_index    │            │
│  └──────────────┘            │
│                              │
│  Matching + Governance Logic  │
└─────────▲───────────┬────────┘
          │ events     │ lookup
          │            │
┌─────────┴────────────▼────────┐
│          EMR / HIS             │
│                                │
│  ┌──────────────┐              │
│  │ patients     │              │
│  │ patient_id   │              │
│  └──────────────┘              │
│                                │
│  Visits / Orders / Clinical    │
└────────────────────────────────┘
```

------

## 2. Clear ownership boundaries (this is the “why it works”)

### EMR / HIS owns

- `patient_id`
- Demographics as **legal/clinical facts**
- Visits, orders, results
- Corrections requested by the patient

### MPI owns

- `person_id`
- Matching logic
- Duplicate detection
- Merge/unmerge decisions
- Cross-record visibility

### Registration UI

- Orchestrates calls
- Shows uncertainty
- Never decides identity

> **No system owns both identity resolution and clinical truth.
> That separation is what keeps you safe.**

------

## 3. Registration-time flow (overlayed on the architecture)

### Step-by-step

```
[Registrar]
   |
   | enters name / DOB
   |
   v
[Registration UI]
   |
   | POST /mpi/registration-search
   |
   v
[MPI Service]
   |
   | query mpi_index
   | group by person
   |
   v
[Registration UI]
   |
   | shows possible duplicates
   | (grouped by person)
   |
   +--> Select existing person
   |         |
   |         v
   |     create patient in EMR
   |         |
   |         v
   |     link patient_id → person_id
   |
   +--> Create new patient
             |
             v
         create patient in EMR
             |
             v
         create new person + link
```

**Key safety points**

- EMR write always happens first
- MPI never blocks registration
- No merges occur here

------

## 4. Update / correction flow (often missed)

```
[Clinician / Registrar]
   |
   | corrects name/DOB
   |
   v
[EMR]
   |
   | UPDATE patients
   | emit PatientUpdated
   |
   v
[MPI]
   |
   | re-normalize
   | re-evaluate matches
   |
   v
[MPI Ops / Batch]
   |
   | suggest merge (if needed)
   | human review
```

MPI **reacts** to changes — it never overwrites them.

------

## 5. Why there is NO arrow back from MPI → EMR (important)

There is intentionally **no direct write-back** path.

Reasons:

- Legal record integrity
- Audit safety
- Unmerge capability
- Vendor neutrality
- Regulatory comfort

MPI can *suggest*. Humans decide.

------

## 6. Failure modes this architecture avoids

- Wrong-patient merges
- Destructive MRN rewrites
- Blocking registration
- Clinical downtime
- Irreversible identity errors

And it still allows:

- Duplicate detection
- Longitudinal view
- Cross-system reconciliation
- Analytics by real human

------

## 7. Minimal version (if leadership wants “small first”)

You can start with just:

```
MPI
 ├─ person
 ├─ person_patient_xref
 └─ mpi_index
```

No automation. No auto-merge.
Just **visibility + structure**.

That alone is a massive step up.

------

## One-line summary you can put under the diagram

> **This architecture treats patient records as immutable facts and identity as a resolvable, auditable hypothesis — enabling safe duplicate detection without clinical risk.**

