# Position Paper

## Executive Summary

The National Health Data Repository (NHDR) framework was established in March 2022 as the mandated data infrastructure for Universal Health Care in the Philippines. Two years later, it remains unimplemented—no deployment strategy exists, no functional systems are operational, and no progress toward the interoperable digital health ecosystem required by the UHC Act has been achieved. This failure directly threatens the entire UHC agenda: without functioning health information exchange, the reforms prescribed by law—integrated service delivery through P/CWHS, population health management, and strategic resource allocation—cannot be realized. The absence of operational NHDR infrastructure is a systemic failure that undermines the foundational promise of Universal Health Care to all Filipinos.

This position paper argues that:

- The NHDR must be fundamentally reimagined and implemented as a federated "network of networks" architecture that prioritizes empowering Province-wide and City-wide Health Systems (P/CWHS) as the primary locus of health information exchange.
- Rather than pursuing a centralized, top-down data aggregation model, implementation must begin by delivering tangible clinical value at the point of care—enabling seamless referrals within HCPNs, eliminating duplicate diagnostics, and providing complete patient information during emergency encounters.
- National-level data consolidation for surveillance, policy-making, and research should emerge as a secondary benefit of well-functioning local HIE systems, rather than being the primary design driver.

This approach aligns with the UHC Act's explicit mandate for regional integration and devolution, respects the governance authority vested in Provincial and City Health Boards, and creates the sustainable value proposition necessary for frontline adoption and long-term system viability.

## Problem Description

**The National Health Data Repository remains unimplemented two years after its establishment in March 2022, leaving the Philippine health system without the data infrastructure mandated by the Universal Health Care Act.**

- **Architectural failure:** The NHDR is conceptualized as a centralized submission-compliance system rather than a federated network built from real-time P/CWHS clinical transactions. This prioritizes national aggregation over local clinical value, burdening providers without delivering the benefits of care coordination.
- **Governance vacuum:** No implementation authority exists despite the UHC Act vesting accountability in Provincial and City Health Boards. The foundational registries required for interoperability—Client/Patient, Health Facility, Health Provider, Terminology, and Product/Medicines—remain unestablished and ungoverned.

- **Capacity and financing gaps:** P/CWHS lack structured technical support, implementation playbooks, and clear guidance on using Special Health Funds for HIE infrastructure. No capacity-building program exists, and financing mechanisms fail to incentivize investments in standards conformance or interoperability.
- **Standards without implementation:** The SCIV process exists on paper but lacks pilot validation, operational testing, or practical enforcement mechanisms to ensure reliable exchange at scale.

**The result: UHC reform without its data foundation, mandates without execution, and the entire health system transformation at risk.**

## Position Statements

### 1. Reorient the NHDR as a Federated Network of Networks

**The NHDR must be built from real-time P/CWHS clinical transactions, not submission-compliance requests.** Implement a federated architecture where P/CWHS are lawful custodians and active contributors to a shared health record, rather than mere data uploaders. Clinical and public health data generated during care delivery flow through an interoperability layer, carrying the required identifiers. National insight derives from the same transactions used for care coordination—faster, more accurate, and auditable—positioning P/CWHS as partners, not reporting units.

### 2. Adopt OpenHIE as the NHDR Implementation Framework

**The NHDR is the Philippines' implementation of OpenHIE.** OpenHIE defines three layers: foundational registries (client/patient, provider, facility, terminology, and products); the interoperability layer (routing, validation, and security); and the services layer (shared health record, analytics, and terminology services). Under this framework, the Shared Health Record is one service component within the NHDR, not the entire repository. It holds longitudinal data accessed under strict role-based controls for continuity of care, safety monitoring, and population analytics. National master registries synchronize with local P/CWHS copies, enabling jurisdictions to steward their data while maintaining an authoritative national source.

### 3. Establish and Govern Foundational Master Registries

**Interoperability cannot function without authoritative registries.** DOH must designate, operationalize, and govern five foundational registries: Client/Patient, Health Facility, Health Provider, Terminology, and Product/Medicines. Each requires published code systems, APIs, data dictionaries, and clear procedures for identifier issuance, correction, and deduplication. Name authoritative sources (PhilSys for client identity, DOH licensing for facilities/providers), publish governance protocols, and enforce registry use through the interoperability layer. These registries are prerequisites—without them, P/CWHS cannot reliably exchange data.

### 4. Build Capacity Through Practical Implementation Support

**P/CWHS need structured technical assistance, not policy mandates alone.** Establish a national capacity-building program with: (1) implementation playbooks covering registry onboarding, identifier use, error handling, and security protocols; (2) model legal templates for data-sharing agreements; (3) reference technical specifications and adapters for standard transactions; and (4) designated test sites where P/CWHS validate systems before national deployment. This ensures repeatable, quality implementation across jurisdictions with varying capabilities.

### 5. Strengthen Standards Conformance and Pilot Before National Rollout

**Review and update SCIV to align with federated architecture.** Conformance must cover end-to-end exchange—encompassing security, identity matching, referral workflows, and error handling—not just file formats. Conduct a regional pilot implementation to identify operational issues at scale, including system performance, governance gaps, and procurement challenges. Establish objective readiness thresholds (data quality, timeliness, completeness) and refine standards based on pilot findings before requiring nationwide compliance.

### 6. Issue Financing Guidance to Enable Local Investment and Incentivize Performance

**DOH must issue policy guidance enabling SHF and PhilHealth mechanisms to support HIE.** Recommended financing: (1) SHF investment in P/CWHS infrastructure (registry operations, connectivity, maintenance); (2) performance incentives tied to measurable outcomes (registry completeness, data timeliness and quality, referral completion rates, system availability). Require demonstrated standards conformance as a condition for LGU procurement and PhilHealth contracting. This ensures that P/CWHS have the resources to participate while creating accountability for a reliable exchange.

## Conclusion

**The NHDR failure threatens the implementation of Universal Health Care, but the path forward is clear.** A federated network-of-networks architecture built on OpenHIE principles, empowered P/CWHS governance, established foundational registries, provided comprehensive capacity building, and aligned financing mechanisms can transform the NHDR from a stalled policy into a functional infrastructure. This approach delivers clinical value where care is offered, respects the legal accountability structure established by the UHC Act, and fosters sustainable local ownership. **Implementation must begin immediately—two years of inaction is two years of UHC reform without its data foundation. The health of millions depends on getting this right.**