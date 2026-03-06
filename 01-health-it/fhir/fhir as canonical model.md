# FHIR as a Canonical Model for NHDR: a realistic treatise

## 1) What “canonical model” actually means

A **canonical model** is the **single internal shape** you normalize data into so you don’t build endless point-to-point mappings.

- **Inbound:** many formats → **canonical**
- **Outbound:** **canonical** → many formats/use-cases

**FHIR as canonical ≠ everyone must speak FHIR.**
It means: *inside NHDR*, you normalize to FHIR (or FHIR-like), so validation, linkage, querying, and reuse are consistent.

------

## 2) The one thing people get wrong: “FHIR canonical” vs “FHIR everywhere”

Two different decisions:

1. **Canonical representation (internal)**
   - What NHDR stores/normalizes to.
2. **Exchange contracts (external)**
   - What submitters must send (FHIR REST, bulk files, HL7 v2, custom JSON, etc.).

In the real world, NHDR needs **multiple onboarding lanes** even if the canonical is FHIR.

------

## 3) Where FHIR is genuinely strong as a canonical model

FHIR works best when the data is:

- **patient-level, event-based**
- intended for **reuse** (clinical continuity, longitudinal record, apps, research extracts)
- can be expressed with **stable terminologies** (or controlled local value sets)

**Strong-fit canonical slice (patient/encounter level):**

- Patient demographics & identifiers (not all identity governance, just the clinical identity slice)
- Encounters/visits
- Problems/diagnoses, procedures
- Observations (vitals, many lab results as observations)
- Med events (orders/dispense administrations depending on scope)
- Immunizations
- Referrals / clinical summaries (as structured payload + document references)

------

## 4) “Real-world FHIR” for NHDR is bulk-first, not chatty REST

FHIR REST is great for app workflows and small/interactive queries.
For national ingestion at scale, it usually fails when used as: “POST millions of resources daily.”

**Practical pattern:**

- **Bulk submission** (files, NDJSON, scheduled extracts) + validation + load
- Optionally expose **FHIR REST** as a *second* lane for capable submitters / app integrations

If you do FHIR canonical but force everyone into transactional REST, you create the worst of both worlds.

------

## 5) The extensions reality (this is where FHIR projects die)

Extensions are not inherently bad. What kills interoperability is:

- uncontrolled vendor-specific extensions
- free-text without value sets
- inconsistent meaning (“same field, different semantics”)
- weak governance (“we’ll fix it later in ETL”)

### Your examples: what needs extensions and what doesn’t

- **Marital status:** core FHIR element (`Patient.maritalStatus`) → no extension.
- **Dietary requirements:** can be represented (simple preferences vs full nutrition needs) → often no extension if you scope it correctly.
- **Religion:** not a core element in R4 Patient, but there’s a **standard HL7 extension** commonly used. That’s still “canonical” *if everyone uses the same URL and value set*.

### A useful “extension threshold” rule

If your “patient/encounter canonical” needs:

- **a few standard extensions** → fine
- **dozens of PH-specific extensions** or frequent “other/specify” text → you’re building a custom standard wearing FHIR clothes

At that point, either:

- narrow the canonical scope (recommended), or
- adopt a different canonical representation for those attributes (see next section)

------

## 6) The long-tail problem: intake/admin fields aren’t always worth canonicalizing in FHIR

Even patient-level data has a **long tail**:

- religion, occupation, household details, socio-economic markers, special consents, preferences, local program fields, etc.

You have three realistic options:

### Option A: Put them all in FHIR via extensions (high risk)

- Pros: one model, one API family
- Cons: extension explosion, governance heavy, vendors diverge, analytics messy

### Option B: “Dual model” (recommended for NHDR)

- **FHIR canonical for the clinical core**
- A separate **attribute store** for long-tail person/encounter attributes (key-value or relational)
  - Example shape: `PersonAttribute(personId, attributeCode, valueCode/valueText, effectiveDate, source, confidence)`
- Later, only promote stable, high-value attributes into FHIR profiles/extensions.

### Option C: Non-FHIR canonical for analytics + FHIR at the edge

- Canonical is a warehouse-friendly table format; FHIR is just an API/export format.
- Strong for BI/research; weaker for app-level interoperability.

------

## 7) Multi-canonical by domain is normal (and sane)

Even if you use FHIR canonical for clinical data, NHDR shouldn’t force everything into FHIR.

**Domains that are usually better with other standards/pipelines:**

- **Imaging payloads:** DICOM/DICOMweb; FHIR only for pointers/metadata.
- **Labs inbound:** HL7 v2 is still common; normalize to FHIR optionally.
- **Claims submission:** keep existing claim formats (don’t “FHIR-ify” submission because it’s trendy).
- **Aggregate reporting:** file-based aggregates (CSV/Parquet) are usually better than patient-level FHIR.

This is not “anti-FHIR.” It’s using the right tool per domain.

------

## 8) What “FHIR canonical” must include to be real, not aspirational

If you pick FHIR as canonical for a slice, you need these to avoid chaos:

### 8.1 Clear scope

- Exactly which use-cases and datasets are canonicalized in FHIR (e.g., PHCDI core)
- What is explicitly *out of scope* (long-tail attributes, ops/ERP, etc.)

### 8.2 A constrained profile set

- Small number of profiles + tight cardinalities
- Avoid optional-everything payloads
- One approved extension set (prefer standard extensions first)

### 8.3 Terminology strategy (non-negotiable)

- Value sets per field (even if local)
- Mapping guidance (local codes → canonical codes)
- Validation rules (reject/flag nonconforming data)

Without this, you get “FHIR-shaped JSON” that isn’t interoperable.

### 8.4 Patient matching/identity approach

FHIR won’t magically solve identity.
You still need:

- Identifier policy (which IDs are trusted)
- Matching rules
- Dedup strategy and provenance

### 8.5 Provenance and auditability

NHDR must know:

- Who sent what
- When it was generated
- What transformations were applied

FHIR can represent provenance, but you must operationalize it.

------

## 9) A practical NHDR onboarding model (works in mixed-maturity ecosystems)

Think “lanes,” not “one interface”:

### Lane 1: FHIR-native submitters

- Can send validated FHIR bundles or bulk exports.

### Lane 2: Translated submitters

- Submit HL7 v2 / custom JSON / CSV → NHDR gateway maps into canonical FHIR.

### Lane 3: File-first submitters

- Periodic extracts (CSV/NDJSON/Parquet) with strict schema + mapping tables.

**Key point:** Canonical can still be FHIR even when most submitters don’t speak FHIR.

------

## 10) Decision checklist: Should FHIR be canonical for a given dataset?

Use FHIR canonical when:

- It’s patient/encounter event data
- Multiple downstream uses need it (HIE, clinical analytics, patient summary, research)
- Semantics are stable, and terminology can be controlled
- The extension need is **limited and governable**

Avoid (or don’t start with) FHIR canonical when:

- The dataset is long-tail admin attributes with unstable definitions
- It’s mostly aggregate metrics
- It’s better served by a mature domain standard/pipeline
- You’ll need heavy PH-specific extensions with unclear value sets

------

## 11) Bottom-line stance (objective, not zealot)

- **FHIR is a good canonical model for a defined clinical core** (patient/encounter-level) if you implement it as bulk-first, with tight profiles, strong terminology, and controlled extensions**.
- **FHIR is not a good canonical model for “everything patient-level.”** The long tail will push you into extension sprawl, and then it stops being a shared canonical.
- For NHDR, the most realistic approach is to use the FHIR canonical for the clinical core, separate modeling for long-tail attributes, and non-FHIR standards where they dominate**.

