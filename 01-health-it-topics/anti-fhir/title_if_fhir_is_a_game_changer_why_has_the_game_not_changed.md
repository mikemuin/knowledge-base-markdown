# Title: If FHIR Is a Game Changer, Why Has the Game Not Changed?

For over a decade, HL7 FHIR (Fast Healthcare Interoperability Resources) has been hailed as a revolutionary standard poised to transform healthcare data exchange. But more than ten years since its inception, many in the global health IT community are asking a critical question: *If FHIR is such a game changer, why hasn’t the game changed?*

This article examines that question from a global lens, particularly outside the U.S., with a specific focus on the Philippine context. Drawing from field experience, practical implementation realities, and the voice of implementers, we explore the promises, pitfalls, and practical alternatives to FHIR.

------

### 1. FHIR Has Been Touted as a Game Changer — But Has It Delivered?

FHIR has been in development since 2011 and widely promoted following U.S. legislation like the 21st Century Cures Act. Yet, real transformation has not followed. Outside the United States, adoption remains shallow, typically limited to pilot projects, research institutions, or vendor-specific implementations. In the Philippines, most healthcare providers and national agencies have little to no active FHIR usage.

------

### 2. Existing Standards Like v2, XML, JSON Are “Good Enough”

Healthcare systems globally continue to rely on HL7 v2, flat files, and basic JSON or XML APIs. HL7 v2 supports semantic interoperability through terminologies like LOINC, SNOMED, and ICD, and it's already integrated into existing workflows. For most integration tasks, especially in settings with constrained resources, these standards are easier to implement, maintain, and extend.

------

### 3. A Game Changer Should Provide 10x Value — Has FHIR?

True game-changing technologies (like AI or blockchain) tend to provide exponential improvement. FHIR, however, introduces significant complexity and requires upskilling, infrastructure investment, and new governance layers without clear immediate benefits. While it may enable long-term innovation (e.g., app marketplaces, CDS Hooks), these use cases are years away from being relevant in many settings.

------

### 4. Global Adoption Faces Structural Barriers

Many low- and middle-income countries (LMICs), including the Philippines, face deep-rooted barriers that FHIR does not solve: weak digital infrastructure, lack of funding, fragmented governance, and limited availability of FHIR-skilled developers. Without regulatory mandates or national frameworks enforcing its adoption, FHIR remains optional and often unnecessary.

------

### 5. FHIR Is Just a Standard — Not a Solution

FHIR should be seen as a technical standard, not a solution to interoperability in itself. It does not resolve the fundamental problems of incentive misalignment, system fragmentation, or cultural resistance to data sharing. Like TCP/IP, FHIR is best when it quietly powers a solution, not when it becomes the focus of the solution.

------

### 6. 80% of Interop Needs Are Better Served by Simpler Tools

Most healthcare interoperability challenges are centered around a small set of high-volume workflows: admissions, discharges, transfers, lab results, billing. These are event-driven, not resource-centric, and are better served by HL7 v2 messages, JSON-based APIs, or even CSV file transfers. FHIR may address edge cases well, but it adds disproportionate complexity to the core needs.

------

### 7. In Real-World Experience, FHIR Often Slows Projects Down

From actual implementation experience, particularly in the Philippines, forcing FHIR into projects has often led to delays, confusion, and increased costs. Custom-built APIs and HL7 v2 interfaces continue to outperform FHIR in terms of speed-to-implementation and developer accessibility.

------

### 8. Why FHIR Is Being Pushed Globally

The momentum behind FHIR is largely driven by:

- **U.S. regulatory mandates** (e.g., ONC rules)
- **Global EHR vendors** (Epic, Oracle Cerner)
- **Consulting firms** selling FHIR transformation services
- A perception that FHIR represents modernization

However, in many global contexts, this enthusiasm precedes actual use cases and capacity.

------

### 9. What Should Be the Philippine Strategy for 2025?

Rather than prioritize FHIR, the Philippines should focus on:

1. **HL7 v2** for high-volume, event-driven exchanges (e.g., ADT, labs).
2. **JSON APIs** for developer-friendly, custom integrations.
3. **CSV uploads** for programmatic reporting and low-tech environments.
4. **Event-driven architectures** using simple notifications or webhooks.

FHIR may be introduced selectively for specific international interoperability or long-term innovation initiatives, but it should not be mandated prematurely.

------

### Conclusion: Let’s Win the Game We’re Actually Playing

FHIR is not flawed. But in the global South and other under-resourced contexts, its complexity, cost, and misalignment with current needs make it an inefficient choice for the bulk of interoperability challenges. It's time to de-hype FHIR and focus on strategies that deliver practical, scalable results now.

FHIR may yet change the game. But today, we need to focus on **winning the game we're actually playing**.