# Paste In:

### X. Execution (Delivery Tracking)

Execution is for *doing the work* and tracking commitments. All items support attachments/links and soft-delete.

#### A. Milestones
- **Purpose:** Track key project checkpoints and dates.
- **Minimum fields:** Title, Target Date, Status, Notes (optional), Attachments/Links (optional)
- **Status values (Milestones):**
  - pending
  - on_track
  - at_risk
  - delayed
  - completed
  - cancelled

#### B. Deliverables
- **Purpose:** Track major outputs being produced by the project.
- **No approval workflow** in V1 (no “in review/approved/rejected”).
- **Minimum fields:** Title, Target Date, Status, Notes (optional), Attachments/Links (optional)
- **Status values:** Use the same list as Milestones (pending/on_track/at_risk/delayed/completed/cancelled).

#### C. Action Items
- **Purpose:** Track discrete to-dos arising from execution and meetings.
- **Rules:**
  - **Target Date is required** (not “due date”).
  - **Owner is optional**.
- **Minimum fields:** Title, Target Date (required), Owner (optional), Status, Notes (optional), Attachments/Links (optional)
- **Status values (Action Items):**
  - pending
  - on_track
  - at_risk
  - delayed
  - blocked
  - completed
  - cancelled

#### D. Dependencies
- **Purpose:** Track dependencies that can impact delivery.
- **Dependency Type (required):**
  - internal
  - external
  - vendor
  - regulatory
  - technical
- **Fields:** “Almost the same as Action Items”:
  - Title
  - Dependency Type
  - Target Date (required)
  - Owner (optional)
  - Status (use Action Item statuses)
  - Notes (optional)
  - Attachments/Links (optional)
  - Optional “Related Party/Project” reference (free-text or link) for vendor/external/internal context

#### E. Meeting Events
- **Rules:**
  - **No recurring meetings** in V1.
  - **All meeting creation is manual**.
  - **No automation** (no auto-flagging for missed meetings; any “critical” indicator is informational only).
- **Fields:** Keep the existing **Data Model** fields for Meeting Events as-is in V1 UI.