# Philippine Health Architecture: Blueprint for a Person-Centric System

This briefing document summarizes the key findings and recommendations from the "Development of Health Enterprise Architecture for the National Health Program (Phase 1)" report (May 2017), a project funded by the European Union for the Philippine Health Sector Reform Contract. The report assesses the current state of health enterprise architecture (HEA) in the Philippines, particularly in relation to the Philippine Health Agenda (PHA) and its "ACHIEVE" initiative.

### Detailed Briefing Document: Philippine Health Enterprise Architecture Assessment (Phase 1)

**Report Title:** Development of Health Enterprise Architecture for the National Health Program (Phase 1) **Authors:** Derek Ritz & Joseph De Guia (Consultants, EU Health Sector Reform Contract Period) **Date:** May 2017 **Funding:** European Union (DCI-ASIA Programme For Philippines)

### 1. Executive Summary

This report provides an assessment of the existing Health Enterprise Architecture (HEA) in the Philippines, focusing on its alignment with the ambitious Philippine Health Agenda (PHA) and its "ACHIEVE" initiative. The overarching finding is that while the Department of Health (DOH) and PhilHealth (PHIC) have developed their own organizational-centric segment architectures, there is a critical need for a **person-centric, sector-wide Health Sector Blueprint v2** to effectively coordinate efforts across public and private health organizations for the benefit of all Filipinos.

Key challenges identified include:

- Lack of a unified, person-centric approach to health information.
- Insufficient infrastructure for health information sharing and underdeveloped interoperability specifications.
- Weak integration of eHealth systems into frontline care workflows, leading to data entry burdens and limited value for health workers.
- The "opt-in" consent policy for information sharing, which impedes comprehensive care.
- Weak market regulation of eHealth products and a fragmented approach to eHealth software development by DOH.
- Insufficient private sector and Local Government Unit (LGU) representation in eHealth governance.

The report strongly recommends the development of a unified Health Sector Blueprint (HSB) that defines how various segment EAs will collaborate, along with policy and technical interventions to mandate information sharing, enforce care guidelines, and ensure patient safety in eHealth.

### 2. The Philippine Health Agenda (PHA) and ACHIEVE Initiative

The PHA serves as the definitive framework for the health sector's "target configuration" and the business case for change. It identifies critical challenges in **equity, cost, and quality** within the existing health system.

**2.1. PHA Goals and Values:** The PHA's goals and values resonate with the aspirations of Filipinos and align with the missions of DOH and PhilHealth.

**2.2. The Business Case for Change:** The report frankly states, as illustrated by Figures 5, 6, and 7, that "the Agenda very candidly and bluntly delineates the unacceptability of the status quo." The PHA is committed to a new approach, centered around three guarantees:

- **Guarantee #1: Services to both promote lifelong health and treat the triple burden of disease.** This involves person-centric health promotion and tailored services across all life stages.
- **Guarantee #2: A well-functioning, high-quality, accessible network of health facilities.** This emphasizes systematic operation, improved accessibility, and gatekeeping governed by broadly-deployed care guidelines.
- **Guarantee #3: Universal Health Insurance financed predominantly by PhilHealth.** This ensures universal PhilHealth membership, tax-funded insured services, and PhilHealth as the primary purchaser of health services from public and private networks.

**2.3. ACHIEVE's Implications for Blueprint v2:** The cornerstone of the PHA is the **ACHIEVE** initiative, which outlines key areas for transformation:

- **A - Advance health promotion, primary care and quality:Today:** Patients "jump" between care levels, referrals are ad hoc, PhilHealth contracts with individual providers (not Service Delivery Networks - SDNs), quality metrics are weak, annual "health census" is not universal, surveillance data is weak, and no mega-hospitals exist in rural areas.
- **ACHIEVE:** DOH to develop Integrated Care Pathways (ICPs), PhilHealth to enforce referral adherence through incentives/penalties, PhilHealth to contract with SDNs based on ICP adherence and outcomes, DOH/LGUs to implement "health census" tools and rudimentary surveillance with actionable scorecards, and DOH to establish three mega-hospitals for national referral.
- **C - Cover all Filipinos against financial health risk:Today:** Insufficient funding, 8% non-formal sector Filipinos are not PhilHealth members, case rates are not linked to empirical costs or quality, many essential services are uninsured, and facilities do not retain income.
- **ACHIEVE:** PhilHealth/DOH to develop more valuable benefit portfolios (cost-effectiveness analysis informed), PhilHealth to issue unique person-centric health ID#, develop evidence-based case rates linked to quality, and enforce LGU health trust fund payments.
- **H - Harness the power of strategic HRH (human resources for health):Today:** Rural/GIDA areas suffer HRH shortages.
- **ACHIEVE:** DOH to partner with academe for targeted training, PhilHealth/LGUs to use provider payment incentives for remote area vacancies.
- **I - Invest in eHealth and data for decision-making:Today:** Frontline health workers lack ICT support, high data entry burden, non-networked facilities create "blind spots," data is siloed, and data-driven management is poorly supported.
- **ACHIEVE:** DOH/LGUs to create clear EMR functional specifications (HL7 EHRS-FM), maximize care delivery and minimize data entry (e.g., OMR, telemedicine). Critically, "DOH, PhilHealth and LGUs to support (via PHIE) and enforce secure, confidential, electronic sharing of person-centric information from all healthcare sites (public and private) as a condition of license and accreditation." Also, "DOH to enforce that every Filipino to have a confidential, secure, longitudinal, lifetime, electronic health record – linked to their PhilHealth ID#." DOH to share birth/death transactions with CRVS, and embrace open data initiatives.
- **V - Enforce accountability and transparency:Today:** Insufficient price/performance transparency, ad hoc provider quality monitoring, inconsistent LGU scorecards.
- **ACHIEVE:** DOH/PhilHealth/LGUs to leverage "big data" from mandatory electronic reporting for analytics, scorecards (quality, fraud detection), supportive supervision, and public accountability.
- **E - Value clients and patients, especially the poor and vulnerable:Today:** Wide inequities in access/outcomes (wealth quintiles), underutilization by poor, poor reputation of public care.
- **ACHIEVE:** DOH/PhilHealth/LGUs to develop targeted service offerings and financial incentives for bottom quintiles (e.g., GIDA profiling, telemedicine), PhilHealth/DSWD/NAPC to coordinate financial aid for travel, and coordinate actionable customer feedback with accountability.
- **E - Elicit multi-sector, multi-stakeholder support for health:Today:** Private sector participation challenged by DOH eHealth governance, collaboration with other GoP agencies undermined, "Health in all Policies" not routine.
- **ACHIEVE:** DOH to create governance seats for private sector, allied agency, and civil society, PhilHealth to accredit and contract with public-private partnerships and private providers at the SDN level, and DOH to participate more broadly in GoP policy/legislation to support "Health in all Policies."

### 3. Assessment Findings

**3.1. Overarching Finding: Need for a Person-Centric Health Sector Blueprint** The primary finding is that existing DOH and PhilHealth segment architectures are, correctly, organization-centric. However, "The blueprint for the Philippine health sector, however, must have a fundamentally different character. The health sector’s role is to serve the health of Filipinos. A sector-wide EA, therefore, needs to be **person-centric** and coordinate the efforts of the constellation of public and private health organizations to best serve the people of the Philippines." (Figure 19, "Health Sector EA vs Segment EA").

**3.2. Specific Challenges Identified:**

- **High-Quality Care:** There is a "wide disparity of health outcomes between Filipinos in the first and fifth income quintiles." Longitudinally coordinated, patient-safe, high-quality care is not designed into the overall health sector's underlying workflows.
- **Identity:** While using the PhilHealth ID# as a national health ID# is an "excellent option," the current system focuses on the account number (family number) rather than the individual member ID#. The "person-centric ID# must be used, not account-centric one."
- **Information Sharing:** "There is no established infrastructure for health information sharing and the interoperability specifications for such an infrastructure are under-engineered." The current "opt-in" consent policy hinders information flow. The report recommends evolving to an "opt-out" framework for healthcare purposes to support patient safety and quality.
- **Guideline-Adherence:** While pockets of success exist (e.g., St. Luke's, Philippine Heart Center), there is a lack of "national set of integrated care pathways (ICPs) defining care practice guidelines, clear care escalation criteria, and SDN-level referral patterns." Existing eHealth systems are often poorly integrated into frontline workflows, providing "low value to these workflows" and increasing data entry burden (e.g., midwives entering data after patient encounters, motivated only by capitation funds, with no perceived benefit for care delivery).
- **Mandatory EHR Updates:** A "perverse incentive" exists in the private sector against sharing health information due to perceived financial impacts. "Information-sharing be made mandatory and that to not do so would put at risk both a provider’s DOH license and PhilHealth accreditation."
- **Safe Health Software:** "There is weak market regulation of eHealth products and DOH, itself, does not presently employ ISO-13485 conformant practices regarding eHealth software development." This poses a patient safety risk, especially given future eHealth investments.
- **Governance:** "Private sector and LGU-level care provider representation within the eHealth governance structure is weak." The absence of eHealth solution vendors in governance meetings is also noted.

**3.3. Readiness to Support ACHIEVE (Assessment of DOH and PhilHealth EAs):**

- **General:** No functioning EA repository exists, leading to difficulties in accessing and managing architectural artifacts.
- **DOH EA:**The DOH EA v1.0 (2011) is outdated and has not been updated since.
- The 2011 EA was primarily motivated by the Aquino Health Agenda (AHA), focusing on "ICT for health system monitoring and management," not "improving individual care delivery." This is "out of step with the new Agenda."
- The Philippine eHealth Strategic Framework and Plan (2013) introduced the concept of Health Information Exchange (HIE) and a longitudinal person-centric Electronic Health Record (EHR).
- The 2015 DOH Information Systems Strategic Plan (ISSP) mentions the HIE but does not list its development and implementation as a Major Final Output (MFO) or a top priority.
- The DOH ISSP "intends to make a case for DOH to be in the software business and to harmonize all CHT systems," with targets for iClinicSys deployment. This raises questions about DOH's eHealth software product strategy (mandating its own products vs. clear interoperability specifications).
- The PHIE conceptual framework (2015 ISSP) indicates the interoperability layer and shared health record repository are "yet to be implemented." The placement of the shared health record as a DOST asset is questioned, suggesting it "may be more suitably identified as a DOH asset."
- PHIE Lite is described as a preliminary implementation, but engineering analysis suggests it may be tied to iClinicSys data forms rather than a canonical information model for HIE, potentially leading to a "breaking change" for future "full" PHIE implementation.
- **PhilHealth EA:**Both PhilHealth EA versions (2012, 2015) pre-date the new Agenda but consistently identify "member/citizen centricity as the #1 EA principle."
- The PhilHealth EA (2015) does not explicitly identify PHIE as either a supplier or consumer of PhilHealth data, which is a significant "gap."
- PhilHealth's focus is on externally-facing interfaces supporting the account number for eligibility and claims. An interface to the underlying, person-centric ID# needed for unique client identification is "missing."

### 4. Recommendations

DOH is expected to lead and coordinate overall digital health efforts. The report proposes the following actionable items, categorized by short, medium, and long-term priorities:

**4.1. Key Recommendations:**

1. **Develop a Draft Health Sector Blueprint (HSB):** This will describe how segment EAs will collaborate to operationalize the PHA. (Short Term)
2. **Mandate Inclusive eHealth Governance:** Publish legislation ensuring private sector, LGU care provider, and private sector eHealth vendor representation on all eHealth governance committees. (Short Term)
3. **Re-engineer PhilHealth Client ID:** Improve/re-engineer the use of the unique, person-centric client ID interface based on the interoperability profile defined in the HSB. (Short Term)
4. **Adopt "Opt-Out" Consent Policy:** Develop a policy paper for a national opt-out consent model for health information sharing and advocate for its enactment. Designate a Data Privacy Officer within DOH KMITS. (Short Term, Long Term for assessment)
5. **Develop National Integrated Care Pathways (ICPs):** Convene medical committees to develop consensus-based ICPs for top causes of death, morbidity, and MNCH. Mandate their use by all health providers. Incorporate ICPs into eHealth software with conformance-testable functionalities (HL7 EHRS-FM). Enact appropriate certification and market-wide regulation for patient-safe eHealth software. (Medium Term)
6. **Re-engineer PhilHealth Claims Adjudication:** Review and re-engineer claims adjudication rules to incentivize and operationalize broad adoption of consensus ICPs, integrating them into PhilHealth's ICT systems. (Medium Term)
7. **Mandate Health Information Posting to PHIE:** Publish a white paper on PHIE's role in ensuring high-quality, patient-safe care and draft regulations for mandatory posting of health information to the PHIE after each care encounter. (Short Term)
8. **Rationalize Data Collection and Reporting:** Develop actionable indicators from the canonical HIE information model, phasing out indicators not naturally arising from routine care delivery. Ensure FHSIS data collection transitions to PHIE and EMRs are ready for care delivery workflow. (Medium Term, Long Term for sustainability)

**4.2. Road Map (Short, Medium, Long Term):**

- **Short Term (6 months – 1 year):(Rec 1) Design and build the PHIE:** Accelerate with technical assistance for HSB development.
- **(Rec 2) DOH Department Order:** Mandate inclusive representation on eHealth governance committees.
- **(Rec 3) Re-engineer PhilHealth person-centric ID interface:** Establish PHIE software reference and testing lab (collaborate with AeHIN SIL-Asia lab).
- **(Rec 4) Update Administrative Order for opt-out consent:** Present policy paper with National Privacy Commission. Designate DPO.
- **(Rec 7) Publish PHIE white paper:** Encourage public-private-academe partnerships for patient safety and care quality.
- **Medium Term (1 – 3 years):(Rec 5) Develop ICPs and mandate their use:** Integrate CPGs and referral paths into PHIE. DOH to adopt international standards and develop policies/regulations.
- **(Rec 6) PHIC Adjudication:** Monitor claims through PHIE, leveraging data harmonization to mitigate fraud.
- **(Rec 8) FHSIS transition to PHIE:** Ensure all EMRs and eHealth software are ready for care delivery workflow.
- **Long Term (3 – 5 years):(Rec 4) Assess and monitor data sharing and PHIE use:** Address privacy issues and data leakage. Capacitate DPO and KMITS data officer on breach response protocols.
- **(Rec 8) PHIE Sustainability:** Ensure operationalization covers technology, support, and updates for all users.

**Annexes and Field Visit Insights (Summary):**

The annexes provide valuable qualitative data from field visits to regional DOH and PhilHealth offices, as well as rural health units (RHUs) and hospitals in Iloilo, Pasig (Metro Manila), and Camarines Sur. Key insights include:

- **Western Visayas Medical Center (Iloilo):** Uses iHOMIS, but medical records are mostly paper-based. No sharing of patient records between systems or facilities. iHOMIS modules are limited to basic operations and PHIC claims, not customizable. Data accuracy and medicine inventory are challenges.
- **Zarraga Rural Health Unit (Iloilo):** Uses two EMRs: SHINE (real-time consultation, limited reports) and iCLINICsys (DOH FHSIS reporting, slow due to internet). Data is duplicated. Manual reporting persists due to system limitations and slow connectivity.
- **Pototan Municipal Health Center (Iloilo):** Uses paper-based Individual Treatment Records (ITRs) for patient health records, encoded *after* consultation. iCLINICsys processing is very slow, with data errors (e.g., patient family count). Nurses spend time manually encoding.
- **Janiuay Rural Health Unit (Iloilo):** Largely paper-based. Limited EMR training (only PHA). Most midwives untrained. Additional burden for field reporting. Rely on nearby internet shops for online submissions. Shortage of manpower. Plans to start EMR use and training in 2017.
- **PHIC Regional Office – PRO VI (Iloilo):** High membership coverage (94-98%). Challenges include real-time payment updates, delayed claim processing due to documentation, and difficulties with pharmacy accreditation project (connectivity). PHIC IDs are principal-member centric; dependents' IDs exist in the database but are not issued. Membership database not shared with healthcare facilities due to privacy concerns. Capitation funds to RHUs are often delayed due to manual reporting and low compliance.
- **DOH Regional Office – NCR (Mandaluyong):** Frontline workers face high data entry burden from multiple, fragmented health information systems. Data encoding delays reports by months. Systems need to be offline-capable. Emphasizes building systems for end-users (health providers) rather than just statisticians.
- **Pasig City Health Office:** Fragmented reporting systems cause silos. Patchy internet connectivity even in urban areas. Cultural and behavioral challenges in system adoption. Calls for more manpower, hardware, and training for iCLINICsys.
- **Kalawaan Health Center (Pasig):** Nurses untrained on iCLINICsys; learn by navigating. Not used real-time; paper forms preferred. Slow internet and system latency. No dedicated encoder. EMR follow-up/referral features unused. Challenges with troubleshooting, printer access, medicine dispensing, and clinician orders. Data in EMRs not updated; used primarily for PCB capitation.
- **PHIC LHIO NCR South (Pasig):** Streamlined membership updates (no documentary requirements). PINs linked to dependents but no cards issued for them. Challenges with real-time payment updates and internet connectivity for e-claims. Acknowledges DOH provides license, PHIC provides accreditation.
- **DOH Regional Office – Bicol (Legaspi):** Numerous iCLINICsys versions (1.2 to 1.9 in 7 months) caused implementation difficulties and inconsistent content. P10M spent on training. Regional office still relies on manual processing. EMRs only present aggregated data, not useful for local planning. Clinicians not consulted in EMR design. Many older midwives struggle with EMRs. Connectivity issues in GIDA areas. No permanent IT positions in RHUs. Significant frustration with iCLINICsys for being a "burden" with "no benefit at the regional operations." Capitation funds from PhilHealth not always spent on EMR implementation by LGUs.
- **PHIC – PRO V (Legazpi):** Data quality issues in membership coverage. Challenges with bandwidth for e-claims. Discussed incentives vs. penalties for data sharing.
- **Bicol Medical Center:** Internet connectivity issues (SLA problems). Significant investment in hardware/software. iHOMIS embedded in hospital workflows but paper records persist for discharge summaries, doctor's orders. Patient charts and medical history viewable only within specific hospital areas for privacy. Confidentiality concerns hinder information sharing (e.g., HIV, mental health). Desire for web-based system and wireless infrastructure.
- **Private Hospitals in Bulacan:** Express skepticism about "Universal Health Coverage" and government's ability to provide health care. Burdensome ID requirements and government bureaucracy. Poor telecom infrastructure. Unreliable government online systems. Challenges with PhilHealth "not paying on time" for claims, impacting hospital operations and making software investments difficult. Question whether current case rates are sufficient. Referral system is based on personal relationships, not service delivery networks. Need for "carrot and stick" approach for data sharing incentives.

These detailed field reports underscore the systemic and practical challenges in implementing eHealth solutions across the Philippines, highlighting the gap between policy aspirations and ground-level realities, particularly concerning connectivity, user adoption, data quality, and the fragmented nature of existing systems.

NotebookLM can be inaccurate; please double check its responses.