Excellent — let’s strip away the fluff and replace the vague “roadmap” and “next steps” with a **realistic, hard-nosed implementation roadmap** that can actually make the **NHDR + DOH Digital Health Interoperability (DHI)** alignment *work* within the Philippine UHC ecosystem.

Below is a **frank rewrite** of what the NHDR–DHI roadmap *should be* — built around real governance, interoperability, and operational maturity milestones.

------

# NHDR–DHI Joint Implementation Roadmap (2025–2028)

### “From Data Chaos to One Health Data Ecosystem”

------

## Strategic Goal

> To establish a **Federated National Health Data Repository (NHDR)** that enables **standardized, secure, and interoperable data exchange** between health providers, DOH, and PhilHealth — supporting Universal Health Care (RA 11223) implementation, analytics, and evidence-based policymaking.

------

## Guiding Principles

1. **Submit Once, Share to All** — one submission from providers, shared to all authorized agencies.
2. **Federated by Design** — DOH and PhilHealth systems stay autonomous, connected via interoperability layer.
3. **Privacy by Default** — compliance with RA 10173 and NPC Circular 2023-01 integrated into architecture.
4. **Standards-based Interoperability** — all transactions conform to PH Core Data for Interoperability (PHCDI) and HL7-FHIR.
5. **Joint Stewardship** — DOH as *Data Steward*, PhilHealth as *Data Operator*.

------

## GOVERNANCE STRUCTURE (Immediate – Q1 2026)

**Objective:** Establish joint decision-making and accountability mechanism.

| Structure                                            | Lead                           | Key Deliverables                                             | Timeline |
| ---------------------------------------------------- | ------------------------------ | ------------------------------------------------------------ | -------- |
| **NHDR Joint Management Committee (JMC)**            | DOH (KMITS) + PhilHealth (IMS) | Terms of Reference; Joint Charter defining ownership, funding, and data access policies | Q1 2026  |
| **Technical Governance Subgroup**                    | DOH Digital Health TWG         | API management, interoperability governance, FHIR registry oversight | Q1 2026  |
| **Data Privacy and Ethics Board**                    | NPC + DOH Data Governance EG   | Privacy impact assessments, consent templates, data anonymization policy | Q2 2026  |
| **Regional Digital Health Clusters (pilot regions)** | CHDs + PhilHealth PROs         | Field-level implementation and provider support              | Q2 2026  |

> 🧩 *Without this governance layer, the NHDR remains an IT project, not a national health data system.*

------

## ARCHITECTURE IMPLEMENTATION PHASES

### Phase 1: Foundation and Policy Alignment (Q1–Q4 2026)

**Goal:** Make the legal, policy, and technical foundation operational.

| Deliverable                                             | Lead                                | Description                                                  |
| ------------------------------------------------------- | ----------------------------------- | ------------------------------------------------------------ |
| Finalized **NHDR Federated Architecture (Framework 3)** | PhilHealth IMS & DOH KMITS          | Adopt API passthrough model — agency systems connect via Interoperability Layer |
| Update of **Joint Policy Issuances**                    | DOH–PhilHealth                      | Amend JMC 2021-0001 to reflect federated model and NHDR governance |
| **Data Standards Consolidation**                        | DOH Standards & Interoperability EG | Publish PHCDI v2 with refined FHIR profiles and core datasets |
| **Integration of SCIV → NHDR pipeline**                 | DOH SCIV Team + PhilHealth IMS      | Ensure all validated EMRs can send data directly to NHDR endpoints |
| **Data Privacy Framework**                              | NPC + DOH                           | Data minimization, consent management, de-identification protocols |

**Success metric:** 3 priority datasets (Claims, eReferral, Disease Surveillance) fully mapped to FHIR.

------

### Phase 2: Pilot Implementation and API Federation (2027)

**Goal:** Prove that the model works in live systems.

| Deliverable                                                  | Lead                            | Description                                                  |
| ------------------------------------------------------------ | ------------------------------- | ------------------------------------------------------------ |
| **Pilot Deployment** (3 regions: NCR, Region IV-A, Region XI) | DOH–PhilHealth–DICT             | Operational NHDR passthrough for live claims and surveillance data |
| **API Federation Layer**                                     | PhilHealth IMS (Operator)       | Single endpoint for data submission, integrated queue management, authentication |
| **Shared Health Record Prototype**                           | DOH EB + UP NTHC                | FHIR-based patient summary accessible to both agencies       |
| **Regional NHDR Hubs**                                       | CHDs + PhilHealth PROs          | Regional caching and validation nodes (for GIDA resiliency)  |
| **Capacity Building Program**                                | DOH Capacity EG + PhilHealth HR | Certification for HIS officers and EMR vendors on FHIR submission |

**Success metric:** 70% of Konsulta and public hospital EMRs in pilot regions transmitting FHIR bundles via NHDR APIs.

------

### Phase 3: Scale and Institutionalization (2028–2029)

**Goal:** Integrate NHDR into national policy and budgeting cycles.

| Deliverable                                 | Lead               | Description                                                  |
| ------------------------------------------- | ------------------ | ------------------------------------------------------------ |
| **National Rollout of Federated NHDR**      | PhilHealth IMS     | Rollout to all regions; DOH and PhilHealth systems integrated via Interoperability Layer |
| **Legislative and Budget Support**          | DOH–PhilHealth–DBM | Include NHDR operations in annual appropriations and PhilHealth GAA |
| **Sector-Wide Data Governance Training**    | DOH Capacity EG    | Formal certification for Data Stewards in CHDs and LGUs      |
| **Decommissioning of Redundant Registries** | DOH + LGUs         | Rationalize legacy reporting systems (TB, FIC, etc.) integrated into NHDR |
| **Public Data Access Portal**               | DOH + PhilHealth   | Publish anonymized, open-access health data as per Sec. 31.8 UHC IRR |

**Success metric:** NHDR designated as **National Health Data Repository of Record** through joint DOH–PhilHealth AO.

------

## PERFORMANCE INDICATORS

| Domain                 | KPI                                             | 2026 Target | 2027 Target | 2028 Target |
| ---------------------- | ----------------------------------------------- | ----------- | ----------- | ----------- |
| **Interoperability**   | % of certified EMRs transmitting via FHIR       | 30%         | 70%         | 100%        |
| **Data Quality**       | % completeness and validity per dataset         | 80%         | 90%         | 95%         |
| **Timeliness**         | Data latency from source to NHDR dashboard      | <24h        | <12h        | <6h         |
| **Privacy Compliance** | % of systems with active DPO and PIA            | 70%         | 90%         | 100%        |
| **User Confidence**    | Provider satisfaction score (ease of reporting) | 60%         | 80%         | 90%         |

------

## COMMUNICATION AND ADVOCACY STRATEGY

1. **Message:** “Submit Once, Share to All.”
→ Promote NHDR as *provider-friendly* — not another layer of bureaucracy.
2. **Campaign:** “Your Data, Our Health”
→ Public-facing transparency initiative showing where data goes and how it improves health services.
3. **Channels:** DOH and PhilHealth joint digital media, CHD rollouts, EMR vendor webinars.
4. **Target Outcome:** Build *trust and compliance* through visibility and simplicity.

------

## CRITICAL SUCCESS FACTORS

1. **Governance before Technology** – The NHDR must be institutionally anchored before systems go live.
2. **Federation, not Centralization** – Avoid data hoarding. Each agency retains domain control.
3. **Legal Harmonization** – Joint AO + NPC endorsement = operational legitimacy.
4. **Workforce Professionalization** – Build a Digital Health Cadre under DOH–PhilHealth HR framework.
5. **Incremental Rollout** – Pilot, measure, adapt, scale — not “big bang” implementation.

------

## Summary: From Slideware to System

| Old Deck Reality                      | Revised Roadmap Response                    |
| ------------------------------------- | ------------------------------------------- |
| NHDR as a central PhilHealth database | NHDR as a federated, joint platform         |
| “Advocacy, Capacity Building” = vague | Sequenced, measurable deliverables          |
| Governance absent                     | Defined JMC and TWG structure               |
| Data privacy as afterthought          | Integrated privacy-by-design                |
| Conceptual frameworks                 | Phased national implementation with metrics |

------

### Final Statement

If this roadmap is adopted, **NHDR stops being a theoretical PowerPoint artifact** and becomes the **backbone of UHC data governance** — a living, federated system co-managed by DOH and PhilHealth, compliant with both technical and institutional realities.

------

Would you like me to produce a **one-page visual version** of this roadmap (like a Gantt-style chart or timeline infographic) for presentation to leadership or partners (e.g., DOH–PhilHealth Steering Committee)? It would summarize this strategy in a single slide.