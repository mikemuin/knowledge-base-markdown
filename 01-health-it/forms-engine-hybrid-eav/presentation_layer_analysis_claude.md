# Presentation Layer & Modular Hierarchy — Deep Analysis

Let me now build a comprehensive analysis document focused entirely on these two areas.

------

## 1. Overall Assessment

Your presentation layer thinking is **architecturally coherent but strategically underspecified** in several areas that will become expensive to retrofit later. The good news is that your instincts are consistently pointing in the right direction — reusable widgets, singular anchor points, fresh per-placement configuration, schema-driven logic. What's missing is the **formal specification** of how these concepts connect into a buildable system. This analysis will give you that connective tissue.

------

## 2. The Widget Library as Your Clinical Data Dictionary Made Tangible

Your decision to make widgets **reusable and shareable across forms and modules** is the single most impactful architectural decision in your presentation layer. It means your widget library is not just a UI component library — it is a **living, versioned catalog of every clinical data point your platform can capture**, each one linked to a concept, a data type, a terminology code, and a set of default behaviors.

This reframes how you should think about widget creation. A developer doesn't just build a UI component — they are **registering a new clinical variable into your system**. That act should be treated with the same governance seriousness as adding a new concept to your terminology service. In fact, widget creation and concept registration should be a **single unified workflow**, not two separate acts.

**Recommended Widget Registry Model:**

```
widgets
- id
- name                        ← "Blood Pressure Widget"
- slug                        ← blood_pressure_vital
- widget_type                 ENUM(atomic, composite)
- concept_id                  ← FK to concepts (for atomic)
- data_type                   ENUM(text, numeric, coded, boolean, datetime, composite)
- default_anchor_point        ENUM(patient, encounter, module)
- is_repeatable               BOOLEAN  ← can it render multiple rows?
- json_schema                 JSONB    ← structure, validation, default logic
- status                      ENUM(draft, active, deprecated)
- created_by
- version_number
- superseded_by_widget_id     ← self-referential for versioning
```

Every widget in your system traces back to this registry. When a developer places a widget into a form, they are creating a **widget placement instance** — a join record that inherits from this registry but carries its own per-placement configuration for permissions, lifecycle, and conditional logic.

------

## 3. Formalizing the Compositional Tree — Your Flutter-Inspired Model

Your instinct about widget-within-widget deserves to be formalized because it resolves several design tensions simultaneously. Here is how I recommend you think about it:

**Two distinct widget roles exist in your system:**

A **Container Widget** is a structural parent. It groups child widgets into a logical clinical unit — a Vital Signs Panel, a Medication Entry Block, a Physical Examination Section. It carries no clinical data itself. It has no concept linkage, no anchor point, no lifecycle state. It exists purely to define composition and visual structure. Think of it as a named slot that holds other widgets.

A **Leaf Widget** is the atomic clinical data capture unit. It has a concept linkage, a data type, an anchor point, permissions, a lifecycle state machine, and conditional visibility logic. It is where data actually lives. All governance — permissions, lifecycle, approval workflows — lives exclusively at this level.

This maps cleanly to your Flutter intuition. The widget tree is a **structural concern**. Governance is a **leaf concern**. Never mix the two.

```
widget_compositions
- id
- parent_widget_id            ← Container Widget
- child_widget_id             ← Leaf or another Container
- display_order
- is_repeatable               ← repeating child rows (e.g. medication list)
- min_occurrences
- max_occurrences
```

A repeatable child container is how you handle the **medication table problem** — a clinician adds multiple rows of the same Medication Entry composite widget within a single form submission. Each row is a separate JSONB document in your storage layer, linked by a `group_instance_id` that preserves their logical grouping.

------

## 4. Anchor Points — Formalizing Singular Ownership

Your decision to keep anchor points singular and mutually exclusive is correct. But it needs one additional design element to handle the scenario you implicitly identified — an encounter diagnosis that should also appear on the longitudinal problem list.

The solution is not dual-anchor widgets. The solution is a **clinical promotion workflow** — a deliberate, auditable action where an authorized clinician explicitly promotes an encounter-anchored observation into the patient's longitudinal record. This keeps your data lineage clean while supporting the clinical reality.

```
widget_placements
- id
- widget_id                   ← FK to widget registry
- form_id
- section_id
- anchor_point                ENUM(patient, encounter, module)  ← per placement
- display_order
- is_required
- conditional_logic           JSONB  ← skip logic, visibility rules
- lifecycle_config            JSONB  ← state machine config for this placement
- permission_config           JSONB  ← role and context rules for this placement
- version_number              ← placement config versioning
```

Note that `anchor_point` lives on the placement record, not the widget definition. The widget registry declares a **default** anchor point, but each placement can override it. A Blood Pressure Widget defaults to encounter-anchored in most forms but could be placed as patient-anchored in a chronic disease monitoring module.

------

## 5. The Lifecycle State Machine

Your openness to a formal lifecycle including approval by others is clinically important. In regulated environments like registries, data provenance and approval chains are not optional — they are audit requirements. Here is a recommended state machine that balances clinical reality with regulatory need:

**Draft → Submitted → Under Review → Verified → Amended → Locked**

Draft is created automatically when a clinician opens a widget for data entry. Submitted occurs when the clinician saves and commits the data. Under Review is triggered when the widget's lifecycle config requires countersignature or peer verification. Verified is the approved, trustworthy state used by analytics. Amended creates a new version of the submission while preserving the original — the original is never mutated. Locked is a terminal state for regulatory or legal hold purposes.

**Critical design rule:** Your analytics layer — materialized views, data marts, AI feature pipelines — should **only consume Verified or Locked submissions** by default. Draft and Submitted data should be explicitly opted into for operational dashboards only. This single rule protects your entire analytical layer from dirty data contamination.

```
widget_submissions
- id
- widget_placement_id
- patient_id
- encounter_id                ← null if patient or module anchored
- module_enrollment_id        ← null if patient or encounter anchored
- schema_version_id           ← immutable link to widget schema at capture time
- submitted_data              JSONB
- lifecycle_status            ENUM(draft, submitted, under_review, verified, amended, locked)
- previous_submission_id      ← self-referential FK for amendment chain
- submitted_by
- verified_by
- submitted_at
- verified_at
- locked_at
```

------

## 6. Permissions — Role-Based Plus Contextual Rules

Your decision to support both role-based and contextual permissions is the right clinical call but needs a concrete implementation model to avoid becoming an unmaintainable tangle of if-else logic.

**Recommended Pattern — Permission Rule Engine in JSONB:**

Rather than hardcoding permission logic, store permission rules as structured JSONB on the widget placement record. Your Angular frontend and Laravel backend both evaluate the same rule structure, giving you consistent enforcement at both layers.

A permission rule has three components — a subject (who), a condition (when), and an action (what they can do). For example: a nurse role can submit but not verify; a widget becomes read-only after lifecycle status reaches verified; a widget is hidden entirely unless a parent question answer equals a specific coded value.

```json
{
  "rules": [
    {
      "subject": { "role": "nurse" },
      "condition": { "lifecycle_status": ["draft", "submitted"] },
      "actions": ["view", "edit", "submit"]
    },
    {
      "subject": { "role": "physician" },
      "condition": { "lifecycle_status": ["submitted", "under_review"] },
      "actions": ["view", "verify"]
    },
    {
      "subject": { "role": "any" },
      "condition": { "lifecycle_status": ["verified", "locked"] },
      "actions": ["view"]
    }
  ]
}
```

This JSONB rule structure is evaluated by a **Permission Rule Engine** — a dedicated Laravel service class that takes the current user's role, the current widget lifecycle status, and the current form context and resolves what actions are permitted. This engine is called by both your API controllers and can be serialized to your Angular frontend for pre-render permission checks.

------

## 7. Schema-Driven Conditional Logic

Your decision to embed form logic in the JSON schema is correct for a developer-built form environment. Angular has mature libraries for this — **Angular Formly** is the closest match to what you're describing and deserves serious evaluation. It is a metadata-driven form rendering engine where the entire form definition, including field types, validation rules, conditional visibility, and layout, lives in a JavaScript/TypeScript object that maps cleanly to a JSON schema stored in your database.

**Recommended Conditional Logic Schema Structure:**

```json
{
  "conditions": [
    {
      "type": "visibility",
      "target_widget_placement_id": "wp_456",
      "rule": {
        "source_widget_placement_id": "wp_123",
        "operator": "equals",
        "value": "concept_id_positive"
      }
    },
    {
      "type": "required",
      "target_widget_placement_id": "wp_789",
      "rule": {
        "source_widget_placement_id": "wp_123",
        "operator": "greater_than",
        "value": 140
      }
    }
  ]
}
```

Critically — conditional logic must be **enforced on the backend**, not just the frontend. A clinician bypassing the Angular UI and submitting directly via API should not be able to violate conditional rules. Your Laravel submission endpoint must re-evaluate the same conditional logic schema before persisting any widget submission. This dual enforcement is non-negotiable for data integrity.

------

## 8. The Form Rendering Pipeline

Given Angular on the frontend and Laravel on the backend, here is the recommended rendering pipeline for a form:

When a clinician navigates to a form, Angular requests the **Form Manifest** from your Laravel API. This manifest is a single structured JSON document that contains the complete module and form hierarchy for that context — all pages, sections, widget placements, their schemas, their conditional logic rules, their permission configs, and their lifecycle states. Angular's form rendering engine consumes this manifest and builds the reactive form dynamically. No hardcoded form templates exist in your Angular codebase — every form is a runtime rendering of a manifest.

This means adding a new form, modifying a section, or updating conditional logic **requires zero frontend deployment**. A developer updates the database records, the manifest changes, and the next form load reflects the new definition. This is your single most powerful architectural advantage in the presentation layer.

------

## 9. Recommendations Summary

**Formalize the Container versus Leaf widget distinction** immediately. This resolves your widget-within-widget uncertainty cleanly and gives developers a clear mental model for building the widget library.

**Evaluate Angular Formly seriously** as your form rendering engine before building a custom one. It maps almost perfectly to your schema-driven, metadata-first approach and has a mature ecosystem.

**Build the Permission Rule Engine as a standalone Laravel service** that is consumed by both API controllers and serialized for frontend use. Never duplicate permission logic between layers.

**Enforce conditional logic on the backend** regardless of frontend enforcement. Treat every API submission as potentially coming from a non-UI client.

**Restrict analytics layer consumption to Verified and Locked submissions only** as a platform-wide default. Make this a documented architectural rule, not just a convention.

**Design the Form Manifest API as your primary frontend contract** — a single endpoint that returns the complete, context-aware form definition. This decouples your Angular rendering engine from your database schema evolution.

**Treat widget creation as a governed clinical act** equivalent to concept registration. Establish a widget governance process before your widget library grows beyond a handful of components.

------

## 10. Final Verdict

Your presentation layer has the bones of a genuinely powerful, clinically intelligent form engine. The reusable widget library, the compositional tree model, the per-placement configuration, and the schema-driven logic together form a coherent and scalable design philosophy. What it needs now is formalization — specifically the Container versus Leaf distinction, the Permission Rule Engine specification, the lifecycle state machine definition, and the Form Manifest API contract. Resolve these five things and your presentation layer becomes not just buildable but maintainable and extensible for years of clinical evolution.