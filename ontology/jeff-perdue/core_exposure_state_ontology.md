# Core Exposure-State Ontology
**Project:** NomNomSafe Ontology Research
**Phase:** 1 — Core Exposure-State Ontology
**Deliverable:** R3.3
**Location:** `docs/individual/jeff-perdue/ontology/core_exposure_state_ontology.md`

---

## Introduction

This document defines the Phase 1 Core Exposure-State Ontology for the NomNomSafe allergen reasoning research project. It is the foundational artifact of Phase 1 and the primary reference for all subsequent phase work.

The ontology is designed to solve a specific and well-defined problem: current AI systems reasoning about allergen safety lack a formal mechanism that requires evidence sufficiency before permitting a safety inference. Phase 0 established this empirically. Both models tested demonstrated adequate knowledge of allergen risks but produced responses whose safety conclusions were prompt-dependent rather than structurally enforced. The ontology defined here is the architecture that makes structural enforcement possible.

The design follows three principles stated in the research roadmap. First, evidence-based safety: safety is never inferred from absence alone, and must require positive structural evidence. Second, layer separation: the ontology defines what exists and how exposure works, independently of how conservative any application chooses to be in using that information. Third, graceful handling of incomplete data: when information is insufficient to establish a safety conclusion, the system represents that insufficiency explicitly rather than collapsing it into either a safe or unsafe default.

The ontology is intentionally minimal. It defines only the concepts and relationships necessary to support justified safety inference and block unjustified inference. Complexity is added in later phases when it is warranted by evidence — not anticipated in advance.

---

## Part 1: Core Concepts

### FoodItem

A `FoodItem` is a prepared dish or product as it appears on a menu or is offered for consumption. It is the primary subject of any allergen safety assessment. A `FoodItem` is not an abstract recipe — it is a specific preparation as it would be produced in a specific kitchen environment. Two dishes with the same name at different restaurants are distinct `FoodItem` instances because their preparation contexts, ingredient sourcing, and kitchen environments may differ.

A `FoodItem` has:
- A name or description as it appears on the menu
- A set of intended `Ingredient` components
- A `PreparationContext` describing how it is made and in what environment
- An `ExposureState` for each `Allergen` being assessed

### Ingredient

An `Ingredient` is a component intentionally included in a `FoodItem` by the kitchen. Ingredients are the intended compositional elements of a dish — what the kitchen means to put in. They are distinct from substances that enter a dish through cross-contact, which are modeled separately through `CrossContactSource`.

An `Ingredient` may itself be a compound — a sauce, a marinade, a spice blend — whose full composition may not be disclosed in the menu description. The ontology treats an `Ingredient` whose composition is not fully known as a source of `Unknown` `ExposureState` for any allergen that could plausibly be present in that compound.

An `Ingredient` contains one or more `Substance` instances.

### Substance

A `Substance` is a physical material contained within an `Ingredient`. Substances are the compositional level at which allergen presence is determined. An `Ingredient` described as "house dressing" contains substances — oils, acids, emulsifiers, flavor compounds — some of which may be `Allergen` instances.

The `Substance` level exists to support precision in allergen reasoning. A dish may contain an `Ingredient` that does not sound allergenic but contains an allergenic `Substance` — peanut oil in a vegetable marinade, egg yolk in a hollandaise described only as "rich sauce," anchovies in a Caesar dressing described as "housemade." The `Substance` node makes these relationships representable.

### Allergen

An `Allergen` is a type of `Substance` capable of causing an allergic reaction in a sensitized individual. The ontology does not attempt to enumerate all possible allergens. For practical application, it operates against the set of major food allergens defined by applicable regulation — in the United States, the nine major allergens identified under the FASTER Act: milk, eggs, fish, shellfish, tree nuts, peanuts, wheat, soybeans, and sesame.

An `Allergen` is the unit against which `ExposureState` is assessed. Each allergen assessment for a given `FoodItem` is independent: a dish may be `ConfirmedAbsent` for peanuts and `Unknown` for tree nuts simultaneously.

### PreparationContext

`PreparationContext` is the description of how a `FoodItem` is prepared, including the cooking method, the ingredient incorporation sequence, the culinary conventions associated with named dish types, and the physical environment in which preparation occurs.

`PreparationContext` is a required dimension of any allergen safety assessment. It captures risks that are invisible in the ingredient list — allergens that enter a dish through the cooking process itself rather than through intended ingredient composition. A pan-seared dish is basted during cooking. A risotto is finished through mantecatura. A wok-fried dish is seasoned with sauces that may not appear in the menu description. These are preparation-context risks: they exist because of how the dish is made, not only because of what it contains.

`PreparationContext` also governs how modification requests are evaluated. A modification request changes one or more elements of a `FoodItem`'s preparation. Whether that change is sufficient to affect `ExposureState` depends on whether it reaches and resolves the preparation-context dimension of the allergen risk — not only the ingredient-composition dimension. This rule is stated precisely in Part 3.

### CrossContactSource

A `CrossContactSource` is a shared piece of equipment, a shared cooking surface, a shared preparation environment, or any other physical condition that may introduce an allergen into a `FoodItem` through contact rather than through intended ingredient inclusion. `CrossContactSource` is modeled as a distinct node because cross-contact risk is independent of ingredient composition — a dish can contain no allergenic ingredient and still present a real allergen risk through its preparation environment.

`CrossContactSource` instances include but are not limited to: shared fryers, shared grill surfaces, shared pasta water, shared cutting boards and utensils, shared prep surfaces, shared storage environments, and airborne allergen transfer in enclosed kitchen spaces.

The `CrossContactSource` associated with a `FoodItem` is determined by its `PreparationContext` — specifically, by which equipment and surfaces are used in its preparation and what other items are prepared on or in those same resources. A `CrossContactSource` assessment is a required step in any `ExposureState` determination and cannot be bypassed by confirming ingredient composition alone.

### ExposureState

`ExposureState` is the modeled relationship between a `FoodItem` and a specific `Allergen`. It is the central output of the ontology's reasoning process and the direct input to the Safety Inference Principle. Every allergen safety assessment produces an `ExposureState` value for each allergen being evaluated.

`ExposureState` takes one of four values, defined in Part 2.

---

## Part 2: Exposure States

For a given `FoodItem` F and `Allergen` A, `ExposureState(F, A)` takes one of the following four values.

### ConfirmedPresent

`ConfirmedPresent` is assigned when Allergen A is directly and positively included in `FoodItem` F through one or more of its intended `Ingredient` components, or when cross-contact introduction through a `CrossContactSource` has been positively confirmed.

`ConfirmedPresent` is an affirmative finding. It does not require uncertainty — it is the state in which exposure is known to occur. A dish containing peanut butter as a named ingredient has `ExposureState(peanuts) = ConfirmedPresent`. A dish prepared in a shared fryer where the cross-contact transfer has been confirmed has `ExposureState(relevant allergen) = ConfirmedPresent`.

`ConfirmedPresent` blocks safety inference. It is not a failure state — it is the correct and honest representation of a dish that contains an allergen.

### ConfirmedAbsent

`ConfirmedAbsent` is assigned when all introduction pathways for Allergen A into `FoodItem` F have been assessed and confirmed clear. This requires that three independent dimensions have each been evaluated and returned a negative finding:

1. **Ingredient Composition:** Allergen A is not present in any `Ingredient` or constituent `Substance` of the `FoodItem`, and the composition of all `Ingredient` components is sufficiently known to support this conclusion.
2. **CrossContactSource:** The `PreparationContext` of the `FoodItem` does not include any `CrossContactSource` that introduces Allergen A, and this has been confirmed rather than assumed.
3. **PreparationContext:** The preparation method does not introduce Allergen A through cooking process, technique, or culinary convention, including in cases where the allergen would be incorporated as a functional element of the preparation rather than as a listed ingredient.

`ConfirmedAbsent` is the only `ExposureState` value that permits a positive safety inference under the Safety Inference Principle. All three dimensions must be satisfied — satisfaction of one or two is insufficient. `ConfirmedAbsent` is an affirmative finding in the negative: it is not the absence of evidence of presence, but the presence of evidence of absence across all three introduction pathways.

### PotentiallyPresent

`PotentiallyPresent` is assigned when there is a plausible but unconfirmed introduction pathway for Allergen A into `FoodItem` F. This state captures risk that is real and structurally grounded but not yet confirmed. It arises when a `CrossContactSource` exists and has not been ruled out, when an `Ingredient`'s composition is partially known and the known portion is safe but the unknown portion could plausibly contain the allergen, or when the `PreparationContext` includes a culinary convention that typically involves the allergen but has not been confirmed to do so in this specific preparation.

`PotentiallyPresent` blocks safety inference. It is not equivalent to `ConfirmedPresent` — the risk is unconfirmed — but neither is it equivalent to `Unknown`. The distinction is that `PotentiallyPresent` implies a specific, identified pathway that requires investigation, whereas `Unknown` implies that the assessment has not been completed sufficiently to identify whether pathways exist.

"May contain" labeling, shared fryer environments that have not been confirmed exclusive, and preparation methods that canonically involve an allergen but have not been confirmed in a specific instance all map to `PotentiallyPresent`.

### Unknown

`Unknown` is the mandatory default `ExposureState` for any allergen that has not been assessed through a completed evidence pathway. It is not a conclusion — it is the absence of sufficient information to assign any of the three substantive states. `Unknown` arises when the ingredient composition is not sufficiently disclosed, when the `PreparationContext` is not known, when the `CrossContactSource` profile has not been evaluated, or when any combination of these conditions leaves the assessment incomplete.

`Unknown` blocks safety inference. Critically, `Unknown` is not equivalent to safety — it is the explicit representation of insufficient information. A menu description that does not mention an allergen does not produce `ConfirmedAbsent` for that allergen. It produces `Unknown`, because omission is not evidence of absence.

`Unknown` is the default state from which all assessments begin. The assessment process moves `ExposureState` from `Unknown` toward one of the three substantive states as evidence is gathered. If the evidence pathway is not completed, `ExposureState` remains `Unknown`.

---

## Part 3: Exposure Logic

The following rules define how `ExposureState` is determined for a given `FoodItem` F and `Allergen` A. The rules are evaluated in order. The first matching condition determines the state.

**Rule 1 — ConfirmedPresent (Ingredient):**
If Allergen A is directly included in any `Ingredient` of F, or is present in any `Substance` within an `Ingredient` of F, and this is positively known, then `ExposureState(F, A) = ConfirmedPresent`.

**Rule 2 — ConfirmedPresent (CrossContact):**
If a `CrossContactSource` associated with F's `PreparationContext` is confirmed to introduce Allergen A, then `ExposureState(F, A) = ConfirmedPresent`.

**Rule 3 — PotentiallyPresent:**
If Allergen A is not confirmed present through Rules 1 or 2, but a plausible and identified introduction pathway exists — an unconfirmed `CrossContactSource`, a partially known `Ingredient` composition that could plausibly contain the allergen, or a `PreparationContext` that canonically involves the allergen but has not been confirmed in this specific case — then `ExposureState(F, A) = PotentiallyPresent`.

**Rule 4 — ConfirmedAbsent:**
If Allergen A is not present through Rules 1 or 2, and no plausible introduction pathway exists under Rule 3, and all three evidence dimensions — ingredient composition, `CrossContactSource`, and `PreparationContext` — have been assessed and confirmed clear, then `ExposureState(F, A) = ConfirmedAbsent`.

**Rule 5 — Unknown (Default):**
If none of Rules 1 through 4 can be applied because the evidence pathway is incomplete — ingredient composition is not sufficiently known, `CrossContactSource` has not been assessed, or `PreparationContext` has not been evaluated — then `ExposureState(F, A) = Unknown`.

### State Precedence

When multiple rules could apply simultaneously — for example, when ingredient composition is confirmed clear but `CrossContactSource` has not been assessed — the rule producing the more conservative state takes precedence. `ConfirmedPresent` takes precedence over all other states. `PotentiallyPresent` takes precedence over `ConfirmedAbsent` and `Unknown`. `Unknown` takes precedence over `ConfirmedAbsent`. `ConfirmedAbsent` is only assigned when all three evidence dimensions have been positively evaluated and confirmed clear — it cannot be assigned by default or by partial assessment.

### The Modification Request Rule

A modification request from a diner — removing a topping, requesting an alternative preparation, asking for an ingredient to be left off — changes one or more elements of a `FoodItem`'s preparation. Whether a modification request changes the `ExposureState` for a given allergen depends on whether the modification reaches and resolves all introduction pathways for that allergen across all three evidence dimensions.

A modification request that addresses only the ingredient-composition dimension does not change `ExposureState` if the allergen is also introduced through `PreparationContext` or `CrossContactSource`. Requesting that butter be left off pan-seared scallops resolves the finishing garnish — an ingredient-composition element — but does not resolve the basting method, which is a `PreparationContext` element. `ExposureState` for dairy remains `PotentiallyPresent` until the preparation method itself is confirmed modified and the modification is confirmed applied cleanly.

A modification request can move `ExposureState` toward `ConfirmedAbsent` only when:
1. The modification addresses all identified introduction pathways across all three evidence dimensions.
2. The kitchen has confirmed that the modified preparation has been applied — the request has been received, understood, and executed in a way that eliminates the allergen from all pathways.
3. The resulting preparation is evaluated under the standard exposure logic and satisfies Rule 4.

A recorded modification request, without kitchen confirmation of execution, does not change `ExposureState`. The request is evidence of intent, not evidence of outcome.

---

## Part 4: Safety Inference Principle

A `FoodItem` F may be inferred safe for `Allergen` A if and only if:

**`ExposureState(F, A) = ConfirmedAbsent`**

All other `ExposureState` values — `ConfirmedPresent`, `PotentiallyPresent`, and `Unknown` — block safety inference. There is no conditional or provisional version of a safety conclusion. A safety inference is either supported by a completed evidence pathway terminating in `ConfirmedAbsent`, or it is not permitted.

This principle enforces the evidence-based safety design requirement at the output level. It means that correct knowledge of allergen risks — knowing that hollandaise contains eggs, that shared fryers transfer allergens, that wok-fried dishes are typically seasoned with soy sauce — is necessary but not sufficient for a safety conclusion. The knowledge must be assembled into a completed assessment across all three evidence dimensions, and that assessment must terminate in `ConfirmedAbsent`, before a safety inference is permitted.

The Safety Inference Principle does not prohibit useful output when `ExposureState` is not `ConfirmedAbsent`. A system applying this ontology can and should communicate the current `ExposureState` value, the evidence that supports it, and the specific information that would be required to move toward `ConfirmedAbsent`. What it cannot do is produce a safety inference from an incomplete or non-confirming assessment.

---

## Part 5: Relationship to Phase 0 Findings

The four failure categories documented in the Phase 0 Allergen Safety Failure Taxonomy map directly onto structural gaps that this ontology is designed to close.

Overconfident Safety failures arose when models produced safety-implying opening framings before completing an evidence assessment. The Safety Inference Principle closes this gap by requiring a completed `ConfirmedAbsent` assessment before any safety inference is permitted — there is no partial or conditional safety conclusion.

Omission-as-Absence failures arose when models treated a missing allergen mention as evidence of absence. The `Unknown` default state closes this gap by requiring that allergen absence be positively confirmed rather than assumed from omission. An allergen not mentioned in a menu description has `ExposureState = Unknown`, not `ConfirmedAbsent`.

Cross-Contact Blindness failures arose when models ignored shared equipment and kitchen environment as independent allergen introduction pathways. The `CrossContactSource` node and its mandatory role in the `ConfirmedAbsent` pathway close this gap by requiring that cross-contact be assessed independently of ingredient composition before any safety inference is permitted.

Preparation-Context Blindness failures arose when models ignored how preparation method introduces allergen risk that is invisible in the ingredient description. The `PreparationContext` node and its mandatory role in the `ConfirmedAbsent` pathway close this gap by requiring that preparation-context risks — including those embedded in named culinary conventions — be assessed and confirmed clear before any safety inference is permitted. The Modification Request Rule extends this to diner-initiated changes: a modification does not change `ExposureState` unless it resolves all introduction pathways across all three evidence dimensions.

---

## Part 6: Scope and Limitations

This ontology is intentionally minimal. It defines the structural foundation necessary to support justified safety inference and block unjustified inference. It does not attempt to:

- Model the biochemistry of allergic reaction or sensitization thresholds
- Automate regulatory compliance or labeling requirements
- Represent the full complexity of ingredient sourcing or supply chain allergen risk
- Replace human expert judgment in high-stakes individual allergen assessments
- Enumerate all possible allergens beyond the major regulated set

The ontology is designed to be testable in realistic scenarios. Phase 1 succeeds when the scenario test suite demonstrates that justified safety inference is permitted — that is, when a dish with a completed `ConfirmedAbsent` assessment across all three dimensions correctly produces a positive safety inference — and that unjustified inference is blocked — that is, when incomplete assessments, `PotentiallyPresent` states, and `Unknown` states correctly prevent safety conclusions.

Concepts introduced in Phase 2 — `Mention`, `Omission`, `Disclaimer`, `CertaintyLevel`, `Scope`, and `RoleType` — extend this ontology to model how allergen risk is communicated in real-world menu language. Phase 2 builds on the structural foundation defined here. It does not modify the core exposure logic or the Safety Inference Principle.
