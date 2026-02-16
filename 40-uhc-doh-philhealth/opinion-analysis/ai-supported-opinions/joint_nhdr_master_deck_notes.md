# Joint NHDR Master Deck Notes

## 1. Initial Assessment

Here’s a complete presentation notes and assessment for **MasterDeck_JOINT_NHDR_DHI.pptx** — including slide insights, strategic interpretation, and key recommendations.

------

### Overall Assessment

The deck provides a comprehensive overview of the **National Health Data Repository (NHDR)** initiative under the **PhilHealth–DOH collaboration**, aligned with the **UHC Act (RA 11223)**. It covers policy grounding, architectural frameworks, interoperability plans, and governance structures. However, it’s dense, highly technical, and lacks a clear narrative flow connecting the NHDR concept to operational outcomes.

The presentation serves well for internal technical discussions but would benefit from improved visual clarity, simplification of technical content, and a unified strategic direction emphasizing **"why NHDR matters"** and **"how it will transform the healthcare data ecosystem"**.

------

### Slide-by-Slide Notes and Insights

#### Opening & Objectives

- **Speakers:** Lemuel Untalan (PhilHealth), Teodulfo Gallano Jr. (PhilHealth)
- **Intent:** Review and redesign the NHDR framework, identify challenges, and update the roadmap.
- **Insight:** Objectives are sound but need a stronger articulation of success indicators — e.g., “achieve full data interoperability by 2028.”

------

#### NHDR Key Insights, Pain Points, and Challenges

- **Speakers:** Emily Razal, Alan Sabeniano
- **Key Issues:**
  - Fragmented data collection and multiple redundant submissions.
  - Manual and non-standardized submission processes.
  - No unified roadmap or governance.
  - Lack of funding and sustainable staffing (mostly contractual).
  - Weak data privacy governance and public trust.
  - Connectivity issues in geographically isolated and disadvantaged areas (GIDAs).
- **Assessment:**
The pain points correctly reflect systemic issues across government agencies. They point to a lack of enterprise architecture and fragmented mandates between DOH and PhilHealth.
- **Recommendation:**
Introduce a **Data Governance Charter** defining roles, data custodianship, and accountability for interoperability and privacy compliance (aligned with DPA 2012 and NDPHIS initiatives).

------

#### UHC Law Context (Sections 31, 36, etc.)

- **Insight:**
This section properly anchors NHDR in its legal mandate. However, it should visually link each legal clause to **specific NHDR data functions** — e.g., EHR → Section 36 interoperability → NHDR data standard adoption.
- **Recommendation:**
Simplify and convert into a “UHC Mandate to NHDR Deliverables Map” (law → requirement → NHDR response).

------

#### NHDR Framework Overview

- **Insight:**
Clearly defines NHDR as the **“single source of truth”** for all health-related data in the Philippines. It distinguishes **centralized** and **decentralized** models.
- **Assessment:**
Strong technical foundation referencing DOH–PhilHealth JAO 2021-0002 and standard terminologies (ICD-10, SNOMED CT, LOINC, DICOM, ISO 8601, etc.).
However, lacks clarity on **data governance processes** (who validates, who stores, who owns).
- **Recommendation:**
Adopt a **federated architecture** (like Framework 3) combining flexibility and centralized oversight. Clarify data flow diagrams with labels (sources, APIs, validation nodes).

------

#### Digital Health Governance (COBIT 2019 Alignment)

- **Speaker:** Dir. Bently Roxas
- **Insight:**
Presents the governance model — Steering Committee, TWG, Experts Groups mapped to COBIT 2019 domains (Plan, Build, Run, Monitor).
Excellent structure but bureaucratically heavy.
- **Recommendation:**
Propose a **Lean Governance Model** with fewer decision layers and stronger data stewardship functions under a **Health Data Governance Office (HDGO)** shared by DOH–PhilHealth–DICT.

------

#### Experts Group Key Deliverables

- **Data Governance & Architecture:** Issue a sector-wide enterprise architecture plan.
- **Cybersecurity & Privacy:** Capacity-building and compliance programs with DPA and ISO 27001.
- **Standards & Interoperability:** HL7 FHIR implementation and validation through SCIV Program.
- **Capacity Building:** Digital health training integration into health education.
- **Insight:**
These are well aligned with global best practices. The FHIR pilot and conformance testing are critical wins.
- **Recommendation:**
Prioritize **FHIR-based APIs** and **SCIV certification** as compliance requirements for all EMR vendors by 2026.

------

#### Technical Interoperability and Conformance

- **Insight:**
The SCIV Program aligns DOH technical specs with EMR providers (e.g., Konsulta, MyEMR, eHIS, Cure).
Ensures uniform data exchange through HL7 FHIR.
- **Assessment:**
The phased approach (specs → publishing → regional rollout → testing → monitoring) is solid but implementation capacity is the key risk.
- **Recommendation:**
Create a **Central Interoperability Testing Sandbox** under UP-NTHC for national validation, training, and conformance tracking.

------

#### Architecture Framework Options

- **Framework 1:** Centralized — Single source, but bottlenecks and single point of failure.
- **Framework 2:** Decentralized — Agency independence, but redundant systems.
- **Framework 3:** Interoperability layer — Balanced; allows pass-through APIs, reduced risk.
- **Insight:**
Framework 3 is the optimal hybrid model (federated architecture). It supports both autonomy and unified reporting.
- **Recommendation:**
Adopt **Framework 3** as the “future state vision,” integrate with **PhilHealth HIMS** and **DOH HIE** via shared interoperability APIs.

------

#### Current State Analysis (Hairball Architecture)

- **Insight:**
Excellent visual metaphor. Accurately describes the current state: fragmented data exchange between thousands of facilities and multiple agencies.
- **Recommendation:**
Use this slide early in the deck to **justify urgency** and lead into the NHDR “future vision.”

------

#### Reference Architecture & Conceptual Model

- **Insight:**
Describes a multi-layer model:

  - Clinical & operational systems (source)
  - Health interoperability layer (HIL)
  - OLAP data warehouse
  - BI and dashboarding layer

The model uses FHIR Implementation Guides as connectors — technically sound.

- **Recommendation:**
Expand visuals to show **data anonymization**, **role-based access**, and **feedback loops** to health facilities for data quality improvement.

------

#### Roadmap & Action Planning

- **Facilitators:** Cherrie Esteban, Emily Razal, Dr. Muin
- **Objective:** Translate gap analysis into actionable roadmap.
- **Assessment:**
The roadmap needs stronger **time-bound milestones**, KPIs, and clear owners.
- **Recommendation:**
  - **Short Term (2025–2026):** NHDR–HIE API pilot, SCIV rollout, governance formalization.
  - **Medium Term (2027–2028):** Full interoperability compliance for top 8 DOH outcomes.
  - **Long Term (2029–2030):** NHDR expansion to LGUs and private HMOs, full analytics deployment.

------

#### Strategic Insights

1. **Adopt a Federated Data Architecture (Framework 3)** – Enables scalability, interoperability, and agency independence.
2. **Institutionalize NHDR Governance** – Through a joint DOH–PhilHealth data governance board with DICT oversight.
3. **Mandate FHIR-Based Standards** – For all EMR vendors under DOH certification; link compliance to PhilHealth accreditation.
4. **Centralize Conformance Testing (SCIV)** – Use UP-NTHC as national interoperability lab.
5. **Build Workforce Sustainability** – Convert key contractual IT staff into plantilla positions for continuity.
6. **Prioritize Public Trust and Data Privacy** – Transparent communication on how data is collected, stored, and used.
7. **Leverage Analytics for Policymaking** – Integrate NHDR outputs into DOH-PhilHealth dashboards for UHC monitoring.

------

Would you like me to turn these notes into a **presentation script or speaker guide** (per slide talking points for delivery)?

---

## No Bullshit

Straight up — here’s the no-fluff take.

------

#### Overall Impression

The deck tries to do too much and ends up being more **bureaucratic than strategic**. It’s packed with references, mandates, and frameworks, but it reads like a compliance report — not a plan that will actually get NHDR built. It identifies all the right pain points but doesn’t offer a realistic, resourced, or accountable path to fix them.

There’s clear awareness of the problems — fragmented systems, redundant data collection, poor governance, and weak capacity — but the proposed solutions are mostly **structural rearrangements**, not operational game changers.

------

#### What’s Good

- The **UHC law anchoring** is solid — it ties NHDR to a legal mandate.
- **Recognition of HL7-FHIR** and interoperability standards is on point.
- The **SCIV Program** and use of UP-NTHC as a conformance lab is a strong move.
- Framework 3 (federated model) is technically sound and the only one that scales in PH context.
- Governance reorg aligning to COBIT 2019 is conceptually right.

------

#### What’s Weak

1. **Too much theory, not enough execution.**
The deck leans heavily on frameworks and jargon but doesn’t say *who’s actually doing what by when*. There’s no timeline with accountability, funding plan, or manpower strategy.
2. **Governance overkill.**
Six “expert groups” and multiple committees for a system that doesn’t even exist yet is premature. You need a small, empowered core team before building bureaucracy.
3. **Funding and sustainability ignored.**
Every major risk (contractual workforce, unclear funding) is listed, but no concrete fix — just vague “need for sustainable manpower.”
4. **Tech stack clarity missing.**
There’s no architecture diagram showing where the NHDR lives (cloud? hybrid? on-prem?), how APIs are managed, or what the actual data flow looks like end-to-end.
5. **No operational buy-in.**
There’s zero reference to how hospitals, LGUs, or EMR vendors will be incentivized or supported to comply. The NHDR is useless if data sources don’t participate willingly and consistently.
6. **Change management is an afterthought.**
The slide says “how to address change management” — that’s not a plan, that’s a placeholder.

------

#### Reality Check on Recommendations

- **Federated architecture** (Framework 3) is the right call — but only if DOH, PhilHealth, and DICT agree to one interoperability layer and joint API governance. That alignment doesn’t exist yet.
- **Data governance board** sounds nice, but unless it has real authority and budget, it’s just another committee.
- **FHIR-based compliance for EMRs** — doable, but needs regulatory teeth (make SCIV certification a licensing or PhilHealth accreditation requirement).
- **Conformance testing via UP-NTHC** — smart, but it’ll fail if there’s no funding for lab operations and maintenance.
- **Workforce regularization** — crucial, but civil service bureaucracy will drag this for years without executive pressure.
- **Public trust** and **data privacy** — real risks. Without a public-facing communications plan and transparency policy, this will get political backlash once data starts flowing.

------

#### Bottom Line

- The **direction** is right.
- The **execution readiness** is poor.
- The **architecture recommendation** (federated model) is sound but needs governance and budget muscle.
- The **deck needs to evolve from “plan to plan” to “plan to build.”**

If they want NHDR to actually move, they need:

1. One accountable lead agency (joint DOH–PhilHealth is too fuzzy).
2. A lean, funded, technically competent implementation unit.
3. Clear interoperability policy enforced through accreditation.
4. Realistic rollout timeline with priority use cases (claims, e-referrals, surveillance).
5. Cloud-first architecture with secured data-sharing protocols.

------

Want me to rewrite the recommendations section into a version that would actually convince senior DOH/PhilHealth execs to approve and fund it?

---

## Rewrite

Here’s a straight, executive-ready version of the recommendations — the kind you’d put in front of DOH and PhilHealth execs to make them actually act:

------

### Actionable Recommendations for NHDR Implementation

#### 1. Establish One Accountable Lead

- Designate **DOH as the primary data custodian** and **PhilHealth as the co-implementer** for financing-related datasets.
- Issue a **Joint Administrative Order** explicitly assigning NHDR ownership, governance, and enforcement authority.
- Create a **single NHDR Program Management Office (PMO)** with embedded technical staff from DOH, PhilHealth, and DICT.
  - **Structure:** 1 program director, 3 technical leads (interoperability, data governance, infrastructure), 1 finance/operations lead.
  - **Budget:** Secure multi-year funding via GAA or DOH-PhilHealth cost-sharing.

------

#### 2. Adopt the Federated (Framework 3) Architecture

- Implement **an interoperability layer (NHDR-HIL)** that connects DOH, PhilHealth, and facility systems using **HL7-FHIR APIs**.
- Each agency maintains its own operational data systems; NHDR consolidates through API passthroughs.
- Mandate use of **national identifiers** (PhilHealth ID, NHFR, PSGC, etc.) and DOH-PHIC data standards.
- Use **queuing and caching** to avoid data bottlenecks; deploy on **GovCloud** with DICT security compliance.

------

#### 3. Enforce Compliance Through Accreditation

- Make **SCIV certification** (DOH FHIR conformance) a **requirement for EMR licensing** and **PhilHealth accreditation** by 2026.
- Link submission of NHDR-compliant data to **facility licensing renewal** (DOH) and **claims processing eligibility** (PhilHealth).
- Provide a **transition window (18–24 months)** with technical support and sandbox access for EMR vendors and facilities.

------

#### 4. Build a Sustainable Technical Workforce

- Convert key **NHDR project staff to plantilla positions** within DOH and PhilHealth IM offices.
- Partner with CHED and DICT to integrate **Digital Health Informatics** tracks in public health and IT programs.
- Use **service contracting or PPP models** to retain developers and data engineers during transition years.

------

#### 5. Institutionalize Governance and Data Stewardship

- Form a **Health Data Governance Board (HDGB)** chaired by DOH CIO, co-chaired by PhilHealth SVP-IMS, with DICT and NPC participation.
- Functions:
  - Approve data standards and interoperability rules.
  - Enforce compliance and audit data submissions.
  - Oversee cybersecurity and privacy controls.
- Secretariat function handled by the NHDR PMO.

------

#### 6. Prioritize Quick Wins

Focus initial rollout on **high-impact, data-rich use cases**:

1. **Claims data integration** (PhilHealth → NHDR → DOH)
2. **Electronic referrals and Konsulta records**
3. **Disease surveillance (COVID, TB, Dengue)**
4. **Licensing and facility master data**

These create early value and demonstrate system capability while building confidence among stakeholders.

------

#### 7. Strengthen Public Trust and Data Privacy

- Publish a **Citizen Data Use Policy** explaining how health data is collected, anonymized, and used.
- Implement **privacy-by-design** measures, including PII scrubbing and de-identification pipelines.
- Set up a **public data dashboard** for transparency and compliance monitoring.

------

#### 8. Secure Long-Term Infrastructure and Funding

- Host NHDR on **DICT GovCloud** or an equivalent sovereign cloud platform.
- Secure **joint DOH–PhilHealth annual funding** (target ₱100–₱150M/year for infra, licenses, manpower).
- Include **PPP or donor-funded support** (ADB, WHO, USAID) for FHIR lab operations and analytics capacity-building.

------

#### 9. Deliver Results by Phases

| Phase       | Period    | Focus             | Key Outputs                                                |
| ----------- | --------- | ----------------- | ---------------------------------------------------------- |
| **Phase 1** | 2025–2026 | Pilot & Standards | NHDR-HIL prototype, FHIR sandbox, 3 pilot facilities       |
| **Phase 2** | 2026–2027 | Scale-Up          | Nationwide SCIV rollout, accredited EMRs integrated        |
| **Phase 3** | 2028–2030 | Institutionalize  | Full data governance enforcement, UHC analytics dashboards |

------

#### 10. Shift Mindset: From “Compliance” to “Capability”

The NHDR should not just be a data dump. It should be the **backbone for evidence-driven health policy**. That means measurable results:

- 80% reduction in redundant data submissions
- 50% faster access to validated health statistics
- Real-time analytics for UHC indicators

------

Would you like me to turn this into a short **executive summary slide deck (5–7 slides)** that you can present to DOH/PhilHealth leadership? It’ll highlight only the essentials: what’s broken, what’s proposed, what’s needed now.

---

## FHIR?

You’re absolutely right — pushing FHIR prematurely in the current PH environment is impractical.

Here’s the straight assessment and rationale:

------

#### 1. FHIR is aspirational, not operational

FHIR (especially R4/R5) is where global standards are heading, but most Philippine EMRs — including the major ones used for Konsulta and LGU deployments — were built on **relational DBs with proprietary schemas** and legacy HL7 v2.x messaging. They can’t just “turn on” FHIR.

- Retrofitting FHIR resources needs data mapping, resource definition, and version control.
- Even the DOH SCIV program is still in pilot; the technical specs exist, but **no live conformance** yet.
- If we wait for everyone to be FHIR-ready, NHDR will stall for years.

------

#### 2. v2 and existing formats are a working bridge

HL7 v2.x is messy but mature — and it’s already supported by several hospital systems.
You can immediately start collecting standardized data through:

- **ADT, ORU, and DFT messages** from hospital HIS or LIS systems.
- **Batch CSV/JSON uploads** (for smaller clinics) using DOH standard templates.
- **REST endpoints** that wrap around existing data structures and transform them server-side into a consistent schema.

That lets you start receiving structured data **next week**, as you said — which means immediate momentum.

------

#### 3. Readiness is the real bottleneck

Before standardizing the format, you need to **measure who can send what, and how.**
That means:

- **Capability survey** across facilities and EMR vendors — can they produce HL7, XML, JSON, or CSV?
- **Connectivity check** — bandwidth, uptime, and security readiness (VPN, HTTPS, or courier).
- **Data quality profiling** — completeness, accuracy, consistency of required fields.

You’ll find maybe 5–10% of facilities are ready for API-based submission today, 30–40% can export structured CSVs, and the rest are manual or fragmented.

------

#### 4. Practical rollout strategy

**Stage 1: Accept what exists (v2, CSV, JSON)**

- Define minimal submission datasets (e.g., patient ID, encounter, diagnosis, claim, facility ID).
- Standardize field naming and code sets (ICD-10, PhilHealth ID, NHFR, PSGC).
- Ingest data into NHDR via a **staging layer** that can normalize formats on ingestion.

**Stage 2: Gradual FHIR mapping**

- Use a middleware translator (FHIR façade) to map incoming v2 or CSV into FHIR bundles internally.
- This allows future compliance without forcing every submitter to adopt FHIR right away.

**Stage 3: Capability-based onboarding**

- Categorize submitters:
  - **Tier 1:** v2/API capable — go live early.
  - **Tier 2:** Batch file upload — onboard within 6–12 months.
  - **Tier 3:** Manual entry via portal — interim compliance.

------

#### 5. The hybrid path is the only realistic one

If the NHDR insists on FHIR-only, you’ll alienate 90% of the ecosystem.
If you stay v2-only, you lock yourself in legacy.
The win is **hybrid ingestion** — start with what’s available, normalize centrally, and evolve toward FHIR as infrastructure matures.

------

#### 6. Bottom line

- Implementing FHIR now, in a market that’s not ready, guarantees failure.
- Implementing a **FHIR-compatible staging layer** while ingesting v2/CSV today guarantees progress.
- The immediate goal should be **data flow, not data perfection.**

------

Would you like me to lay out a **transition architecture diagram** showing how v2 and CSV submissions can feed into a FHIR-ready NHDR pipeline? It’ll make this hybrid approach crystal clear for decision-makers.

---

## Sample of Single Protocol?

Not successfully — and not in the Philippines.

Here’s the reality based on global and local experience:

------

### 1. No country started with a single mandated protocol.

Every country that built a national health data exchange **began hybrid** and converged over time.

- **US (ONC / HIEs):**
HL7 v2, CDA, CSV, flat files, and APIs coexisted for over a decade before FHIR gained traction.
Even today, v2 messages still dominate labs and ADT feeds. TEFCA only *recommends* FHIR, it doesn’t *mandate* it exclusively.
- **UK (NHS Spine / GP Connect):**
Used multiple formats (EDIFACT, CSV, XML, HL7 v3) for years.
NHS Digital introduced FHIR gradually for specific use cases — not system-wide mandates.
- **Singapore (NEHR):**
Started with HL7 v2 + CDA documents, transitioned to FHIR selectively.
Interoperability is achieved via adapters, not protocol mandates.
- **Thailand & Malaysia:**
Also adopted hybrid ingestion — HL7 v2 and custom XML/JSON uploads coexist, especially in provincial health offices.

------

### 2. Philippine context — multiple protocols already exist

There’s never been a single mandated data submission protocol in national health IT projects.
Examples:

- **FHSIS (DOH):** Manual and Excel/CSV submissions, later through the FHSIS online portal (custom API).
- **PhilHealth Claims:** E-Claims system uses XML and flat files (proprietary schema).
- **DOH-PhilHealth JMC 2021-0001:** Requires “electronic submission conforming to standards,” but does *not* specify one protocol.
- **Konsulta EMRs:** Send structured JSON over HTTP APIs; some vendors use CSV export templates.

So in practice, **Philippine data exchange is—and will remain—multi-format** for the foreseeable future.

------

### 3. Why “one protocol” mandates fail

- **Legacy lock-in:** Thousands of facilities can’t rebuild their EMRs or middleware.
- **Capacity disparity:** LGU clinics, tertiary hospitals, and private labs all have different capabilities.
- **Vendor ecosystem:** Forcing one standard alienates vendors and stalls adoption.
- **Evolving standards:** By the time you enforce one, the global standard changes (FHIR is already shifting to R5 and beyond).

------

### 4. What actually works

- Define **a canonical data model**, not a single transmission protocol.
- Accept multiple formats (v2, CSV, JSON), normalize at the NHDR ingestion layer.
- Use **FHIR internally** to ensure the long-term semantic structure is future-proof.
- Phase out legacy formats only after majority adoption and verified readiness.

------

### Bottom line

No major health data project — not even in countries with far better infrastructure — ever succeeded by forcing a single protocol from day one.
They succeed when they **define the data, not the transport**, and let technology catch up.

If NHDR wants nationwide compliance, it must **ingest everything and standardize centrally**, not wait for FHIR to be universal.

---

## Realistic, No BS

Realistically — if you want NHDR to move **from concept to working system**, here’s the blunt, field-tested plan:

------

### 1. Stop chasing a “single protocol”

You’ll never get every facility, EMR vendor, and LGU on one standard in the next 3–5 years.
Mandating FHIR-only or API-only submissions will kill momentum.
**Accept data in any structured format (v2, CSV, JSON)** that meets minimum data element standards.
Normalize it centrally.

**→ Principle:** Don’t make facilities “speak FHIR.” Make NHDR *understand whatever they can send.*

------

### 2. Define and publish the canonical data model

Forget about transmission first — define what data looks like:

- Required fields per domain (Patient, Encounter, Claim, Lab Result, Facility).
- Standard identifiers and terminologies (PhilHealth ID, NHFR, PSGC, ICD-10, LOINC).
Once that’s frozen, *anyone can map to it*, regardless of transport protocol.
That’s the real foundation of interoperability.

**→ Deliverable:** DOH–PhilHealth “NHDR Canonical Dataset v1.0” — not an API spec, just the data dictionary.

------

### 3. Deploy a hybrid ingestion pipeline now

Stand up **v2 listeners, SFTP batch upload, and HTTPS JSON endpoints** — same backend normalization.
Let facilities choose the method that fits their capability.
Process all incoming data into a unified schema (using an ETL or FHIR façade).

**→ You start getting data next week instead of next administration.**

------

### 4. Gradually phase in FHIR where it matters

Use FHIR *internally* in NHDR and *selectively* for high-value, API-driven use cases:

- eClaims API
- eReferral between hospitals
- Disease surveillance (DOH-led)

Keep it optional for data submitters until at least 2027.
Make it mandatory only for **new systems** or **vendors under new contracts.**

------

### 5. Build a readiness matrix

Every facility or EMR vendor gets a “readiness score”:

| Capability                       | Tier   | Format     | Support Needed           |
| -------------------------------- | ------ | ---------- | ------------------------ |
| Fully integrated HIS (HIE-ready) | Tier 1 | HL7 v2/API | Minimal                  |
| EMR with export                  | Tier 2 | CSV/JSON   | Template + QA support    |
| Manual data entry                | Tier 3 | Web form   | Portal access + training |

Then prioritize onboarding Tier 1 and 2 — don’t waste resources trying to make GIDA clinics use APIs first.

------

### 6. Fund a central translation layer

Put the budget into **middleware and mapping**, not endless stakeholder training.
A small expert data engineering team can maintain converters (v2→canonical→FHIR).
That’s faster, cheaper, and more scalable than forcing standard compliance across 5,000+ facilities.

------

### 7. Use policy levers for gradual enforcement

By 2026, tie compliance with the canonical dataset (not FHIR itself) to:

- DOH licensing renewal
- PhilHealth accreditation
- Access to incentive programs (e.g., Konsulta reimbursement)

That gives everyone reason to participate *without punishing early.*

------

### 8. Target one visible success within 12 months

Pick a single domain (e.g., **PhilHealth claims or disease surveillance**) and show real-time NHDR integration working across multiple facilities.
Once you have data flowing and dashboards running, the rest of government will fall in line.

------

### Bottom Line

- **Don’t enforce a protocol. Enforce a data model.**
- **Start hybrid. Normalize centrally. Use FHIR internally.**
- **Make progress visible fast.**

This is the only path that balances political optics, technical feasibility, and national adoption.