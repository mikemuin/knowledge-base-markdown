# Claude - Comprehensive Assessment: NHDR Framework Workshop Presentation

## Executive Summary

This presentation represents a critical strategic initiative to redesign the Philippine National Health Data Repository (NHDR) framework. The workshop aims to address significant operational challenges and align the NHDR with Universal Health Care (UHC) Act mandates. While the presentation demonstrates strong policy grounding and technical awareness, it reveals concerning gaps in implementation clarity, stakeholder engagement strategy, and practical execution planning.

------

## Section-by-Section Analysis

### 1. Opening and Objectives (Slides 1-4)

**Strengths:**

- Clear articulation of workshop purpose
- Appropriate stakeholder identification (DOH, PhilHealth, DOH)
- Well-defined objectives focused on framework review, gap identification, redesign, and roadmap updates

**Weaknesses:**

- No baseline metrics presented to measure current NHDR performance
- Missing context on why NOW is the critical time for this workshop
- No explicit statement of success criteria for the workshop outcomes

**Assessment:** **6/10** - Adequate setup but lacks urgency framing and measurable targets.

------

### 2. NHDR Key Insights, Pain Points and Challenges (Slides 5-29)

**Strengths:**

- Comprehensive review of UHC legal mandates (Sections 31, 36, 17.3b, 18.4c/d, 20.6, 28.16, 28.23.b, 31.8, 32.3, 38.20)
- Clear identification of NHDR as "single point-of-submission, single source of truth"
- Excellent documentation of enforcement mechanisms (licensing requirements, penalties)
- Detailed stakeholder mapping across 15+ entity types

**Weaknesses:**

- **CRITICAL FLAW**: Pain points presented are vague and lack quantitative impact analysis
  - "Data collection based on separate requirements" - How many separate systems? What's the duplication cost?
  - "Manual submission process" - What percentage? What's the error rate?
  - "Mismatched data between DOH and PhilHealth" - What's the discrepancy rate? Financial impact?
- **No root cause analysis** - Lists symptoms without diagnosing underlying problems
- **Missing stakeholder perspective** - No evidence of actual facility interviews or surveys
- **Governance gaps** poorly articulated - Political intervention and change management mentioned but not contextualized within Philippine bureaucratic realities

**Assessment:** **5/10** - Strong on policy foundation, critically weak on empirical problem definition. This creates risk that proposed solutions won't address actual operational pain points.

------

### 3. DOH Digital Health Initiatives (Slides 31-50)

**Strengths:**

- Excellent presentation of Digital Health Strategy 2025-2028 alignment
- Clear governance structure (COBIT 2019 framework)
- Strong documentation of enabling policies (JAO 2021-0001, 2021-0002)
- Impressive SCIV (Standards Conformance and Interoperability Validation) program structure
- Evidence of actual implementation progress (EMR validation, FHIR implementation guides)

**Weaknesses:**

- **Governance structure appears bureaucratically heavy** - Multiple layers (Steering Committee → TWG → 4 Expert Groups) risk slow decision-making

- **No metrics on SCIV adoption rates** - How many EMR vendors have passed? What's the adoption timeline?

- **Unclear integration** between separate initiatives:

  - DOH Digitalization 9.0
  - HRH Workforce 8.0
  - EMR Implementation & RISE
  - NHDR
  - NHIE
  - Digital Vaccinations
  - Healthcare Financing System

**CRITICAL QUESTION**: Are these truly integrated programs or siloed projects with coordination meetings?

- **FHIR Implementation specifics lacking** - Which FHIR resources? What profiles? Maturity level?

**Assessment:** **7/10** - Strong strategic framework with concerning execution opacity. The digi-H matrix shows planning maturity but lacks evidence of cross-program integration.

------

### 4. Framework Alternatives Presented (Slides 54-59)

This is the **most consequential section** of the entire presentation.

#### Framework 1: Centralized NHDR

**Design:**

- All data flows through single NHDR interoperability layer
- NHDR contains staging, repository, data fabric
- Component services (Claims, Yakap, Disease Surveillance, FHSIS, Others) sit within NHDR
- Single security/validation layer at ingestion

**Pros Analysis - ACCURATE:**

- International standard compliance
- Single point of submission
- Single source of truth
- Unified security requirements

**Cons Analysis - ACCURATE BUT UNDERSTATED:**

- Single point of failure **(CRITICAL RISK - should be in red)**
- Transmission bottleneck risk
- Delays for new data mandates
- High dependency
- Heavy operational management

**MISSING CONS:**

- **Vendor lock-in risk** - Centralized system likely requires large monolithic contract
- **Scalability concerns** - Philippine healthcare system has 20,000+ facilities
- **Political risk** - Single system becomes political target; failure is catastrophic and visible
- **Change management nightmare** - Requires simultaneous coordination across all facilities

**Honest Assessment:** This is the "theoretically perfect but practically nightmarish" option. It's what a consultant would recommend in a report but an implementer would fear deploying.

------

#### Framework 2: Parallel Systems (DOH + PhilHealth) + NHDR

**Design:**

- Health facilities submit to both DOH and PhilHealth systems separately
- PHCDI-compliant data flows to NHDR
- NHDR exists as separate repository alongside operational systems

**Pros Analysis - ACCURATE:**

- No NHDR dependency
- No changes to existing agency systems
- Faster deployment for new mandates
- Different security requirements possible

**Cons Analysis - ACCURATE:**

- Multiple submission requirements for facilities **(This contradicts UHC Act Section 31 mandate)**

**MISSING ANALYSIS:**

- **This defeats the entire purpose of NHDR** - UHC Act specifically mandates single submission point
- **Perpetuates the "hairball architecture"** shown in their own slides
- **No governance model** for data reconciliation between systems

**Honest Assessment:** This is the "status quo with a repository bolted on" option. It's operationally easier but legally non-compliant and strategically incoherent.

------

#### Framework 3: Interoperability Layer (Federated Model)

**Design:**

- Health Interoperability Layer (HIL) sits between facilities and backend systems
- HIL provides:
  - ESB/API Gateway (ingestion)
  - Security Layer (authentication)
  - API Layer (routing)
  - Validation Layer
- Integration passthrough to DOH systems, PhilHealth systems, and NHDR
- FHIR Implementation Guides define standard interfaces

**Pros Analysis - ACCURATE AND COMPELLING:**

- No NHDR dependency
- International standards compliance
- Single point of submission
- Unified security for facilities
- Agency-specific security possible
- Queueing/async processing
- Faster new mandate deployment
- Low bottleneck risk
- Agencies manage their own APIs

**Cons Analysis - ACCURATE BUT MANAGEABLE:**

- Requires agency API registration (but this is governance, not tech burden)
- Requires facility adoption (but this is true for any change)
- Requires agency standard adoption (but JAO 2021-0002 already mandates this)

**MISSING PROS:**

- **Enables phased rollout** - Can onboard agencies/programs incrementally
- **Supports existing investments** - Agencies can keep current systems
- **Market-based improvement** - Agencies compete to provide better APIs
- **Disaster resilience** - Failure of one backend doesn't collapse entire system

**Honest Assessment:** This is the **architecturally sound, politically pragmatic, and implementation-feasible option**. It's what successful health information exchanges (e.g., CommonWell, Carequality in US; NHS Digital in UK) have converged toward.

------

### 5. Conceptual Architecture Deep Dive (Slides 60-68)

**Strengths:**

- **Excellent use of reference architecture** (Data Management Body of Knowledge) - shows professional maturity
- **Logical layer decomposition** is well thought through:
  - External Source Systems
  - Health Interoperability Layer (ESB, Security, Validation, Routing)
  - Target Operational Systems (DOH, PhilHealth)
  - NHDR (Cleanse, Aggregate, De-identify)
  - External Authoritative Sources (Terminology servers, NHFR, PSA, PSGC)
- **Crucially includes de-identification pipeline** for public data - this is essential for research/transparency

**Weaknesses:**

- **No technology stack specified** - Is HIL using Mirth? InterSystems? Custom? Open-source?
- **No performance requirements** - What's the expected throughput? Latency SLAs?
- **FHIR IG scope unclear** - The diagram shows "FHIR IG" on every connection but doesn't specify:
  - Which FHIR version (R4? R5?)
  - Which resources (Patient, Encounter, Condition, Observation, etc.)
  - Maturity level (STU? Normative?)
- **No discussion of master patient index (MPI)** - How are patient identities resolved across systems?
  - Is PhilHealth ID the primary identifier?
  - How are duplicates handled?
  - What about patients without PhilHealth ID?
- **No mention of consent management** - How is patient consent tracked for data sharing?

**Assessment:** **7.5/10** - Strong conceptual design, but needs technical specification depth for actual procurement/build.

------

### 6. Roadmap and Action Planning (Slides 76-83)

**Strengths:**

- Acknowledges current timeline (originally Year 3 of 10-year plan)
- Template structure for action planning is appropriate

**CRITICAL WEAKNESSES:**

- **Action plan table is EMPTY** - This is unacceptable for a "roadmap and action planning" session
  - No responsible parties assigned
  - No timelines
  - No dependencies identified
  - No resource requirements
- **No prioritization framework** - Should Framework 3 be implemented first? Which FHIR IGs first? Which agencies onboard first?
- **No risk mitigation strategies** - What if PhilHealth management doesn't approve? What if EMR vendors resist? What if funding is cut?
- **No change management plan** - 20,000+ facilities need to transition; how?
- **No stakeholder engagement beyond this workshop** - Where are the facility representatives? EMR vendors? LGU health officers?

**Assessment:** **2/10** - This section is a placeholder, not a roadmap. It's like presenting a car design without an engine.

------

## What They Got RIGHT

1. **Legal/Policy Foundation is Rock Solid**
   - Comprehensive citation of UHC Act mandates
   - Clear understanding of DOH-PhilHealth joint responsibilities
   - Proper documentation of enforcement mechanisms
2. **Framework 3 (Interoperability Layer) is the Correct Technical Choice**
   - Aligns with global best practices
   - Balances centralization (standards) with decentralization (agency autonomy)
   - Enables phased implementation
   - Reduces single point of failure risk
3. **Use of International Standards (FHIR, DMABOK)**
   - Shows maturity beyond "let's build custom everything"
   - Enables interoperability with future systems
   - Reduces long-term technical debt
4. **Recognition of Data Governance Importance**
   - Separate Expert Group for Health Enterprise Architecture and Data Governance
   - Emphasis on master/reference data
   - De-identification pipeline for open data
5. **SCIV Program is a Game-Changer**
   - Tying EMR validation to facility licensing creates real leverage
   - Regional support model is appropriate for Philippine context
   - Connectathon approach is industry best practice

------

## What They Got WRONG

1. **Problem Definition is Inadequate**
   - Pain points are qualitative, not quantitative
   - No baseline metrics (e.g., "facilities spend X hours/week on duplicate reporting")
   - No stakeholder interviews or surveys presented
   - **Impact:** Risk building the wrong solution to assumed problems
2. **Framework Comparison Lacks Honest Trade-off Analysis**
   - Framework 1 cons are understated (should emphasize catastrophic failure risk)
   - Framework 2 cons miss legal non-compliance
   - No cost comparison between frameworks
   - **Impact:** Decision-makers may choose based on incomplete information
3. **Roadmap Section is Unfinished**
   - Empty action planning table is inexcusable
   - No resource requirements (people, budget, infrastructure)
   - No timeline with milestones
   - **Impact:** Workshop will end without actionable next steps
4. **Stakeholder Engagement is Top-Down**
   - Presentation is to agency leadership, not WITH facility operators
   - No evidence of EMR vendor consultation
   - LGU perspectives missing (they operate most facilities)
   - **Impact:** Implementation will face resistance from those expected to use the system
5. **Governance Structure May Be Too Bureaucratic**
   - 4-layer hierarchy (Steering → TWG → 4 Expert Groups) risks slow decisions
   - No clear escalation paths for urgent issues
   - **Impact:** System may be out-maneuvered by faster-moving private health sector
6. **No Discussion of Funding and Sustainability**
   - Who pays for HIL infrastructure? NHDR storage? Ongoing operations?
   - Is this DOH budget? PhilHealth? PPP?
   - **Impact:** Project may stall in procurement/budget allocation

------

## What Needs IMPROVEMENT

### Immediate (Before Workshop Ends)

1. **Complete the Action Planning Table**
   - Assign responsible agencies for each topic
   - Set realistic timelines (Q4 2025, Q1 2026, etc.)
   - Identify dependencies (e.g., "Policy update must precede procurement")
2. **Add Quantitative Pain Point Analysis**
   - Survey results from facilities on reporting burden
   - Data quality metrics (discrepancy rates between DOH/PhilHealth)
   - Cost estimates of current inefficiencies
3. **Conduct Framework Decision Exercise**
   - Weighted scoring model for the three frameworks
   - Criteria: Legal compliance, cost, implementation timeline, failure risk, scalability
   - Force a recommendation (likely Framework 3)

### Short-term (Next 3 Months)

1. **Develop Detailed FHIR Implementation Guides**
   - Start with one high-value use case (e.g., Claims submission or Disease surveillance)
   - Specify exact FHIR resources, profiles, terminologies
   - Publish draft IGs for stakeholder comment
2. **Establish Master Patient Index (MPI) Strategy**
   - Define identity matching algorithms
   - Clarify role of PhilHealth ID vs other identifiers
   - Plan for de-duplication and record linkage
3. **Create Pilot Program for HIL**
   - Select 3-5 facilities (mix of public/private, urban/rural)
   - Select 1-2 data flows (e.g., Claims + Immunizations)
   - Run 6-month pilot with success metrics
4. **Engage EMR Vendors Formally**
   - Workshop to present HIL approach
   - Gather feedback on implementation burden
   - Build coalition of vendors willing to be early adopters

### Medium-term (Next 6-12 Months)

1. **Develop Comprehensive Change Management Plan**
   - Training programs for facility staff
   - Regional support structure (leverage existing CHDs)
   - Communication strategy (explainer videos, FAQs, help desk)
2. **Secure Funding and Procurement Strategy**
   - Cost model for HIL infrastructure (likely Php 500M - 1B for national scale)
   - Identify funding source (GAA? PhilHealth reserves? Donor funding?)
   - Procurement roadmap (may need separate lots for HIL platform, NHDR storage, services)
3. **Establish Clear Governance Operating Model**
   - Decision-making authority matrix
   - Escalation protocols
   - Performance monitoring dashboard
   - Quarterly steering committee reviews with published minutes

------

## Bottom Line: No Bullshit Assessment

### What This Presentation Represents:

This is a **serious, well-intentioned effort** by technically competent people who understand healthcare IT standards and the Philippine policy landscape. They've correctly diagnosed that the current state is a mess and have identified the right architectural pattern (federated interoperability layer) as the solution.

### What This Presentation LACKS:

1. **Operational Realism** - It reads like a consultant's deck, not an implementer's plan. Missing are the gritty details: Who codes the HIL? What happens when a facility can't connect? How do we handle the 5,000 small RHUs with intermittent internet?
2. **Stakeholder Buy-In Evidence** - This is a DOH-PhilHealth conversation. Where are the voices of:
   - Facility IT staff who will do the work?
   - EMR vendors who must build the interfaces?
   - LGU health officers who control the facilities?
   - Patients whose data this is ultimately about?
3. **Political Economy Analysis** - Philippine health IT is littered with failed "national systems." Why will this be different? The presentation doesn't address:
   - How to sustain momentum across administration changes
   - How to prevent scope creep and feature bloat
   - How to manage competing interests (e.g., PhilHealth wants claims data; DOH wants surveillance data; researchers want de-identified data)

### Will This Succeed?

**Conditional YES - IF:**

1. They choose Framework 3 (federated HIL model)
2. They start with a narrow pilot (1-2 data flows, 5-10 facilities)
3. They secure sustained leadership commitment and funding
4. They build a coalition of EMR vendors as partners, not adversaries
5. They accept that "done" means 80% of facilities by 2028, not 100%

**Likely NO - IF:**

1. They try to build Framework 1 (centralized monolith)
2. They proceed to full procurement without pilots
3. Leadership changes and new administration deprioritizes this
4. They underestimate change management and expect facilities to "just comply"
5. They don't secure multi-year funding commitment

------

## Final Recommendation to Workshop Participants

**Do NOT let this workshop end without:**

1. A completed action plan with names and dates
2. A decision on which framework to pursue (recommend Framework 3)
3. Commitment to a pilot program with specific facilities and timelines
4. A follow-up meeting in 60 days to review progress

**The biggest risk is not technical failure—it's drift into endless planning.** The Philippines has had "digital health strategies" for 15+ years. Execution is the constraint, not strategy.

This workshop should be the **last strategy session and the first implementation planning session.** If the next meeting is another PowerPoint deck, this initiative is doomed.

------

## Rating: 6.5/10

**Strong foundation, critical execution gaps. Has the potential to succeed if leaders act decisively in next 90 days. Without urgent follow-through, will become another well-documented initiative that never ships.**

---

# Action Plan Reconstruction: From Bureaucratic Template to Executable Roadmap

## Current State Assessment: What's Wrong with the Existing Action Plan

The presentation shows **THREE virtually identical tables** with these columns:

- TOPIC
- AGREEMENTS
- RESPONSIBLE AGENCY
- TIMELINE

**Problems:**

1. **All cells are empty** - This is a template, not a plan
2. **No prioritization** - Everything seems equally important
3. **No dependencies** - What must happen before what?
4. **No resources** - Who does the work? What does it cost?
5. **No success metrics** - How do we know it's done?
6. **Topics are vague** - "Implementation of Philippine Core Data" - what does that mean operationally?

------

## Proposed Action Plan Framework: Three-Tier Structure

### TIER 1: CRITICAL PATH (Must Happen for Anything Else to Work)

These are **blocking dependencies**. Nothing moves without these.

### TIER 2: ENABLING WORKSTREAMS (Can Proceed in Parallel)

These support implementation but aren't strictly sequential.

### TIER 3: CONTINUOUS/ONGOING (No End Date)

These are operational functions that persist beyond launch.

------

# TIER 1: CRITICAL PATH ACTION PLAN

## CP-1: FRAMEWORK DECISION & GOVERNANCE CHARTER

| Element              | Details                                                      |
| -------------------- | ------------------------------------------------------------ |
| **ACTION**           | Steering Committee formally selects Framework 3 (Interoperability Layer) and approves 18-month phased implementation approach |
| **DELIVERABLE**      | Signed resolution with:<br>• Framework choice rationale<br>• Authority to proceed to procurement<br>• Budget allocation commitment<br>• Governance operating model |
| **RESPONSIBLE**      | • **Decision:** Digital Health Steering Committee (Chair: Usec Domingo)<br>• **Documentation:** KMITS eHealth Systems Division<br>• **Legal review:** DOH Legal, PhilHealth Legal |
| **TIMELINE**         | **By December 15, 2025** (4 weeks from workshop)             |
| **DEPENDENCIES**     | None - this unblocks everything                              |
| **BUDGET**           | Minimal (staff time)                                         |
| **SUCCESS CRITERIA** | Signed resolution posted to DOH website; briefing materials distributed to Congress health committees |
| **RISKS**            | • Steering Committee wants more studies (MITIGATION: Set decision deadline in workshop)<br>• PhilHealth management disagrees with Framework 3 (MITIGATION: Pre-brief SVP Untalan before Steering meeting) |

------

## CP-2: PILOT PROGRAM DESIGN & SITE SELECTION

| Element              | Details                                                      |
| -------------------- | ------------------------------------------------------------ |
| **ACTION**           | Design 6-month pilot to test HIL with real facilities and data flows |
| **DELIVERABLE**      | Pilot Program Charter including:<br>• 3-5 pilot facilities selected (criteria: mix of public/private, EMR vendors, connectivity levels, geographic diversity)<br>• 2 initial data flows defined (recommend: PhilHealth Claims + Immunization data to EPI)<br>• Success metrics defined<br>• Pilot budget and resource plan |
| **RESPONSIBLE**      | • **Lead:** KMITS Standards & Interoperability EG + PhilHealth EA-NHDR<br>• **Site Selection:** DOH-CHD Offices (recommend NCR + Region 7 for geographic diversity)<br>• **Vendor Coordination:** PhilHealth SCIV Team |
| **TIMELINE**         | **By January 15, 2026** (8 weeks from workshop)              |
| **DEPENDENCIES**     | CP-1 (Framework decision)                                    |
| **BUDGET**           | Php 15-20M for pilot:<br>• Php 8M HIL infrastructure (dev/test environment)<br>• Php 5M facility support (connectivity upgrades, training)<br>• Php 3M technical assistance (integration support)<br>• Php 2M monitoring & evaluation |
| **SUCCESS CRITERIA** | • 5 facilities signed up with executive commitment<br>• At least 2 different EMR vendors participating<br>• FHIR Implementation Guides drafted for 2 data flows<br>• Pilot timeline with weekly milestones |
| **RISKS**            | • Facilities decline to participate (MITIGATION: Offer incentive - free connectivity upgrade or EMR support)<br>• EMR vendors refuse to develop interfaces (MITIGATION: Engage vendors early; offer technical assistance) |

------

## CP-3: HIL PLATFORM PROCUREMENT

| Element              | Details                                                      |
| -------------------- | ------------------------------------------------------------ |
| **ACTION**           | Procure or deploy Health Interoperability Layer platform (ESB/API Gateway) |
| **DELIVERABLE**      | Operational HIL environment with:<br>• ESB/API Gateway (Mirth Connect, InterSystems IRIS, or similar)<br>• Security layer (OAuth2/SMART on FHIR)<br>• FHIR validation engine<br>• Monitoring/logging infrastructure<br>• Dev, test, and production environments |
| **RESPONSIBLE**      | • **Procurement Lead:** PhilHealth IMS (has procurement capacity)<br>• **Technical Specs:** KMITS + PhilHealth ITMD + UP Manila NTC<br>• **Deployment:** TBD vendor or in-house team |
| **TIMELINE**         | • **TOR Completion:** January 31, 2026<br>• **Procurement:** February-April 2026<br>• **Deployment:** May 2026<br>• **Pilot Ready:** June 2026 (6 months from framework decision) |
| **DEPENDENCIES**     | CP-1 (Framework decision and budget approval)                |
| **BUDGET**           | **Build vs. Buy Decision:**<br><br>**Option A (Open Source):** Php 30-50M<br>• Mirth Connect (free) + infrastructure + customization<br>• Requires strong in-house technical team<br>• Higher implementation risk, lower ongoing cost<br><br>**Option B (Commercial):** Php 80-150M<br>• InterSystems IRIS for Health or similar<br>• Vendor provides implementation & support<br>• Lower implementation risk, higher ongoing cost<br><br>**Recommendation:** Option A for pilot, Option B evaluation for scale |
| **SUCCESS CRITERIA** | • Platform deployed and accessible via DICT gov cloud<br>• Can ingest FHIR resources (Patient, Encounter, Immunization, Claim)<br>• Can route to DOH/PhilHealth test endpoints<br>• Passes security audit (penetration test) |
| **RISKS**            | • Procurement delays (MITIGATION: Start TOR now; consider emergency procurement authority)<br>• Platform doesn't scale (MITIGATION: Architecture review by external experts)<br>• Vendor lock-in (MITIGATION: Require open standards; include exit clauses) |

------

## CP-4: FHIR IMPLEMENTATION GUIDES (Version 1.0)

| Element              | Details                                                      |
| -------------------- | ------------------------------------------------------------ |
| **ACTION**           | Develop and publish FHIR IGs for pilot data flows            |
| **DELIVERABLE**      | Published FHIR IGs (R4) covering:<br><br>**IG #1: PhilHealth Claims Submission**<br>• Claim resource profile<br>• Patient, Practitioner, Organization resources<br>• Required terminologies (ICD-10-PH, PHIC case rates)<br>• Validation rules<br><br>**IG #2: Immunization Reporting**<br>• Immunization resource profile<br>• VaccineCode value set (DOH vaccine list)<br>• Administration workflow<br><br>• Example payloads for testing<br>• Technical conformance criteria<br>• Published on DOH FHIR server (e.g., fhir.doh.gov.ph) |
| **RESPONSIBLE**      | • **Lead Authors:** UP Manila NTC Standards Lab + PhilHealth EA-NHDR<br>• **Clinical Review:** DOH Disease Prevention & Control Bureau (immunization); PhilHealth Claims (case rates)<br>• **Technical Review:** Standards & Interoperability EG<br>• **Publication:** KMITS |
| **TIMELINE**         | • **Draft v0.1:** January 31, 2026<br>• **Stakeholder review:** February 2026<br>• **Final v1.0:** March 31, 2026 |
| **DEPENDENCIES**     | CP-2 (Pilot data flows defined)                              |
| **BUDGET**           | Php 5-8M:<br>• UP Manila NTC contract (Php 4M for IG development)<br>• Connectathon event (Php 2M for vendor testing)<br>• FHIR server hosting (Php 500K/year) |
| **SUCCESS CRITERIA** | • IGs pass HL7 FHIR validation<br>• At least 2 EMR vendors successfully submit test data<br>• DOH/PhilHealth confirm data meets operational needs<br>• Published to international FHIR registry |
| **RISKS**            | • IGs too complex for vendors to implement (MITIGATION: Start simple; iterate based on vendor feedback)<br>• Stakeholders can't agree on data elements (MITIGATION: Standards EG has authority to break ties) |

------

## CP-5: MASTER PATIENT INDEX (MPI) STRATEGY

| Element              | Details                                                      |
| -------------------- | ------------------------------------------------------------ |
| **ACTION**           | Define and deploy patient identity matching strategy         |
| **DELIVERABLE**      | MPI Operational Framework:<br>• PhilHealth ID as primary identifier (PHN)<br>• Probabilistic matching algorithm for patients without PHN<br>• Match thresholds and manual review process<br>• Integration with PSA birth registry (future)<br>• Golden record management rules |
| **RESPONSIBLE**      | • **Lead:** PhilHealth EA-NHDR (owns PHN registry)<br>• **Algorithm:** UP Manila NTC (matching logic)<br>• **Policy:** Health Enterprise Architecture & Data Governance EG |
| **TIMELINE**         | • **Strategy Document:** February 2026<br>• **Algorithm Testing:** March-April 2026<br>• **Deployment:** May 2026 (before pilot) |
| **DEPENDENCIES**     | CP-3 (HIL platform must support MPI function)                |
| **BUDGET**           | Php 10-15M:<br>• MPI software (open source OpenEMPI ~Php 5M to customize)<br>• Integration with PhilHealth PHN registry<br>• Testing with de-identified patient data |
| **SUCCESS CRITERIA** | • Can match 90%+ of patients with existing PhilHealth records<br>• False positive rate <2%<br>• Average match time <500ms |
| **RISKS**            | • PhilHealth PHN data quality issues (MITIGATION: Data cleansing effort in parallel)<br>• Privacy concerns about centralized identity (MITIGATION: Tokenization; patient IDs never stored in clear text) |

------

## CP-6: PILOT EXECUTION

| Element              | Details                                                      |
| -------------------- | ------------------------------------------------------------ |
| **ACTION**           | Run 6-month pilot at 5 facilities with 2 data flows          |
| **DELIVERABLE**      | Pilot Results Report including:<br>• Quantitative metrics (transaction volume, success rate, latency)<br>• Qualitative feedback (facility staff interviews, vendor feedback)<br>• Issues log and resolutions<br>• Recommendations for national scale-up |
| **RESPONSIBLE**      | • **Program Management:** KMITS eHealth Systems Division<br>• **Technical Support:** PhilHealth SCIV Regional Teams + UP Manila NTC<br>• **M&E:** External evaluator (recommend hiring independent firm) |
| **TIMELINE**         | • **Launch:** June 2026<br>• **Monthly Reviews:** July-November 2026<br>• **Final Report:** December 2026 |
| **DEPENDENCIES**     | All previous CP items                                        |
| **BUDGET**           | Included in CP-2 (Php 15-20M)                                |
| **SUCCESS CRITERIA** | **QUANTITATIVE:**<br>• 80% of claims from pilot facilities submitted via HIL<br>• 95% of immunization records submitted via HIL<br>• <5% data validation error rate<br>• Average end-to-end latency <30 seconds<br><br>**QUALITATIVE:**<br>• Facility staff report time savings<br>• No data breaches or privacy incidents<br>• EMR vendors willing to continue to national scale-up |
| **RISKS**            | • Facilities revert to manual submission (MITIGATION: Daily monitoring; rapid support response)<br>• Technical issues block pilot (MITIGATION: 24/7 support hotline; vendor on standby) |

------

## CP-7: SCALE-UP DECISION & PLANNING

| Element              | Details                                                      |
| -------------------- | ------------------------------------------------------------ |
| **ACTION**           | Based on pilot results, Steering Committee decides whether to proceed to national scale-up |
| **DELIVERABLE**      | National Scale-Up Plan (if pilot succeeds) covering:<br>• Phased rollout (Phases 1-3, by facility type and region)<br>• Additional data flows (expand beyond claims and immunization)<br>• Infrastructure scaling (HIL capacity planning)<br>• Change management strategy<br>• 3-year budget and resource plan<br>• Risk mitigation strategies |
| **RESPONSIBLE**      | • **Decision:** Digital Health Steering Committee<br>• **Planning:** KMITS + PhilHealth EA-NHDR + External consultant |
| **TIMELINE**         | **Decision Meeting: January 2027** (after pilot final report) |
| **DEPENDENCIES**     | CP-6 (Pilot results)                                         |
| **BUDGET**           | Php 5M for scale-up planning study                           |
| **SUCCESS CRITERIA** | • Clear go/no-go decision documented<br>• If "go": Approved 3-year national scale-up plan with budget<br>• If "no-go": Documented lessons learned and alternative approach |
| **RISKS**            | • Pilot shows technical success but facilities refuse to scale (MITIGATION: Address change management issues before scale)<br>• Budget not approved for scale-up (MITIGATION: Engage DBM and Congress early) |

------

# TIER 2: ENABLING WORKSTREAMS (Parallel Tracks)

These can start early and run alongside Tier 1.

## EW-1: POLICY & REGULATORY UPDATES

| Element              | Details                                                      |
| -------------------- | ------------------------------------------------------------ |
| **ACTION**           | Update/create enabling policies for HIL and NHDR             |
| **DELIVERABLE**      | **Updated/New Issuances:**<br>1. **DOH-PhilHealth-DICT JAO on HIL Architecture** (updates JAO 2021-0001)<br>2. **Data Sharing Agreement Template** (for agencies connecting to HIL)<br>3. **Privacy Impact Assessment & Data Protection Guidelines** (updated for centralized routing)<br>4. **NHDR Data Access Policy** (who can access what data, for what purposes)<br>5. **Amendment to SCIV Program** (add HIL compliance to EMR validation) |
| **RESPONSIBLE**      | • **Lead:** Cybersecurity & Health Data Privacy EG<br>• **Legal Drafting:** DOH Legal, PhilHealth Legal, DICT Legal<br>• **Stakeholder Consultation:** NPC (National Privacy Commission), CSOs |
| **TIMELINE**         | • **Drafts:** February 2026<br>• **Consultation:** March 2026<br>• **Signing:** April 2026 (before pilot launch) |
| **DEPENDENCIES**     | CP-1 (Framework decision)                                    |
| **BUDGET**           | Php 2M (legal consultations, stakeholder workshops)          |
| **SUCCESS CRITERIA** | • All 5 issuances signed and published<br>• NPC no-objection for privacy approach<br>• No legal blockers to pilot |

------

## EW-2: PHILIPPINE CORE DATA IMPLEMENTATION (PH Core)

| Element              | Details                                                      |
| -------------------- | ------------------------------------------------------------ |
| **ACTION**           | Operationalize PH Core standards (master/reference data)     |
| **DELIVERABLE**      | **Deployed Reference Data Services:**<br>• **Terminology Server** (SNOMED CT, LOINC, ICD-10-PH, RxNorm)<br>• **Facility Registry** (DOH-NHFR API)<br>• **Health Worker Registry** (PRC-Licensed, DOH-Accredited)<br>• **Patient Registry** (PhilHealth PHN)<br>• **Geographic Codes** (PSA PSGC)<br><br>• APIs published with documentation<br>• SLA commitments (uptime, response time) |
| **RESPONSIBLE**      | • **Lead:** Standards & Interoperability EG<br>• **Facility Registry:** HFSRB (maintains NHFR)<br>• **Health Worker Registry:** HHRDB + PRC coordination<br>• **Patient Registry:** PhilHealth IMS<br>• **Terminology:** UP Manila NTC (hosts terminology server) |
| **TIMELINE**         | • **Terminology Server:** February 2026<br>• **Facility Registry API:** March 2026<br>• **Health Worker Registry API:** June 2026 (more complex)<br>• **Full integration:** Before national scale-up (2027) |
| **DEPENDENCIES**     | CP-3 (HIL can call these services)                           |
| **BUDGET**           | Php 20-30M:<br>• Terminology server (Php 10M - license + hosting)<br>• Registry API development (Php 15M)<br>• Data cleansing (Php 5M - significant effort) |
| **SUCCESS CRITERIA** | • All registries accessible via REST APIs<br>• 99% uptime SLA<br>• Data quality >95% (validated records) |

------

## EW-3: CAPACITY BUILDING PROGRAM

| Element              | Details                                                      |
| -------------------- | ------------------------------------------------------------ |
| **ACTION**           | Train DOH, PhilHealth, and facility staff on HIL and FHIR    |
| **DELIVERABLE**      | **Training Program:**<br>• **Level 1 (Awareness):** 500 facility IT staff, 100 DOH/PhilHealth staff<br>  - Webinar series on NHDR vision and HIL approach<br>• **Level 2 (Technical):** 50 technical staff (DOH, PhilHealth, vendors)<br>  - 3-day FHIR training (HL7 certified trainers)<br>  - 2-day HIL administration training<br>• **Level 3 (Advanced):** 20 core team members<br>  - FHIR IG authoring<br>  - Interoperability architecture<br><br>• Training materials (videos, guides, FAQs)<br>• Helpdesk setup (email, phone, ticketing system) |
| **RESPONSIBLE**      | • **Lead:** Digital Health Capacity Building EG<br>• **Training Delivery:** UP Manila NTC + HL7 Philippines Chapter + Vendor partners<br>• **Helpdesk:** KMITS eHealth Systems Division |
| **TIMELINE**         | • **Level 1 Webinars:** January-March 2026<br>• **Level 2 Training:** April-May 2026 (before pilot)<br>• **Level 3 Training:** Ongoing (quarterly cohorts)<br>• **Helpdesk Launch:** June 2026 (with pilot) |
| **DEPENDENCIES**     | CP-4 (FHIR IGs published for training content)               |
| **BUDGET**           | Php 12-15M:<br>• Training programs (Php 8M)<br>• Materials development (Php 2M)<br>• Helpdesk setup and 1-year operation (Php 5M) |
| **SUCCESS CRITERIA** | • 80% of trained staff pass assessment<br>• Helpdesk resolves 70% of tickets within 48 hours<br>• Satisfaction rating >4/5 |

------

## EW-4: ADVOCACY & STAKEHOLDER ENGAGEMENT

| Element              | Details                                                      |
| -------------------- | ------------------------------------------------------------ |
| **ACTION**           | Build coalition of support among facilities, vendors, LGUs, and advocates |
| **DELIVERABLE**      | **Engagement Activities:**<br>• **EMR Vendor Consortium:** Quarterly meetings with vendors to gather feedback and co-design solutions<br>• **LGU Roadshow:** Regional briefings for provincial/city health officers (prioritize pilot regions)<br>• **Hospital Associations:** Present at PPHA, PCAHO, PHAP conferences<br>• **Patient Advocacy:** Engage with patient groups on data privacy and access rights<br>• **Media Campaign:** Op-eds, press releases, social media on NHDR benefits<br><br>• Communication toolkit (brochures, infographics, videos) |
| **RESPONSIBLE**      | • **Lead:** KMITS eHealth Systems Division + PhilHealth Corporate Affairs<br>• **Vendor Relations:** Standards & Interoperability EG<br>• **LGU Relations:** DOH-CHD Offices<br>• **Patient Advocacy:** Health Promotion Bureau + CSO partners |
| **TIMELINE**         | • **Vendor Consortium Kickoff:** January 2026<br>• **LGU Roadshow:** February-April 2026<br>• **Conference Presentations:** Ongoing<br>• **Media Campaign:** May 2026 (before pilot launch) |
| **DEPENDENCIES**     | CP-1 (Framework decision to communicate)                     |
| **BUDGET**           | Php 8-10M:<br>• Events and conferences (Php 5M)<br>• Communication materials (Php 3M)<br>• Media outreach (Php 2M) |
| **SUCCESS CRITERIA** | • 30+ EMR vendors engaged<br>• 50+ LGU health officers briefed<br>• Positive media coverage (5+ major news outlets)<br>• No organized opposition campaigns |

------

## EW-5: FUNDING & SUSTAINABILITY PLANNING

| Element              | Details                                                      |
| -------------------- | ------------------------------------------------------------ |
| **ACTION**           | Secure multi-year funding commitment for NHDR operations     |
| **DELIVERABLE**      | **Funding Strategy Document:**<br>• 5-year total cost of ownership (TCO) model<br>• Funding source identification:<br>  - DOH GAA allocation<br>  - PhilHealth administrative budget<br>  - Potential donor support (WHO, USAID, etc.)<br>  - PPP opportunities (if appropriate)<br>• Sustainability model (beyond donor support)<br>• Budget proposal to DBM<br>• Congressional briefing materials |
| **RESPONSIBLE**      | • **Lead:** Health Enterprise Architecture & Data Governance EG<br>• **Budget Preparation:** DOH Finance, PhilHealth Finance<br>• **Donor Coordination:** DOH IHFAD<br>• **Congressional Relations:** DOH Executive Office, PhilHealth OIC-President |
| **TIMELINE**         | • **TCO Model:** February 2026<br>• **Funding Strategy:** March 2026<br>• **DBM/Congress Briefings:** April-May 2026 (in time for 2027 budget cycle) |
| **DEPENDENCIES**     | CP-2 (Pilot budget informs scale-up costs)                   |
| **BUDGET**           | Php 3M (consultancy for TCO modeling)                        |
| **SUCCESS CRITERIA** | • 2027 budget includes NHDR operational line item<br>• Multi-year funding commitment (3-5 years)<br>• At least 50% of funding from government sources (not dependent on donors) |

------

# TIER 3: CONTINUOUS/ONGOING (No End Date)

## ON-1: DIGITAL HEALTH GOVERNANCE OPERATIONS

| Element         | Details                                                      |
| --------------- | ------------------------------------------------------------ |
| **ACTION**      | Operate Digital Health Steering Committee and Expert Groups per DC 2024-0486 |
| **ACTIVITIES**  | • **Steering Committee:** Quarterly meetings to review progress, make decisions<br>• **Technical Working Group:** Monthly coordination across DOH-PhilHealth-DICT<br>• **Expert Groups:** Bi-weekly standups; deliverables per workstream<br>• **Secretariat Function:** KMITS maintains meeting minutes, tracks action items, publishes transparency reports |
| **RESPONSIBLE** | Per DC 2024-0486                                             |
| **TIMELINE**    | Ongoing (already in place)                                   |
| **BUDGET**      | Php 5M/year (meeting costs, secretariat support)             |

------

## ON-2: STANDARDS DEVELOPMENT & MAINTENANCE

| Element         | Details                                                      |
| --------------- | ------------------------------------------------------------ |
| **ACTION**      | Continuously update FHIR IGs and PH Core as technology and policy evolves |
| **ACTIVITIES**  | • **IG Versioning:** Release new IG versions as needed (quarterly minor, annual major)<br>• **Backward Compatibility:** Maintain old versions for 2 years<br>• **International Alignment:** Monitor HL7 FHIR updates; attend HL7 meetings<br>• **Feedback Loop:** Vendor and facility feedback into IG revisions |
| **RESPONSIBLE** | Standards & Interoperability EG                              |
| **TIMELINE**    | Ongoing                                                      |
| **BUDGET**      | Php 8M/year (UP Manila NTC retainer, HL7 membership, connectathons) |

------

## ON-3: SCIV PROGRAM OPERATIONS

| Element         | Details                                                      |
| --------------- | ------------------------------------------------------------ |
| **ACTION**      | Continue EMR validation program, expand to HIL compliance    |
| **ACTIVITIES**  | • **Regional Testing:** Support EMR vendors to pass conformance tests<br>• **Certificate Issuance:** For validated EMRs<br>• **Monitoring:** Track EMR adoption and data quality from validated systems<br>• **Registry Management:** Maintain public registry of validated EMRs |
| **RESPONSIBLE** | PhilHealth SCIV Team + DOH Regional ICT Units                |
| **TIMELINE**    | Ongoing (already operational)                                |
| **BUDGET**      | Php 25M/year (per existing SCIV program budget)              |

------

## ON-4: PERFORMANCE MONITORING & REPORTING

| Element         | Details                                                      |
| --------------- | ------------------------------------------------------------ |
| **ACTION**      | Track NHDR/HIL performance metrics and publish transparency reports |
| **ACTIVITIES**  | • **Dashboard:** Real-time monitoring (transaction volume, success rates, latency)<br>• **Quarterly Reports:** Published to DOH website<br>• **Annual Evaluation:** External evaluation of NHDR impact<br>• **User Feedback:** Regular surveys of facilities and vendors |
| **RESPONSIBLE** | Health Enterprise Architecture & Data Governance EG + External M&E firm |
| **TIMELINE**    | Ongoing (starts with pilot)                                  |
| **BUDGET**      | Php 6M/year (dashboard tools, M&E firm retainer)             |

------

# SUMMARY: WHAT CHANGED, DELETED, ADDED

## DELETED (From Original Action Plan)

1. **"Finalization of Revised NHDR Framework"**
   - **WHY:** This is decision-making, not implementation. Moved to CP-1.
2. **Vague "Implementation" language**
   - **WHY:** "Implementation of PH Core" is too broad. Split into specific deliverables (Terminology Server, Registries, etc.)

------

## CHANGED (From Original Action Plan)

1. **"Development/Update of NHDR-related policies"** → **Specified 5 concrete policy deliverables** (JAO update, Data Sharing Agreement, Privacy IA, NHDR Access Policy, SCIV amendment)
   - **WHY:** "Policies" is too vague. Need to know what policies, for what purpose.
2. **"Advocacy Campaigns"** → **Multi-stakeholder engagement program** with specific audiences (vendors, LGUs, patients, media)
   - **WHY:** "Campaign" sounds like PR. This is stakeholder coalition-building.
3. **"Capacity Building"** → **Three-level training program** (Awareness, Technical, Advanced) plus helpdesk
   - **WHY:** One-size-fits-all training fails. Different audiences need different depth.
4. **"Update of NHDR Terms of Reference"** → **National Scale-Up Plan** (after pilot results)
   - **WHY:** TOR is procurement-speak. Need operational plan for going from 5 facilities to 20,000.
5. **"NHDR Use Case Development"** → **Pilot Program with 2 specific data flows** (Claims, Immunization)
   - **WHY:** "Use case development" sounds academic. Need to actually run real transactions.

------

## ADDED (Not in Original Action Plan)

### CRITICAL ADDITIONS:

1. **CP-3: HIL Platform Procurement**
   - **WHY:** You can't run an interoperability layer without infrastructure. Original plan had no procurement item.
2. **CP-4: FHIR Implementation Guides v1.0**
   - **WHY:** Standards without technical specs = vendors can't build. Need actual FHIR profiles.
3. **CP-5: Master Patient Index Strategy**
   - **WHY:** Can't merge data from multiple sources without patient matching. Original plan ignored this entirely.
4. **CP-6: Pilot Execution**
   - **WHY:** Original plan jumped from "use case development" to national rollout. That's how projects fail.
5. **CP-7: Scale-Up Decision & Planning**
   - **WHY:** Original plan assumed success. Need explicit go/no-go decision based on pilot results.
6. **EW-5: Funding & Sustainability Planning**
   - **WHY:** Original plan had zero discussion of money. Projects die from budget starvation.
7. **ON-1 through ON-4: Continuous Operations**
   - **WHY:** Original plan treated this as a project with an end date. NHDR is operational infrastructure that needs ongoing maintenance.

------

### STRUCTURAL ADDITIONS:

1. **Three-Tier Framework (Critical Path / Enabling / Ongoing)**
   - **WHY:** Original plan had flat list. Need to show dependencies and prioritization.
2. **Dependencies Column**
   - **WHY:** Shows what blocks what. Essential for project management.
3. **Budget Column**
   - **WHY:** "Timeline" without "Budget" is fantasy. Need cost reality check.
4. **Success Criteria Column**
   - **WHY:** "Done" must be measurable, not opinion-based.
5. **Risks & Mitigations Column**
   - **WHY:** Murphy's Law applies to government IT projects. Plan for failure modes.
6. **Deliverable Specificity**
   - **WHY:** Original had "agreements" as deliverable (what does that mean?). New version has concrete outputs (signed resolution, deployed platform, published FHIR IG, etc.)

------

# TOTAL RESOURCE REQUIREMENTS (All Tiers)

## Budget Summary (18 months to national scale-up decision)

| Category                           | Amount (Php)                      |
| ---------------------------------- | --------------------------------- |
| **Tier 1: Critical Path**          | **145-205M**                      |
| CP-1: Framework Decision           | <1M                               |
| CP-2: Pilot Design                 | 15-20M                            |
| CP-3: HIL Procurement              | 30-150M (depends on build vs buy) |
| CP-4: FHIR IGs                     | 5-8M                              |
| CP-5: MPI Strategy                 | 10-15M                            |
| CP-6: Pilot Execution              | (included in CP-2)                |
| CP-7: Scale-Up Planning            | 5M                                |
| **Tier 2: Enabling Workstreams**   | **50-65M**                        |
| EW-1: Policy Updates               | 2M                                |
| EW-2: PH Core Implementation       | 20-30M                            |
| EW-3: Capacity Building            | 12-15M                            |
| EW-4: Advocacy                     | 8-10M                             |
| EW-5: Funding Strategy             | 3M                                |
| **Tier 3: Ongoing (Annual)**       | **44M/year**                      |
| ON-1: Governance                   | 5M/year                           |
| ON-2: Standards Maintenance        | 8M/year                           |
| ON-3: SCIV Operations              | 25M/year                          |
| ON-4: Performance Monitoring       | 6M/year                           |
| **TOTAL (18 months + 1 year ops)** | **239-314M**                      |

**NOTE:** This does NOT include national scale-up costs (2027-2029), which would be substantially higher (likely Php 1-2B for 20,000 facilities).

------

## Staffing Requirements

### Core Team (Full-Time Dedicated)

| Role                   | FTE        | Notes                                |
| ---------------------- | ---------- | ------------------------------------ |
| Program Director       | 1          | Senior (Undersecretary or SVP level) |
| Technical Lead         | 2          | 1 DOH, 1 PhilHealth                  |
| FHIR Architect         | 2          | Can be UP Manila NTC contract        |
| Integration Engineers  | 4          | 2 DOH, 2 PhilHealth                  |
| Project Manager        | 1          | Tracks all Tier 1 & 2 activities     |
| M&E Specialist         | 1          | Tracks metrics and pilot results     |
| Communications Officer | 1          | Stakeholder engagement, advocacy     |
| **TOTAL**              | **12 FTE** |                                      |

### Extended Team (Part-Time / As-Needed)

- Legal (DOH, PhilHealth, DICT) - 3 lawyers, 20% time each
- Regional ICT Coordinators - 17 CHDs, 10% time each
- Expert Group Members - ~40 people, 10% time each
- External Consultants - HL7 trainers, M&E firm, technical advisors (contracted)

------

# FINAL RECOMMENDATION: IMMEDIATE NEXT STEPS (This Week)

If I were running this workshop, here's what I'd do before people leave the room:

## Day 1 (Today - Workshop Day):

1. **By End of Workshop:** Steering Committee commits to CP-1 decision by December 15, 2025
   - Formal vote: "Do we adopt Framework 3 (Interoperability Layer)?"
   - If not ready to vote, schedule emergency follow-up meeting
2. **Assign Owners:** For each Tier 1 item, name a person (not an agency) responsible
   - CP-2 Pilot: [Name], KMITS
   - CP-3 HIL Procurement: [Name], PhilHealth IMS
   - CP-4 FHIR IGs: [Name], UP Manila NTC
   - etc.
3. **Budget Reality Check:** DOH Finance and PhilHealth Finance brief on funding availability
   - Can we fund Php 200M from existing budget?
   - Do we need supplemental budget request?
   - Can we start with pilot (Php 20M) while securing full funding?

------

## Week 1 (Next 7 Days):

1. **Workshop Secretariat (KMITS):**
   - Distribute this action plan to all participants
   - Set up project tracking system (Asana, Monday.com, or even just Google Sheets)
   - Schedule CP-1 Steering Committee meeting (Dec 15 deadline)
2. **Technical Leads:**
   - Draft CP-2 Pilot Program Charter (facility selection criteria, data flow priorities)
   - Draft CP-3 HIL TOR (at least outline, even if procurement takes months)
3. **Communications:**
   - Draft press release (hold until after CP-1 decision)
   - Email to all workshop participants: "Here's what we decided, here's what happens next"

------

## Month 1 (By December 15):

1. **CP-1 Decision:** Framework approved, budget committed
2. **CP-2 Draft Complete:** Ready to select pilot facilities
3. **CP-3 TOR Draft:** Ready for legal review
4. **Vendor Outreach:** Informal conversations with top 5 EMR vendors to gauge willingness

------

# CONCLUSION: From Template to Executable Plan

The original action plan was a **template masquerading as a roadmap**. It had the right topics but no operational detail.

This reconstructed plan:

- **Sequences** work properly (Critical Path → Enabling → Ongoing)
- **Assigns** specific responsibilities to named roles
- **Budgets** realistically (Php 239-314M for 18 months)
- **Defines** success measurably (not "policy developed" but "5 signed issuances")
- **Anticipates** risks and mitigates them
- **Scales** appropriately (pilot before national rollout)

**Most importantly:** This plan is **actionable starting Monday morning**. Every person leaving the workshop knows what they're doing next week.

That's the difference between a strategy deck and an implementation plan.