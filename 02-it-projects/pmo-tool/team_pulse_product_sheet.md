# Team Pulse Product Sheet

Here is the comprehensive **Product Data Sheet** for the "TeamPulse" counterpart platform.

This document outlines the architecture, functional modules, and data schema required to build the system.

------

# Product Data Sheet: Weekly Team Contribution & Productivity Platform

**Version 1.0**

## 1. Product Overview

This is a bottom-up productivity tracking system designed specifically for **Matrix Organizations**. Its primary function is to capture the weekly output, workload, and sentiment of individual contributors who report to a Line Manager (Functional) but execute work across multiple Projects (Delivery).

The platform solves the "Reporting Fragmentation" problem by allowing users to submit a single weekly update that is intelligently sliced and routed to multiple stakeholders.

------

## 2. User Roles & Access Control

- **Contributor (The User):** The individual team member. Can view only their own history and public team shouts.
- **Line Manager (Functional Lead):** The direct supervisor. Has a holistic view of their direct reports' workload, sentiment, and total output across all projects.
- **Project Manager (Delivery Lead):** The project owner. Has a restricted view, seeing *only* the specific updates and blockers related to their assigned projects.
- **Administrator:** System configuration and user hierarchy management.

------

## 3. Functional Modules

### A. The Weekly Pulse (Input Engine)

*The core interface for the Contributor. A structured form designed to be completed in <15 minutes.*

- **Project Streams (Dynamic):** The system fetches the user's project assignments from the PMO Governance Tool.
  - *Field - Accomplished:* Rich text list of value delivered this week.
  - *Field - Next Steps:* Planned activities for the upcoming week (feeds the "Reliability" metric).
  - *Field - Blockers:* Specific impediments. These are flagged for the PM.
- **BAU / Internal Stream:** A fixed section for non-project work.
  - *Field - Context:* Captures "Shadow Work" (Mentoring, Hiring, IT Support, Training) often invisible to PMs.
- **Capacity & Sentiment (The "Pulse"):**
  - *Workload Score:* 1 (Underutilized) to 5 (Overwhelmed).
  - *Sentiment:* Emoji-based mood tracker.
  - *Private Note:* A confidential channel strictly between Contributor and Line Manager (bypassing PMs).
- **The "Highlight Reel":**
  - *Field:* "What is your biggest win this week?"
  - *Purpose:* Auto-generates a "Brag Sheet" for annual performance reviews.

### B. The Line Manager Dashboard (People View)

*A consolidated view for managing a functional team (e.g., "Frontend Developers").*

- **Workload Heatmap:** visual grid showing the Capacity Scores of all direct reports over the last 6 weeks. Identifies burnout trends or underutilization.
- **Blocker Aggregator:** A unified list of all blockers affecting the team, regardless of project. Allows the Manager to spot systemic issues (e.g., "Why are 4 people waiting for VPN access?").
- **Output Stream:** A read-only feed of all "Accomplished" items from the team, grouped by person.
- **Sentiment Analysis:** Trend line of team morale over time.

### C. The Project Manager View (Matrix Integration)

*A filtered view injected into the PMO workflow.*

- **Update Injection:** When a Contributor submits their update for "Project A," the text is automatically pushed to the "Project Journal" or "Draft Status Report" of Project A in the PMO system.
- **Blocker Alerts:** Immediate notification if a Contributor tags a "Blocker" against the PM's project.
- **Resource Health:** View of the aggregate "Workload Score" for the team members assigned to their project (anonymized to protect individual privacy if required).

------

## 4. Key Data Elements & Metrics

### Contributor Scorecard (Productivity Metrics)

- **Reliability Index (%):** Calculates the ratio of items listed in "Next Steps" (Week 1) that were marked as "Accomplished" (Week 2).
- **Submission Streak:** Tracks consistency of weekly reporting.
- **Contribution Ratio:** Analyzes the volume of "Project Work" vs. "BAU/Internal Work." (e.g., Is this Senior Dev spending 50% of their time on IT support instead of coding?).

### The "Burnout" Indicators

- **Sustained Load:** Flags users who report a Workload Score of 4+ for 3 consecutive weeks.
- **Sentiment Dip:** Flags a sudden drop in Sentiment Score (e.g., Happy $\rightarrow$ Frustrated) for intervention.

------

## 5. Integration Architecture

### Data Flow: PMO Tool $\leftrightarrow$ Team Platform

1. **Inbound (Context):** The Team Platform pulls `Project_Assignments` from the PMO Tool to configure the user's weekly form.
2. **Outbound (Reporting):** The Team Platform pushes `Project_Update_Text` and `Blockers` back to the PMO Tool, tagged to the specific Project ID.

### Privacy Barriers (The "Trust" Layer)

- **Rule 1:** Project Managers *cannot* see the "Private Note" sent to the Line Manager.
- **Rule 2:** Project Managers *cannot* see updates related to *other* projects. (i.e., PM of Project A cannot see the user complaining about Project B).
- **Rule 3:** Line Managers see *everything*.

------

## 6. Technical Specifications

- **Reminder Engine:** Automated Slack/Email nudges sent Friday morning if the pulse is incomplete.
- **Draft Mode:** Allows users to log items daily (as a scratchpad) and submit the final bundle on Friday.
- **Historical Archive:** Users retain permanent access to their own submission history, serving as a "Career Log" for resume building.

------

## 7. Summary of Value

- **For the User:** "I only report once." (Eliminates triple reporting).
- **For the PM:** "I get raw updates from the source without chasing people."
- **For the Line Manager:** "I know who is burning out and who is productive."
- **For the Org:** "We can finally measure the cost of internal/BAU work."