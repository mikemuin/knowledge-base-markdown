# Team Pulse

This is a classic problem in matrix organizations. The **PMO Tool** (Governance) gives you the "Project Vertical" view, but you are completely blind to the "People Horizontal" view.

In a matrix, a Team Member (Contributor) reports to a **Line Manager** (Functional Boss) but works for multiple **Project Managers** (Delivery Bosses).

If you build this counterpart tool, it must be the **Inverse** of the PMO tool.

- **PMO Tool:** One Project $\rightarrow$ Many Contributors.
- **Team Tool:** One Contributor $\rightarrow$ Many Projects.

Here is the concept for the counterpart product. Let's call it **"TeamPulse."**

------

# Product Concept: TeamPulse

**Tagline:** The Weekly Ritual for High-Performance Teams.

## 1. The Core Philosophy

The goal is to solve the "Triple Reporting" fatigue where a user has to update PM A, PM B, and their Line Manager separately.

**The "Slice and Dice" Logic:**

The user fills out *one* form on Friday afternoon. The system acts as a prism:

- **Project Manager A** sees only the bullets related to Project A.
- **Project Manager B** sees only the bullets related to Project B.
- **Line Manager** sees the **Total View** (Projects A + B + Internal Work + Morale + Career Goals).

------

## 2. The Modules (The Contributor's View)

The interface is simple, designed to be completed in 15 minutes or less.

### A. The "Work Stream" (The Meat)

Instead of asking "What did you do?", the system pulls the projects the user is assigned to (from the PMO tool) and creates sections.

- **Section 1: Project A (Integration)**
  - *Accomplished:* (User types bullets)
  - *Next Week:* (User types bullets)
  - *Blockers:* (User types bullets)
- **Section 2: Project B (Integration)**
  - *Accomplished / Next Week / Blockers*
- **Section 3: BAU & "Keep the Lights On"**
  - *Context:* This is crucial. In matrix orgs, people spend 30% of their time on emails, support tickets, and internal meetings. If you don't track this, you can't measure true productivity.
  - *Inputs:* "Fixed server patch," "Interviewed 3 candidates," "Helped Junior Dev."

### B. The "Highlights" (The Brag Sheet)

- **Prompt:** "What is the one thing you are most proud of this week?"
- **Purpose:** This feeds into performance reviews at the end of the year. It forces the user to articulate their value/contribution.

### C. The "Pulse" (Morale & Capacity)

- **Capacity Check:** "How was your workload this week?"
  - *Scale:* Underutilized (1) $\leftrightarrow$ Overwhelmed (5).
- **Sentiment:** "How are you feeling?" (Emoji based).
- **Private Note to Line Manager:** A text field visible *only* to the Line Manager, not the PMs.
  - *Use Case:* "Project A is disorganized and stressing me out."

------

## 3. The Management Views (The Output)

### A. The Line Manager's Dashboard (People View)

This dashboard allows a Functional Manager (e.g., Head of Marketing) to see their 10 direct reports.

- **The "Burnout Radar":** A heatmap showing the "Workload" scores of their team. If 3 people signal "Overwhelmed" for 2 weeks straight, the Manager knows to intervene.
- **The "Blocker Scanner":** Aggregates all "Blockers" from the team.
  - *Insight:* "Why are 5 of my people blocked by 'VPN Access'?"
- **Productivity Consistency:** Tracks the "Say/Do Ratio." (Did they accomplish what they said they would do last week?).

### B. The Integration with the PMO Tool (Project View)

This is where the magic happens. The PM doesn't log into "TeamPulse."

- When the Contributor hits submit, their update for "Project A" is **pushed** automatically into the **"Project Journal"** or **"Status Report"** draft of the PMO Tool.
- **Benefit:** The PM writes their Executive Report using raw text copy-pasted directly from the people doing the work.

------

## 4. Measuring "Productivity & Contribution"

To monitor productivity without being invasive (no screen recording), you measure **Output** and **Reliability**.

**The "Contributor Scorecard" (Weekly Logic):**

1. **Volume:** Did they have updates for their assigned projects? (Or did they leave it blank?)
2. **Reliability:** Did they check off the items they listed in "Next Week" from the previous report?
3. **Impact:** How many times did they fill out the "Highlight" section?

------

## 5. Consultant's Critique & Risk Assessment

As a consultant, here is why this specific design works for a Matrix Organization, and where it might fail.

### The Win: "The Protection Mechanism"

In a matrix, contributors often get abused. Project A wants 60% of their time, and Project B wants 60% of their time. That's 120%.

- **This tool exposes that.** If a user lists massive updates for both, the Line Manager (who sees the Total View) can see the overload and say: *"Stop. I need to pull you off Project B."*
- Without this tool, the Line Manager has no idea the employee is drowning until they quit.

### The Risk: "The Timesheet Trap"

- **The Danger:** If you ask for "Hours Spent" (e.g., 4 hours on Project A), this tool dies. Users hate timesheets. They will lie.
- **The Fix:** Stick to **Narrative Updates** ("I built the login page") rather than **Time Tracking** ("I spent 4 hours"). Focus on *Value Delivered*, not *Time Served*.

### The Missing Link: Objectives (OKRs)

For V2, you would want to link these weekly updates to quarterly goals (OKRs).

- *Feature:* When a user writes a "Highlight," they tag it to a personal goal (e.g., "Improve Code Quality").
- *Benefit:* Performance reviews become effortless because the data is collected weekly all year long.

### Summary

- **PMO Tool:** Governance, Budget, Risk, RAG Status. (The Macro View).
- **TeamPulse:** Morale, Workload, Narrative Updates, Blockers. (The Micro View).

Combined, they give the organization a complete picture of **Health** (Is the project green?) and **Sustainability** (Are the people burning out?).