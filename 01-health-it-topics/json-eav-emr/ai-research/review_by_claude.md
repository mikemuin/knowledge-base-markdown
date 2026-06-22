# JSON-EAV model Review

> I want a review of this approach to build a Clinical Documentation Module/Application for hospital-wide EMR.
>
> - There is a Presentation and Canonical JSON Storage layer. This layer allows multi-dimensional clinical forms and saves them to JSON
> - There is a second Ingestion and Domain Modeling layer. This is a projection layer of the canonical JSON.
> - Initial ingestion is through EAV tables. The EAV tables are data-typed according to the different DV classes of OpenEHR.
> - Not all canonical JSON document fields are projected. Only select the important ones, especially those that need to be tracked, computed, and retrieved in other modules.
> - These projected data in EAV tables are grouped or anchored as concepts, so that they can be extracted as a group
> - A secondary set of tables in the ingestion layer is for operational use. They are normalized tables projected directly from JSON, but probably more from the EAV tables. These secondary sets are defined after the forms are developed.

This is a thoughtful, layered architecture. Let me give you a thorough evaluation before rendering the diagram.

**Overall verdict: strategically sound with some important risks to manage.** The core idea — JSON as the system of record, EAV as a typed semantic projection, and normalized tables as a purpose-built operational layer — is a mature pattern that mirrors what institutions like openEHR reference implementations actually do in practice. Let me break it down layer by layer.

**Presentation and Canonical JSON layer** is the right choice for multi-dimensional clinical forms. JSON tolerates schema evolution gracefully, handles nested structures (vital signs with sub-observations, medication entries with compound dosing instructions), and decouples the form renderer from the persistence layer. The key risk here is JSON schema governance: without versioning discipline, your "canonical" JSON will silently diverge across form versions, and you will not know until a downstream projection breaks. You need mandatory schema versioning embedded in the document itself — not just in a registry.

**EAV projection with openEHR-typed columns** is genuinely clever. Using the openEHR data value hierarchy (DV_QUANTITY, DV_CODED_TEXT, DV_DATE_TIME, etc.) as your type discriminator solves the classic EAV type-safety problem without forcing a single generic `string_value` column that loses fidelity. This gives you the flexibility of EAV while preserving the semantic precision of a structured model. The risk is query complexity at scale: joining across attribute rows to reconstruct a concept group will become expensive as volume grows. You'll need to be disciplined about indexing on `archetype_node_id` or equivalent concept anchors, and you should plan for materialized concept snapshots early — not as an afterthought.

**Concept-anchored grouping** is the right abstraction. This is essentially implementing openEHR's CLUSTER pattern in your projection layer — grouping related attributes under a common anchor (e.g., a blood pressure reading as a concept containing systolic, diastolic, position, cuff size). This makes extraction tractable. The discipline to enforce is that concept boundaries must be defined in metadata, not inferred at query time. If your data scientists have to reverse-engineer what belongs to a concept, you've lost the value of the grouping.

**Selective projection** is pragmatically correct but needs a governance process. Deciding which fields "matter" is a clinical and analytical judgment that will be revisited constantly. You need a formal mechanism for adding new fields to the projection without retroactively breaking existing ones — additive-only schema evolution on the EAV side. Fields not projected are not lost (they're in JSON), but they become second-class citizens that require ad-hoc JSON parsing to access. Document this boundary explicitly for downstream consumers.

**Operational normalized tables defined post-form-development** is where I'd raise the most caution. This is pragmatically understandable — you can't fully normalize what you haven't designed yet — but it creates a sequencing dependency that tends to slip. In practice, the operational tables end up being defined late, inconsistently, or not at all, and the EAV layer gets used directly for things it wasn't optimized for. I'd recommend defining at least the *contract* for operational tables (what queries they must answer, what latency they must achieve) before form development is complete, even if the physical implementation follows.

One architectural gap worth noting: there's no explicit mention of a temporal or audit model. Clinical data has a mandatory notion of time — not just when a value was recorded, but when it was observed, when it was corrected, and what version of the form definition was active at that moment. If this is implicit in the JSON, it needs to be explicit in the EAV projection for any longitudinal or regulatory use.Here is a consolidated assessment of the specific design decisions, with recommendations:

**What is working well**

The architecture correctly identifies JSON as the right persistence vehicle for the capture layer — it tolerates the sparse, irregular, and evolving nature of clinical forms without forcing premature normalization. Using openEHR's DV type hierarchy as a discriminator in EAV columns is particularly strong; it gives you a type-safe, semantically meaningful column layout that most EAV implementations lack. Concept-anchored grouping is the right abstraction for making EAV tractable downstream.

**What needs strengthening**

The first concern is temporal modeling. Clinical data is fundamentally bitemporal — there is the time of observation, the time of recording, and the time of any correction or amendment. None of these are mentioned explicitly. Your EAV projection should include `observation_datetime`, `recorded_datetime`, and a row-level `valid_from`/`valid_to` for corrections. Without this, longitudinal analysis and regulatory audit become extremely difficult to reconstruct from the JSON alone.

The second concern is the "post-form-development" timing for operational tables. In practice this slips, and teams end up querying EAV directly for operational workloads it was never optimized for. A better discipline is to define the *query contract* — what the operational tables must answer — as part of form acceptance criteria, even if physical implementation follows. This keeps the data team engaged during form design rather than retroactively chasing it.

The third concern is projection governance. "Select the important ones" needs to become a formal, versioned decision. A field that is not projected today may be critical to a study three years from now, and the absence of that data from the projection layer will only be discovered late. Consider maintaining a projection manifest — a machine-readable register of which JSON paths are projected, why, and when the projection was added — so the gap between JSON and EAV is always visible and auditable.

**The architectural gap most worth addressing**

There is no explicit ETL/ELT pipeline described between layers. The phrase "projected from JSON" implies a transform step, but the trigger, frequency, error handling, and reconciliation strategy for that step are undefined. For a hospital-wide EMR, this pipeline is not an implementation detail — it is a core reliability concern. A document saved to JSON that fails projection silently will not appear in operational queries, which in a clinical context can affect patient safety workflows. Define the projection pipeline as a first-class component with dead-letter handling and reconciliation checks.

Would you like me to go deeper on any specific layer — for example, the EAV schema design, the concept anchoring metadata model, or the ETL pipeline architecture?
