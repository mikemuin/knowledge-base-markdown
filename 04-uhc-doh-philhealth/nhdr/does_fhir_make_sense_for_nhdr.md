### Reality check: where FHIR *actually* works in production

FHIR succeeds when the job is **patient-level, app-like data access** (REST, predictable resources, OAuth, tight scope). That’s why you see it used in real national/large-scale APIs like **NHS GP Connect** (primary care record access) and **CMS Blue Button** (claims-as-FHIR). ([NHS England Digital](https://digital.nhs.uk/developer/api-catalogue/gp-connect-access-record-structured-fhir?utm_source=chatgpt.com))

FHIR tends to *hurt* when the job is:

- **high-volume ingestion** (millions of rows/day) done as chatty REST calls (that’s a bad design)
- **aggregate public health reporting** (mostly counts/rates, not longitudinal patient graphs)
- **imaging payload exchange** (DICOM already owns that space)
- **claims submission** where a payer already runs mature EDI/XML pipelines

For big ingestion, “real-world FHIR” usually means **Bulk Data ($export) + NDJSON files**, not interactive CRUD. ([FHIR Build](https://build.fhir.org/ig/HL7/bulk-data/export.html?utm_source=chatgpt.com))

------

## What NHDR is asking for (practically)

NHDR is fundamentally a **national collection + analytics repo**: receive datasets, validate/store, then export to a data lake/warehouse for BI and research. The PhilHealth NHDR framework explicitly describes “Dataset Submission” storing to a **FHIR Server** then exporting to a **data lake**, and says NHDR “will not interpret” the submitted health data (i.e., it’s an ingestion boundary, not a clinical decision engine).

It also frames initial integrated HIS domains (EMR, referral, telemedicine, lab/diagnostics, eRx/dispensing, HR + ERP-ish modules).

------

# No-nonsense verdict: what should be FHIR APIs vs not

## 1) Medical / clinical (patient-level longitudinal data)

**Best fit for FHIR (but use Bulk/Data feeds for NHDR scale):**

- Patient demographics/identity slice (as used for clinical linkage)
- Encounters/visits, conditions/diagnoses, procedures
- Vitals & clinical observations
- Allergies, immunizations
- Med orders and dispensing events (capture-level, not “national eRx network” level)
- Care plans/referrals (the clinical payload)

**Why FHIR makes sense here:** this is the exact problem FHIR models well, and you can standardize a *core* dataset without boiling the ocean.

**How to do it “real-world”:**

- For NHDR ingestion: **FHIR Bulk Data export/import (NDJSON)** or scheduled bulk extracts (not millions of POSTs). ([FHIR Build](https://build.fhir.org/ig/HL7/bulk-data/export.html?utm_source=chatgpt.com))
- For point queries / app integrations (later): REST FHIR endpoints are fine (NHS-style). ([NHS England Digital](https://digital.nhs.uk/developer/api-catalogue/gp-connect-access-record-structured-fhir?utm_source=chatgpt.com))

**Gotcha:** FHIR only works if your **terminology layer** (ICD/LOINC/SNOMED/local value sets) is handled consistently. Otherwise you just get “standard JSON-shaped garbage.”

**Recommendation:** **FHIR** (Bulk-first; REST second).

------

## 2) Laboratory results

**What’s real in the field:** labs commonly exchange results using **HL7 v2 ORU-style messaging**, and many ecosystems still run on v2 as the workhorse. ([National Library of Medicine](https://www.nlm.nih.gov/oet/ed/healthdatastandards/03-300.html?utm_source=chatgpt.com))

**NHDR best practice:**

- Accept **HL7 v2** from labs where that’s what exists
- Optionally convert to FHIR Observations/DiagnosticReport internally for normalization

**Recommendation:** **HL7 v2 inbound** (plus optional FHIR as an internal canonical model).

------

## 3) Immunizations (and similar registry-style transactions)

In practice, immunization registries have long-run HL7 v2 guides (VXU/RSP patterns are common internationally). ([CDC](https://www.cdc.gov/vaccines/programs/iis/technical-guidance/downloads/hl7guide-1-5-2014-11.pdf?utm_source=chatgpt.com))

FHIR can work, but adoption is uneven unless the ecosystem already has FHIR clients.

**Recommendation:** **HL7 v2** for registry feeds (offer FHIR as an additional interface only if providers can actually use it).

------

## 4) Imaging (radiology, PACS, actual images)

Imaging exchange is dominated by **DICOM**; for web access you use **DICOMweb** REST services. ([DICOM](https://www.dicomstandard.org/using/dicomweb?utm_source=chatgpt.com))
If you’re sharing “documents” (reports/PDFs) across orgs, profiles like **IHE XDS** exist for document registration/retrieval. ([IHE Wiki](https://wiki.ihe.net/index.php/Cross-Enterprise_Document_Sharing?utm_source=chatgpt.com))

FHIR’s role here is usually just **metadata/indexing** (e.g., ImagingStudy pointing to DICOM objects), not moving images.

**Recommendation:** **DICOM/DICOMweb** for imaging payloads; **FHIR only for pointers/metadata**; **XDS** if you’re doing document-based exchange.

------

## 5) Pharmaceutical: e-prescribing networks vs “medication data”

Two different things get conflated:

1. **Medication history / orders / dispenses** (clinical record)

- FHIR is fine (MedicationRequest/MedicationDispense etc.)

1. **Actual ePrescribing transaction networks**

- Mature markets often use dedicated eRx transaction standards (e.g., **NCPDP SCRIPT** in the US Medicare Part D context). ([Centers for Medicare & Medicaid Services](https://www.cms.gov/medicare/regulations-guidance/electronic-prescribing/adopted-standard-and-transactions?utm_source=chatgpt.com))

If NHDR just needs reporting/analytics of meds, you don’t need to emulate an eRx network; you need reliable medication events.

**Recommendation:** **FHIR for medication events in the health record**; **don’t force FHIR to be the national eRx transaction protocol** (use whatever the operational eRx stack requires; often custom/local).

------

## 6) Health financing / claims / payments

PhilHealth already has a working production ecosystem for claims submission using **XML** via eClaims (and related specifications/circulars). ([PhilHealth](https://www.philhealth.gov.ph/services/eclaims/?utm_source=chatgpt.com))
FHIR *can* represent claims (e.g., CMS exposes claims data as FHIR ExplanationOfBenefit for access), but that’s **data access**, not **claims submission**. ([Blue Button](https://bluebutton.cms.gov/eob/?utm_source=chatgpt.com))

**Recommendation:**

- **Claims submission:** keep **existing PhilHealth XML/eClaims** (or future EDI-style formats), not FHIR.
- **Claims \*data access\* (analytics/beneficiary access):** FHIR can make sense **if** you’re building app-facing APIs.

------

## 7) Administrative / operations / HR / ERP / supply chain

These are not clinical interoperability problems. FHIR coverage here is either awkward or overkill.

**Recommendation:** **non-FHIR** (straight REST/JSON, CSV extracts, or domain standards like GS1 where relevant).

------

## 8) Public health reporting (aggregate)

Most public health program reporting is **aggregate** (counts, rates, denominators). Modeling this as patient-level FHIR resources is usually expensive and pointless unless you *specifically* need case-based surveillance.

**Recommendation:** **CSV/SDMX-like aggregates / program reporting formats** (or whatever the reporting platform consumes). Use **FHIR only for the patient-level case-based subset**.

------

# Bottom-line list (what in NHDR is truly appropriate for FHIR APIs)

### Strong “Yes: FHIR”

- Core **patient-level clinical record** subset (demographics, encounters, conditions, procedures, observations/vitals, meds, allergies, immunizations, referrals) — **Bulk FHIR for submission** is the practical choice.
- Provider/facility identity slices (Organization/Location/Practitioner) **if** you’re using them for linkage and access control.

### “FHIR only as a wrapper / internal canonical”

- Lab results (ingest HL7 v2, normalize to FHIR internally) ([National Library of Medicine](https://www.nlm.nih.gov/oet/ed/healthdatastandards/03-300.html?utm_source=chatgpt.com))
- Imaging (FHIR as index; DICOM/DICOMweb for content) ([DICOM](https://www.dicomstandard.org/using/dicomweb?utm_source=chatgpt.com))

### Better than other standards / existing pipelines

- Claims submission and billing artifacts → **PhilHealth eClaims XML** ([PhilHealth](https://www.philhealth.gov.ph/services/eclaims/?utm_source=chatgpt.com))
- Imaging exchange → **DICOM/DICOMweb**; document exchange → **IHE XDS** ([DICOM](https://www.dicomstandard.org/using/dicomweb?utm_source=chatgpt.com))
- Immunization registry feeds → **HL7 v2** is the practical default ([CDC](https://www.cdc.gov/vaccines/programs/iis/technical-guidance/downloads/hl7guide-1-5-2014-11.pdf?utm_source=chatgpt.com))
- Aggregate public health reporting → **non-FHIR** (bulk aggregates)
- HR/ERP/supply chain → **non-FHIR** (domain APIs/exports)

---

## Real-world PH Health IT / EMR / HIE status (what’s actually on the ground)

- **EMR adoption is still uneven and fragmented.** Even in government facilities, you have a mix of iHOMIS/iClinicSys, local custom systems, and paper/hybrid workflows; private hospitals run many different vendor HIS/EMRs. A 2023 case study notes that **21% of 311 hospitals surveyed had no EMR**, and in UHC implementation sites, **only ~7% had EMRs capable of submitting data to PhilHealth** (as of DICT 2020 data cited in the paper).
- **Government facility systems exist, but scale and maturity vary.** Same case study cites (as of 2018) **~70% of government hospitals implemented iHOMIS** and **~1,516 of 2,597 RHUs/primary care facilities had iClinicSys**—but “having a system” ≠ interoperable, high-quality exchange.
- **HIE is mostly pilots and pockets, not a national “always-on” exchange.** A 2026 implementation paper describes early PHIE efforts starting in 2015 but constrained by infrastructure/governance/digital readiness, and documents **local HIE pilots (SMILHIS) across three LGUs** as some of the first real LHIE implementations. ([PMC](https://pmc.ncbi.nlm.nih.gov/articles/PMC12915754/))
- **NHDR/PHCDI is moving, but still early-stage in delivery terms.** PhilHealth publicly **launched NHDR + PHCDI** in Dec 2024. ([PhilHealth](https://www.philhealth.gov.ph/news/up/article/2024/news_67524af3e4651.php))
But PhilHealth’s **Q2 2025 monitoring report** shows **NHDR listed at 2%** under ISSP deliverables—i.e., it’s not yet “fully built and scaled,” despite the launch event.
- **What’s reliably “digital at scale” today is claims, not clinical exchange.** PhilHealth is pushing **eClaims 3.0** with older versions disabled by **Mar 31, 2026**. That’s a strong signal where provider IT capacity is most mature: claims workflows. ([PhilHealth](https://www.philhealth.gov.ph/advisories/2025/PA2025-0076.pdf?utm_source=chatgpt.com))
- **FHIR capability is growing, but it remains a minority skill set.** There are active efforts around **PH Core / NHDR FHIR implementation guides** (draft builds exist) and community testing events—good momentum, but not yet “most providers can do FHIR natively.” ([FHIR Build](https://build.fhir.org/ig/UP-Manila-SILab/ph-core/branches/129-fr-a-add-contribution-guide/?utm_source=chatgpt.com))

------

## So… is FHIR appropriate for NHDR *right now*?

**Yes — but only in the way FHIR works in the real world:**

- Use FHIR as a **canonical model** for *core patient-level clinical data* and for *modern API access* (apps, HIE hubs, referral use cases).
- For NHDR-scale ingestion, do **bulk** (files / NDJSON / batch pipelines), not “everyone POSTs resources all day.”

This matches the NHDR framework’s own “dataset submission to storage” concept using a **FHIR server** as a storage/validation layer feeding analytics platforms.

**No — if you mean “FHIR everywhere, for everyone, as the only pipe.”**
Given the current PH ecosystem (many facilities still paper/hybrid, many EMRs not interoperable, and only a small fraction ready for electronic submission beyond claims), making FHIR the universal requirement up front will slow onboarding and widen inequity.

------

## Should PH start implementing FHIR “across the board” during NHDR pre-implementation planning?

**No. Don’t plan NHDR as a nationwide FHIR-only rollout.** Plan NHDR as a **multi-lane onboarding pipeline**:

### Where FHIR truly makes sense (do this)

- **PHCDI-aligned patient summary / encounter-level clinical core**
- **Referrals / continuity of care payloads**
- **National registries as FHIR resources** (patient, provider, facility) *where you need exchange and matching*

### Where FHIR is *not* the right first move (do something else)

- **Claims submission:** keep the established eClaims/XML pipeline; don’t rip-and-replace. ([PhilHealth](https://www.philhealth.gov.ph/services/eclaims/?utm_source=chatgpt.com))
- **High-volume reporting/aggregates:** do bulk file submissions (CSV/Parquet/NDJSON) + strong data quality rules.
- **Labs:** accept what labs can реально send (often HL7 v2 / flat files), then normalize internally if needed.
- **Imaging:** DICOM/DICOMweb for images; FHIR only for metadata/pointers.

------

## Straight answer

- **Is FHIR appropriate for NHDR in its current state?**
**Appropriate as a canonical model + optional API lane (and bulk submission pattern), yes. As the universally mandated interface for all submitters, no.**
- **Should PH implement FHIR across the board in NHDR pre-implementation planning?**
**No.** Plan **hybrid ingestion + phased FHIR adoption**: start with the clinical core + registries + referral use cases, while keeping claims and other domains on the standards/pipelines that already work. ([PhilHealth](https://www.philhealth.gov.ph/advisories/2025/PA2025-0076.pdf?utm_source=chatgpt.com))

---