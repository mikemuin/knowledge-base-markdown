# Product Description Sheet: PMO & Governance Platform

## 1. Product Overview

This is a centralized Project Management Office (PMO) platform designed to standardize project delivery, enforce governance, and provide executive visibility. Unlike standard task management software, this system focuses on **auditability, trend reporting, and decision tracking**. It serves as the "Single Source of Truth" for the health, financial status, and risks of the project portfolio.

---

## 2. User Roles & Access Control

The platform enforces a strict permission hierarchy to maintain data integrity.

* **PMO Lead (Administrator)**
* **Scope:** Full access to all projects, financial data, and system configurations.
* **Capabilities:** Can configure dropdown values (e.g., Risk Categories), approve global changes, manage user access, and view the Master Portfolio Dashboard.

* **Project Manager (Editor)**
* **Scope:** Read/Write access strictly to assigned projects.
* **Capabilities:** Responsible for maintaining registers, updating RAG statuses, managing financials, and publishing the formal Status Report. Cannot delete projects or bypass audit workflows.

* **Stakeholder / Executive (Viewer)**
* **Scope:** Read-Only access.
* **Capabilities:** View-only permissions for Dashboards and published Status Reports. Cannot see draft items or work-in-progress logs.

---

## 3. Core Modules & Registers

### A. Governance & Reporting

* **Projects Hub:** The central repository for project metadata. Tracks key attributes including Project Phase (Initiation, Planning, Execution, Closure), Start/End Dates, and high-level RAG (Red/Amber/Green) indicators for Schedule, Scope, and Quality.
* **Status Reports:** A dedicated engine for creating immutable executive summaries.
* *Snapshot Logic:* Freezes project data at the moment of publication for historical trend analysis.
* *Workflow:* Supports Draft  Submitted  Published  Revised states.
* *Version Control:* "Published" reports are locked records. Corrections generate a versioned amendment (e.g., v1.1).

* **Financials:** High-level budget tracking. Records **Original Budget**, **Current Approved Budget**, **Actual Spend**, **Forecast to Complete**, and **Variance**.

### B. Execution & Tracking

* **Milestones:** Tracks major schedule events and stage gates. Includes fields for Baseline Date, Forecast Date, and Variance.
* **Action Items:** Tracks high-level directives and Steering Committee actions. Includes "Owner" mapping to specific Stakeholders and due dates.
* **Deliverables:** Lists the tangible outputs of the project. Tracks status against the signed-off scope.
* **Dependencies:** Manages internal blocking factors and external vendor dependencies. Distinguishes between Inbound (We are waiting on X) and Outbound (X is waiting on us).

### C. Control, Risk & Audit

* **Risk Register:** Tracks potential future events. Requires quantification of **Probability (1-5)** and **Impact (1-5)** to generate a Risk Score. Includes "Mitigation Strategy" fields.
* **Issue Log:** Tracks active problems impacting the project. Includes "Severity" and "Resolution Plan" fields.
* **Change Register (CR):** A formal workflow for requesting changes to Scope, Timeline, or Budget. Tracks the Requestor, Impact Analysis, and Approval Status (Approved/Rejected/Deferred).
* **Decision Log:** A permanent audit trail of strategic decisions made by leadership. Captures *Who* decided, *When*, and the *Rationale*, preventing the re-litigation of closed topics.
* **Assumptions & Constraints:** Documents the foundational assumptions (e.g., "Resources will be available in Q3") and fixed constraints (e.g., "Budget cannot exceed $50k").

### D. Knowledge & Engagement

* **Meeting Management:** A module for running SteerCos and Working Groups.
* *Integrated Actions:* Allows users to spawn Action Items, Risks, or Decisions directly from the meeting minutes text.

* **Stakeholder Register:** A directory of individuals impacting the project (beyond just system users). Tracks Role, Influence Level, Interest Level, and Engagement Strategy.
* **Lessons Learned:** A repository for Retrospectives. Captures "What went well," "Root Cause of issues," and "Recommendations" for future projects.
* **Project Assets:** A consolidated library for unstructured data, including Reference Lists, External Links, and Document attachments.

---

## 4. Dashboards & Visualization

### Portfolio Dashboard (Command Center)

A unified grid view for PMO Leads and Executives showing all active projects.

* **Aggregated RAG:** Visual count of projects in Red, Amber, and Green statuses.
* **Financial Roll-up:** Total portfolio spend vs. budget.
* **Milestone Roadmap:** A timeline view of upcoming major milestones across the organization.

### Project Health Dashboard

The landing page for a specific project.

* **Trend Indicators:** Visual arrows (  ) showing if the project health is improving or deteriorating compared to the last report.
* **Top Risks & Issues:** Displays the "Top 5" critical items requiring immediate management attention.

---

## 5. System Capabilities

* **Immutable Audit Trail:** All edits to the Decision Log, Change Register, and Published Reports are timestamped. "Deleted" items in these registers are soft-archived to ensure audit traceability.
* **RAG Inheritance:** Project-level RAG status is manually set by the PM but is informed by data from the underlying registers (e.g., specific indicators for Schedule vs. Budget).
* **Cross-Linking:** Ability to link a specific **Issue** to a **Change Request**, or a **Meeting Minute** to a **Decision**, creating a navigable web of project context.