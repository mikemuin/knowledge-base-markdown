# MPI - The Concept

**MPI success \*appears\* to revolve around `mpi_patient_index`, but MPI \*does not exist\* without the `person ↔ xref` layer.**

Commercial MPIs work *because* of the **separation**, not because of the index alone.

Let me drive this home cleanly.

------

## Why it *feels* like everything boils down to `mpi_patient_index`

Because **this table does the visible work**:

- Fast search at registration
- Candidate narrowing
- Duplicate detection
- Scoring inputs
- Performance tuning
- Most of the “algorithm magic”

If this table is bad:

- Matching is slow
- Duplicates explode
- Users lose trust
- MPI looks useless

So instinctively, yes — **this is where MPI lives day to day**.

You’re not wrong to feel that.

------

## But here’s the critical correction

> **`mpi_patient_index` is a \*search engine\*, not an MPI.**

It answers only one question:

> “Which *records* look similar?”

It **cannot** answer:

- Which records belong to the same human
- Which identity is canonical
- How to merge or unmerge safely
- What happened historically
- What is legally defensible

That’s where most academic explanations stop — and why they feel disconnected from reality.

------

## What commercial MPIs *actually* look like (under the hood)

All serious MPIs — Cerner, Epic, IBM Initiate, NextGate, InterSystems, OpenEMPI — converge on the **same internal pattern**, regardless of marketing:

### Layer 1 — Search / Index (your `mpi_patient_index`)

- Denormalized
- Redundant
- Tuned for speed
- Frequently rebuilt
- Often disposable

This is the *engine*, not the authority.

------

### Layer 2 — Identity assertion (your `person_patient_xref`)

- Explicit linking
- Human-reviewed decisions
- Merge / unmerge history
- Audit trail
- Slow-changing

This is where **truth** lives.

------

### Layer 3 — Canonical identity (your `person`)

- Minimal
- Stable
- Long-lived
- Rarely touched

This is the **anchor**.

------

## Why academic MPI discussions feel abstract

Because they focus on:

- Fellegi–Sunter theory
- Probabilistic math
- Matching accuracy
- Algorithms and weights

But they often skip:

- Rollback
- Audit
- Legal defensibility
- Human error
- Ops workflows
- Long-lived systems

In production, **matching accuracy is not the hard part**.

> **Undoing a wrong match safely is the hard part.**

That’s why the `person + xref` layer exists.

------

## The dangerous misconception (very common)

> “If I build a really good matching table, I don’t need the rest.”

This leads to:

- Hard merges
- Overwriting MRNs
- No unmerge
- No provenance
- Patient safety incidents

Many homegrown MPIs die here.

------

## The correct mental model (this is the insight)

Think of MPI like this:

```
mpi_patient_index = perception
person_patient_xref = judgment
person = identity
```

- **Perception** can be wrong
- **Judgment** must be auditable
- **Identity** must be stable

Commercial products succeed because they **never confuse these roles**.

------

## Why your realization is still valuable (important)

You *have* uncovered the real leverage point:

- If you get `mpi_patient_index` right:
  - Everything else becomes usable
  - Matching improves
  - Ops teams engage
  - MPI shows value quickly

That’s why:

- Products invest heavily here
- Performance tuning lives here
- Most vendor IP is here

But they all still rely on:

- a person table
- a linkage table
- merge governance

Even if they don’t call them that.

------

## One-paragraph truth you can reuse

> In practice, MPI value is delivered through a patient index optimized for fast, flexible matching. However, an index alone is not an MPI. True MPI capability emerges only when that index feeds into an explicit, auditable identity-resolution layer that supports merge and unmerge without altering clinical records. Commercial MPI products differ in algorithms, but they all share this architectural separation.

------

## Final verdict

- You’re right that **`mpi_patient_index` is the operational heart**
- It is **not sufficient by itself**
- Commercial MPIs are absolutely built around this concept
- They do **not** collapse identity truth into the index

If you remember just one thing:

> **Indexes find candidates.
> Links assert truth.
> Persons preserve reality.**

---

## Commercial Vendor Secret Sauce

You’re right that **`mpi_patient_index` is where most of the “daily engineering” and perceived value lives** (search, candidate finding, speed, and most tuning). But **commercial MPIs are not “based on that table” alone**.

The commercial secret sauce is the **combo**:

1. **Index layer** (your `mpi_patient_index`) — *find candidates fast*
2. **Identity assertion layer** (your `person_patient_xref`) — *record the truth + make it reversible*
3. **Identity anchor** (`person`) — *stable handle across time and systems*

Vendors may name these differently, but the pattern is universal.

------

## A commercial EMPI, deconstructed into your schema

Below are common vendor capabilities, translated into your “tables and flows” mental model.

### A. What vendors invest in around the “index” concept

This is what you’re sensing.

- **Indexing/blocking**: building fast candidate sets from demographics so they don’t compare every record to every record (this is your `mpi_patient_index` design and indexes).
- **Probabilistic matching** (weights, similarity functions, thresholds) to link records even when attributes differ. ([IBM](https://www.ibm.com/support/pages/probabilistic-matching-ibm-infosphere-master-data-management?utm_source=chatgpt.com))
- **Referential matching** (using third-party reference data to enrich matching) — common in “next-gen” EMPIs. ([InterSystems Corporation](https://www.intersystems.com/resources/intersystems-empi-factsheet/?utm_source=chatgpt.com))
- **Stewardship UI**: queues, side-by-side compare, explainability, exception handling (this is a workflow sitting on top of index outputs).

**Takeaway:** the index layer is the *engine room*.

------

### B. What vendors invest in that is *not* the index

This is what turns “search” into an MPI.

- **Explicit linkage and survivorship**: “this set of records belongs to the same person, and this is the survivor.” (your `person` + `xref` truth)
- **Merge and unmerge with auditability**: wrong merges happen; being able to reverse safely is a product feature and operational reality.
- **Governance + policy**: who can merge, thresholds for auto-link vs review, protected populations (twins, pediatrics), legal constraints.
- **Lifecycle and provenance**: why a link exists, who approved it, and when it changed.

**Takeaway:** without these, you have a great search tool—but not a reliable MPI.

------

## How this maps to FHIR (practical alignment)

### Patient linking (FHIR-native way)

FHIR already has a way to assert “these Patient records refer to the same actual patient” via **`Patient.link`**. ([FHIR](https://build.fhir.org/patient.html?utm_source=chatgpt.com))

- `Patient.link.other` → points to the related Patient (or RelatedPerson) ([FHIR](https://build.fhir.org/patient-definitions.html?utm_source=chatgpt.com))
- `Patient.link.type` → `replaced-by | replaces | refer | seealso` ([FHIR](https://build.fhir.org/patient-definitions.html?utm_source=chatgpt.com))

**Mapping to your model**

- `person_patient_xref` ≈ the *enterprise notion of equivalence/linkage*
- `Patient.link` ≈ the *FHIR representation of those link assertions between patient records*

In other words:

- Your MPI truth (“these records are the same person”) can be expressed outward as FHIR `Patient.link` relationships.

------

## HIE-scale MPI: what changes vs single-EMR MPI

When you go from EMR-only to HIE, the pattern stays, but you add more “industrial” concerns:

### What grows (beyond the index)

- **Assigning authority everywhere** (every `patient_id` must be qualified by source system/domain)
- **Data stewardship at scale** (queues, SLAs, conflict resolution)
- **Privacy segmentation & consent policies** (some links may be prohibited or masked depending on jurisdiction)
- **High availability + latency budgets** (registration can’t wait)
- **Event-driven sync** across systems (create/update events feeding your index)

The index stays important, but HIE success is often decided by:

- governance
- audit
- unmerge discipline
- operational workflows

------

## So… are commercial products “based on that table”?

### Correct framing

- **Yes:** commercial EMPIs absolutely depend on an internal equivalent of `mpi_patient_index` for search/matching performance.
- **No:** the product is not “based on that” alone. The product is the **full identity management system** around it (link truth, merge/unmerge, stewardship, audit, governance).

If you want a crisp one-liner:

> `mpi_patient_index` is the matching engine.
> `person + xref + audit + workflow` is the MPI.

------

## What vendors actually charge for (vs commodity)

### Commodity (you can build)

- patient index tables
- deterministic/probabilistic scoring
- basic linkage tables
- basic APIs

### Usually “vendor grade”

- referential matching partnerships and data (e.g., LexisNexis-backed referential matching claims in products like InterSystems EMPI) ([InterSystems Corporation](https://www.intersystems.com/resources/intersystems-empi-factsheet/?utm_source=chatgpt.com))
- stewardship UI + explainability
- governance workflow, role-based approvals
- operational tooling (monitoring, drift, backlog, SLAs)
- scale hardening (millions → tens/hundreds of millions)

------

## If you want “all” in a single mental picture

- **Index layer** (`mpi_patient_index`): speed + recall + precision
- **Truth layer** (`person_patient_xref`): explicit, auditable, reversible
- **Anchor** (`person`): stable ID for analytics + HIE interoperability
- **Product value**: everything that makes those three safe to run in real life

---

## Fellegi–Sunter theory

**Fellegi–Sunter (F–S) theory** is the **statistical foundation of probabilistic record linkage** — the math behind deciding whether two records refer to the **same real-world entity** when data is messy.

It’s the reason MPIs talk about *weights, scores, and thresholds* instead of simple equals/not-equals.

------

## The core idea (plain language)

Given two records, F–S asks:

> “How likely is it that these two records belong to the **same person** vs **different people**, based on how their fields agree or disagree?”

Instead of yes/no matching, it produces a **score**.

------

## The mental model (very important)

For any pair of records:

- **M** = probability the records **match** (same person)
- **U** = probability the records **do not match** (different people)

Each field (name, DOB, phone, etc.) provides **evidence** for M or U.

------

## Field-level logic (this is the heart)

For each attribute (e.g., DOB):

- If it **matches**, that increases confidence
- If it **doesn’t match**, that decreases confidence
- Some fields matter more than others

Each field gets a **weight** based on how discriminative it is.

### Example intuition

| Field       | Match means…         | Weight |
| ----------- | -------------------- | ------ |
| National ID | Very strong evidence | Huge   |
| DOB         | Strong evidence      | High   |
| Last name   | Moderate evidence    | Medium |
| Gender      | Weak evidence        | Low    |
| Phone       | Context-dependent    | Medium |

------

## The actual Fellegi–Sunter formula (simplified)

For a field *i*:

```
weight_i = log( P(field_i agrees | match)
              / P(field_i agrees | non-match) )
```

Then:

```
Total score = sum(weight_i across all fields)
```

You don’t need the math day-to-day — but this explains **why weights exist**.

------

## Decision thresholds (critical concept)

F–S defines **three zones**, not two:

| Score range | Meaning                 | MPI action                         |
| ----------- | ----------------------- | ---------------------------------- |
| High        | Very likely same person | Auto-link / auto-merge (carefully) |
| Middle      | Uncertain               | Human review                       |
| Low         | Likely different        | Ignore                             |

This is why good MPIs **never force binary decisions**.

------

## Why Fellegi–Sunter still matters today

Even with ML hype:

- Most production MPIs still use **F–S-style weighted scoring**
- ML often *learns the weights*, but the structure stays the same
- Regulators and auditors understand F–S logic
- It’s explainable (huge in healthcare)

------

## Why academic MPI explanations feel disconnected

They often focus on:

- the math
- probability theory
- optimal thresholds

But skip:

- rollback
- audit trails
- merge governance
- human error

That’s why theory alone doesn’t build safe MPIs.

------

## How F–S maps to *your* architecture

| Your component        | F–S role                      |
| --------------------- | ----------------------------- |
| `mpi_patient_index`   | Supplies field comparisons    |
| Matching algorithm    | Computes F–S-style weights    |
| Score thresholds      | Define auto / review / reject |
| `person_patient_xref` | Records the decision          |
| Merge workflow        | Handles uncertainty           |

**F–S stops at scoring.**
Everything after that is *engineering and governance*.

------

## The key realization (this is the “aha”)

> **Fellegi–Sunter tells you how confident you should be.
> It does not tell you what to do with that confidence.**

MPI systems exist because **real life needs reversibility**.

------

## One-line takeaway

> Fellegi–Sunter is the math that ranks “same person” likelihoods — MPIs are the systems that decide, record, and safely undo those decisions.

If you want next, I can:

- Translate F–S into **pseudo-code matching logic**
- Show **how commercial MPIs approximate F–S**
- Explain **why pure ML can be dangerous without F–S guardrails**
- Map F–S scores to your exact enum states

Just tell me.