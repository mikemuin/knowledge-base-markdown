# FRIENDS Requirements Gathering Checklist

**Project Name:** _________________________________
**Date:** _________________
**Facilitator:** _________________________________
**Stakeholders Present:** _________________________________

------

## How to Use This Checklist

1. **Review all categories** - Walk through each FRIENDS category systematically
2. **Ask the questions** - Use the prompts to guide stakeholder discussions
3. **Document responses** - Capture requirements in the notes section or separate documentation
4. **Mark completeness** - Check boxes as each area is addressed
5. **Identify gaps** - Note any areas requiring follow-up
6. **Map dependencies** - Record cross-category linkages

**Remember:** Not all categories are equally relevant to every project. Focus your effort appropriately.

------

## F - FORMS

### Current State Assessment

- [ ] What forms are currently in use (paper or digital)?
- [ ] What data is collected on each form?
- [ ] Who completes these forms?
- [ ] What happens to the form after completion?
- [ ] What approval workflows exist?
- [ ] Where are completed forms stored?
- [ ] How long are forms retained?
- [ ] What compliance requirements apply to forms?

### Future State Requirements

- [ ] Which paper forms need to be digitized?
- [ ] What new forms are needed?
- [ ] What fields are required vs. optional?
- [ ] What validation rules apply (data types, ranges, formats)?
- [ ] What conditional logic is needed (show/hide fields)?
- [ ] Who has access to complete each form?
- [ ] What is the approval workflow for each form?
- [ ] Are there form templates or standards to follow?
- [ ] What happens when a form is submitted?

### System-Embedded Forms

- [ ] What standard forms will the system provide?
- [ ] Do standard forms need customization?
- [ ] What user roles interact with each form?

### Custom Form Development

- [ ] What forms need to be built from scratch?
- [ ] What forms builder or development tools will be used?
- [ ] Are there complex calculations or logic requirements?
- [ ] Do forms need integration with other systems?

**Notes:**

------

------

------

------

## R - REPORTS

### Current State Assessment

- [ ] What reports are currently in use?
- [ ] Who uses each report and for what purpose?
- [ ] How often is each report generated?
- [ ] What data sources feed current reports?
- [ ] What are the pain points with current reporting?
- [ ] Are there compliance or regulatory reporting requirements?

### Report Requirements

- [ ] What new reports are needed?
- [ ] What is the purpose/business objective of each report?
- [ ] Who is the audience for each report?
- [ ] What data elements are needed in each report?
- [ ] What filters, parameters, or drill-down capabilities are needed?
- [ ] What format (PDF, Excel, dashboard, etc.)?
- [ ] What is the frequency (real-time, daily, weekly, monthly, on-demand)?
- [ ] What is the distribution method (email, portal, print)?

### Report Categories

- [ ] **Administrative Reports** - Operational metrics, activity logs
- [ ] **Performance Reports** - KPIs, turn-around time, SLA tracking
- [ ] **Financial Reports** - Budget, revenue, cost analysis
- [ ] **Compliance Reports** - Regulatory, audit, quality assurance
- [ ] **Data Exports** - Raw data downloads for external analysis

### Technical Considerations

- [ ] Will we use embedded/standard reports?
- [ ] What reports require custom development?
- [ ] What reporting tools/platforms will be used?
- [ ] What are the data refresh/latency requirements?
- [ ] Are there performance considerations (large data volumes)?
- [ ] What security/access controls apply to each report?

**Notes:**

------

------

------

------

## I - INTEGRATION/INTERFACES

### Integration Landscape

- [ ] What systems need to connect to this solution?
- [ ] What is the purpose of each integration?
- [ ] What data flows between systems?
- [ ] In what direction (inbound, outbound, bidirectional)?
- [ ] What is the frequency (real-time, batch, scheduled)?
- [ ] What is the data volume for each interface?

### Integration Types

- [ ] **Standard Protocols** - HL7, EDI, REST API, SOAP, FTP/SFTP
- [ ] **Custom Integrations** - Proprietary formats, legacy systems
- [ ] **Third-Party Services** - Payment gateways, shipping services, external APIs
- [ ] **Database Connections** - Direct database access, replication

### Technical Requirements

- [ ] What data format is used (XML, JSON, CSV, fixed-width)?
- [ ] What authentication/authorization is required?
- [ ] What error handling and retry logic is needed?
- [ ] What logging and monitoring is required?
- [ ] Are there service level agreements (SLAs) for interfaces?
- [ ] What happens if an interface fails?

### Data Mapping

- [ ] What is the source system data structure?
- [ ] What is the target system data structure?
- [ ] What transformations are needed?
- [ ] How are data conflicts resolved?
- [ ] What validation occurs before/after transfer?

**Notes:**

------

------

------

------

## E - ENHANCEMENTS

### Gap Analysis

- [ ] What business requirements cannot be met by standard functionality?
- [ ] What are the priority gaps (critical vs. nice-to-have)?
- [ ] What is the business impact of each gap?
- [ ] Are there workarounds for lower-priority gaps?

### Customization Requirements

- [ ] What existing functionality needs modification?
- [ ] What configuration changes are needed?
- [ ] What code customizations are required?
- [ ] What are the implications for system upgrades?

### Custom Module Development

- [ ] What net-new functionality must be built?
- [ ] What are the functional specifications?
- [ ] What are the technical specifications?
- [ ] What development approach (custom code, low-code, third-party)?
- [ ] What testing approach (unit, integration, UAT)?

### Process Optimizations

- [ ] What manual processes can be automated?
- [ ] What workflows can be streamlined?
- [ ] What user experience improvements are needed?
- [ ] What performance optimizations are required?

### Enhancement Tracking

- [ ] What is the enhancement ID/reference number?
- [ ] What is the business justification?
- [ ] What is the estimated effort/cost?
- [ ] What are the dependencies?
- [ ] What is the priority and target release?

**Notes:**

------

------

------

------

## N - NON-TECHNICAL

### Policies and Procedures

- [ ] What policies govern use of the system?
- [ ] What standard operating procedures (SOPs) are needed?
- [ ] What governance documents are required?
- [ ] What compliance policies apply?
- [ ] Who approves policies and procedures?
- [ ] How will policies be communicated and enforced?

### Manual Processes

- [ ] What processes will remain manual (not automated)?
- [ ] What is the workflow for manual processes?
- [ ] How do manual processes interface with the system?
- [ ] What controls exist for manual processes?
- [ ] What documentation is required for manual steps?

### Training Requirements

- [ ] Who needs to be trained (roles, number of users)?
- [ ] What training modalities (classroom, online, self-paced, hands-on)?
- [ ] What training materials are needed (guides, videos, job aids)?
- [ ] When should training occur (before go-live, after)?
- [ ] Who will deliver the training?
- [ ] How will training effectiveness be measured?

### Help Desk and Support

- [ ] What support model will be used (tiered support, help desk)?
- [ ] What are the support hours and availability?
- [ ] What is the escalation process?
- [ ] What knowledge base or FAQ is needed?
- [ ] What ticketing system will be used?
- [ ] What are the response time SLAs?
- [ ] Who provides first-line vs. second-line support?

### Change Management

- [ ] Who are the change champions or sponsors?
- [ ] What is the communication plan?
- [ ] What resistance is anticipated?
- [ ] What organizational impacts are expected?
- [ ] How will readiness be assessed?

### Documentation Requirements

- [ ] What user documentation is needed?
- [ ] What technical documentation is needed?
- [ ] What administrative documentation is needed?
- [ ] What format and accessibility standards apply?

**Notes:**

------

------

------

------

## D - DATA

### Master Data

- [ ] What are the core business entities (customers, products, employees, vendors)?
- [ ] What attributes define each entity?
- [ ] What is the authoritative source for each entity?
- [ ] What are the data ownership and stewardship roles?
- [ ] What data governance processes apply?

### Reference Data

- [ ] What lookup tables or code sets are needed?
- [ ] What pick-list values are required?
- [ ] What configuration data is needed?
- [ ] How is reference data maintained (who, how often)?
- [ ] What is the approval process for reference data changes?

### Data Quality

- [ ] What are the data quality standards?
- [ ] What validation rules apply?
- [ ] What data cleansing is needed?
- [ ] How are duplicates identified and resolved?
- [ ] What data quality metrics will be tracked?
- [ ] Who is responsible for data quality?

### Data Migration

- [ ] What data needs to be migrated from legacy systems?
- [ ] What is the volume of data to migrate?
- [ ] What is the source system data structure?
- [ ] What data transformations are needed?
- [ ] What is the data cutover strategy (big bang, phased)?
- [ ] What validation and reconciliation procedures are needed?
- [ ] What is the rollback plan if migration fails?
- [ ] What data archival is required?

### Data Management

- [ ] What are the data retention policies?
- [ ] What are the data archival requirements?
- [ ] What are the data backup and recovery procedures?
- [ ] How is sensitive data protected?
- [ ] What data audit trails are required?

**Notes:**

------

------

------

------

## S - SET-UP

### Infrastructure Requirements

- [ ] What server infrastructure is needed (application, database, web)?
- [ ] What are the sizing requirements (CPU, memory, storage)?
- [ ] What is the deployment architecture (cloud, on-premise, hybrid)?
- [ ] What redundancy and high availability is required?
- [ ] What disaster recovery capabilities are needed?
- [ ] What backup and restore procedures are required?

### End-User Devices

- [ ] What workstation specifications are needed?
- [ ] What operating systems are supported?
- [ ] What browsers are supported?
- [ ] What mobile device support is required?
- [ ] What peripherals are needed (scanners, card readers, etc.)?

### Printing Infrastructure

- [ ] What printers are needed (networked, local, specialized)?
- [ ] What printer types (laser, thermal, label, receipt)?
- [ ] What printing volumes are expected?
- [ ] What print job routing is required?
- [ ] What printer redundancy is needed?

### Network Requirements

- [ ] What bandwidth is required?
- [ ] What network connectivity is needed (LAN, WAN, VPN)?
- [ ] What are the latency requirements?
- [ ] What network security measures are needed (firewalls, segmentation)?
- [ ] What remote access requirements exist?
- [ ] What load balancing is required?

### Software and Licensing

- [ ] What software licenses are needed?
- [ ] What third-party software is required (databases, middleware)?
- [ ] What are the licensing models (per user, per server, concurrent)?
- [ ] What license compliance tracking is needed?

### Environment Strategy

- [ ] What environments are needed (development, test, staging, production)?
- [ ] What data refresh strategy for non-production environments?
- [ ] What environment parity requirements exist?

**Notes:**

------

------

------

------

## CROSS-CUTTING CONCERNS

### Security and Privacy

- [ ] **Authentication:** How do users log in? (SSO, MFA, local accounts)
- [ ] **Authorization:** What role-based access controls are needed?
- [ ] **Data Encryption:** What data needs encryption (at rest, in transit)?
- [ ] **Audit Logging:** What user actions need to be logged?
- [ ] **Privacy:** What PII/PHI is handled? What regulations apply (GDPR, HIPAA)?
- [ ] **Vulnerability Management:** What security scanning and patching processes apply?
- [ ] **Penetration Testing:** What security testing is required?

### Compliance and Regulatory

- [ ] What industry regulations apply?
- [ ] What audit requirements exist?
- [ ] What certification or accreditation is needed?
- [ ] What compliance reporting is required?
- [ ] What record retention policies apply?

### Performance and Scalability

- [ ] What are the expected user volumes (concurrent, total)?
- [ ] What are the expected transaction volumes?
- [ ] What are the response time requirements?
- [ ] What are the system availability requirements (uptime SLA)?
- [ ] What growth is anticipated over time?

### Business Continuity

- [ ] What is the recovery time objective (RTO)?
- [ ] What is the recovery point objective (RPO)?
- [ ] What disaster recovery procedures are needed?
- [ ] What business continuity testing is required?

------

## DEPENDENCIES AND LINKAGES

### Cross-Category Dependencies

Use this section to map how requirements in different categories depend on or impact each other.

| From Category | To Category | Dependency Description | Impact if Not Addressed |
| ------------- | ----------- | ---------------------- | ----------------------- |
|               |             |                        |                         |
|               |             |                        |                         |
|               |             |                        |                         |
|               |             |                        |                         |

**Examples:**

- Forms (F) → Reports (R): "Custom intake form must feed the weekly activity report"
- Data (D) → Integration (I): "Customer master data structure must align with CRM interface"
- Set-Up (S) → Forms (F): "Barcode scanners required for inventory forms to function"

------

## REQUIREMENTS PRIORITIZATION

After gathering requirements, prioritize using MoSCoW or similar framework:

### Must-Have (Critical for MVP/Go-Live)

------

------

### Should-Have (Important but can be deferred)

------

------

### Could-Have (Desirable but not essential)

------

------

### Won't-Have (Out of scope for current phase)

------

------

------

## COMPLETENESS CHECK

Before closing requirements gathering, confirm:

- [ ] All seven FRIENDS categories have been reviewed
- [ ] Stakeholders from all relevant areas have been engaged
- [ ] Cross-category dependencies have been identified
- [ ] Security and compliance requirements have been addressed
- [ ] Requirements have been prioritized
- [ ] Requirements are traceable to business objectives
- [ ] Gaps and unknowns have been documented for follow-up
- [ ] Requirements have been documented in the appropriate format
- [ ] Stakeholders have reviewed and validated requirements
- [ ] Sign-off process has been initiated

------

## FOLLOW-UP ACTIONS

| Action Item | Owner | Due Date | Status |
| ----------- | ----- | -------- | ------ |
|             |       |          |        |
|             |       |          |        |
|             |       |          |        |
|             |       |          |        |

------

## SIGN-OFF

By signing below, stakeholders confirm that the requirements for their area of responsibility have been adequately gathered.

| Stakeholder Name | Role/Area | Signature | Date |
| ---------------- | --------- | --------- | ---- |
|                  |           |           |      |
|                  |           |           |      |
|                  |           |           |      |

------

**Next Steps:**

1. Document detailed requirements in formal requirements specification
2. Create requirements traceability matrix
3. Develop work breakdown structure based on FRIENDS categories
4. Estimate effort and cost for each category
5. Plan iterative delivery approach

