# Phase 2 Epistemic Extension — Communication Layer
**Project:** NomNomSafe Ontology Research
**Phase:** 2 — Epistemic Extension
**Deliverable:** R3.5
**Location:** `docs/individual/jeff-perdue/ontology/phase2_epistemic_extension.md`

---

## Introduction

The Phase 1 Core Exposure-State Ontology defines a structural model for allergen reasoning: seven core concepts, four ExposureState values, a five-rule ordered exposure logic, and a Safety Inference Principle that gates all safety conclusions behind a completed `ConfirmedAbsent` assessment. Phase 1 assumes that the inputs to that reasoning process — ingredient composition, CrossContactSource profile, PreparationContext — are available as structured data. In practice, they are not.

Real-world allergen information arrives as language. It arrives in menu descriptions that highlight premium ingredients and omit structural ones. It arrives in "may contain" disclaimers that hedge without specifying. It arrives in staff verbal assurances of varying reliability. It arrives in regulatory labels, marketing claims, allergen matrices, and chef cards — each carrying a different institutional weight and a different relationship to the underlying facts the Phase 1 ontology needs as input.

Phase 2 builds the translation layer between that language and the Phase 1 ExposureState framework. It defines seven new concepts — Mention, Omission, Disclaimer, RoleType, CertaintyLevel, Scope, and DeclarationBasis — that together characterize any allergen-relevant communication act with enough precision to produce a defensible ExposureState assignment. Phase 2 does not modify the Phase 1 exposure logic or the Safety Inference Principle. It extends the system upstream, providing the mechanism by which real-world communication inputs are converted into the structured evidence that Phase 1 requires.

The design follows the layer separation principle stated in the research roadmap: the ontology layer defines what exists and how exposure works; the communication layer defines how information about what exists reaches the system. These are kept deliberately separate so that changes in communication conventions — new labeling standards, new disclosure requirements, new menu formats — do not require changes to the core exposure logic.

---

## Part 1: The Communication Act

Before defining the seven Phase 2 concepts individually, it is useful to establish what they are properties of. In Phase 2, the fundamental unit of analysis is the **communication act** — any statement, label, description, omission, or disclosure that conveys information about the allergen content or preparation of a FoodItem.

A communication act may be written or verbal, formal or informal, regulatory or voluntary. It may be produced by the restaurant, the kitchen, a staff member, a supplier, or a regulatory body. It may appear on a menu, on product packaging, in an allergen matrix, or in a conversation between a diner and a server. What makes something a communication act in the Phase 2 sense is that it carries information — or the structured absence of information — that is relevant to an ExposureState determination for a specific FoodItem and Allergen.

Each communication act is characterized across the seven Phase 2 dimensions. Together, those characterizations determine how much evidential weight the act carries and which Phase 1 evidence dimension it provides evidence for. The output of that characterization is a provisional ExposureState contribution — the state the communication act supports, subject to the Phase 1 exposure logic and Safety Inference Principle.

---

## Part 2: The Seven Phase 2 Concepts

### Mention

A Mention is an explicit reference to an allergen, ingredient, or substance in a communication act. A Mention is the positive signal — it asserts that something is named, referenced, or declared. Mentions vary in their implications depending on how the referenced item is characterized, which is captured by RoleType, and how reliable the communication act is, which is captured by CertaintyLevel and DeclarationBasis.

A Mention of an allergen does not automatically produce `ConfirmedPresent`. A mention of peanuts in a "peanut-flavored" descriptor may mean actual peanuts or a peanut-free flavoring compound. A mention of butter in a dish description may mean butter is a structural ingredient or may mean it is a finishing garnish that can be removed. The ExposureState contribution of a Mention is determined in conjunction with RoleType and CertaintyLevel.

### Omission

An Omission is the absence of a reference to an allergen or ingredient in a communication act. Omission is not evidence of absence — it is the structured representation of a missing signal. In the Phase 1 ontology, an allergen not mentioned in a menu description defaults to `Unknown`, not `ConfirmedAbsent`. Phase 2 formalizes this by treating Omission as a communication act in its own right: a communication act consisting entirely of an Omission produces no positive evidence for any ExposureState and leaves the relevant dimension at `Unknown`.

Omission is categorically different from a confirmed negative. A confirmed negative — "this dish contains no peanuts, confirmed by the kitchen" — is a Mention of an absence, characterized by a high CertaintyLevel and a Verified DeclarationBasis. An Omission is simply silence. The system must not treat them equivalently. This distinction is the Phase 2 formalization of the Omission-as-Absence failure category documented in Phase 0.

### Disclaimer

A Disclaimer is a communication act that explicitly hedges or qualifies the safety of a FoodItem with respect to a specific allergen or allergen class. Disclaimers are formally distinct from Mentions and Omissions because they carry a specific epistemic structure: they acknowledge a risk without confirming its presence or absence. "May contain peanuts," "prepared in a facility that also processes tree nuts," and "we cannot guarantee this dish is free of any allergen" are all Disclaimers.

Disclaimers map to `PotentiallyPresent` as their ExposureState contribution. They identify a plausible introduction pathway — the facility processes tree nuts, the kitchen cannot guarantee separation — without confirming that the pathway results in actual allergen transfer. This is precisely the `PotentiallyPresent` definition from Phase 1: a plausible but unconfirmed introduction pathway exists. A Disclaimer is the communication act that creates that pathway on record.

Disclaimers also carry Scope — a "may contain" statement on a dish-level description has different implications than a facility-level processing disclaimer. The Scope dimension captures this distinction, as defined in the Scope section below.

### RoleType

RoleType characterizes how an allergen or ingredient is referenced within a Mention. It distinguishes between three roles that determine how the Mention maps onto the Phase 1 evidence dimensions and, critically, how it interacts with the Modification Request Rule.

**Structural** — the allergen or ingredient is a functional, load-bearing component of the dish whose presence is embedded in the preparation method rather than in the finishing or plating. A structural ingredient cannot be removed by a simple modification request without changing the preparation method itself. Egg yolks in hollandaise, butter in risotto mantecatura, anchovies in Caesar dressing, and soy sauce in wok seasoning are all structural in this sense. A Mention with RoleType Structural maps to `ConfirmedPresent` when the CertaintyLevel is Verified or Asserted, and to `PotentiallyPresent` when the culinary convention is known but not confirmed for this specific preparation. Under the Modification Request Rule, a modification that targets only a Structural ingredient reference does not resolve the PreparationContext dimension and does not change ExposureState.

**Incidental** — the allergen or ingredient is present in the dish but is not embedded in the preparation method. It appears as a topping, a garnish, a finishing element, or a component that can be added or removed without altering the preparation. A butter garnish on a grilled steak, a nut topping on a salad, and a cheese finishing on a pasta dish are incidental in this sense. A Mention with RoleType Incidental maps to `ConfirmedPresent` when CertaintyLevel is Verified, and to `PotentiallyPresent` when the presence is described but not confirmed. Under the Modification Request Rule, a modification that targets an Incidental ingredient reference can resolve the ingredient-composition dimension — but only if the CrossContactSource and PreparationContext dimensions are also cleared.

**Flavor** — the allergen or ingredient is referenced as a descriptor or flavor signal that may or may not indicate actual presence of the allergenic substance. "Peanut-flavored," "butter-glazed" without confirmation of actual butter, and "smoky bacon" on a dish where the bacon may be a flavoring compound rather than actual bacon are Flavor references. A Mention with RoleType Flavor maps to `PotentiallyPresent` as a default, because the reference does not confirm presence but identifies a plausible pathway. Confirmation from a Verified or Asserted source is required to move from `PotentiallyPresent` to either `ConfirmedPresent` or `ConfirmedAbsent`.

### CertaintyLevel

CertaintyLevel characterizes the degree of epistemic confidence carried by a communication act and determines which ExposureState transitions the act is capable of supporting. CertaintyLevel has four values, each with a defined effect on ExposureState assignment.

**Verified** — the communication act is based on formal verification, regulatory disclosure, or confirmed and documented kitchen protocol. A Verified communication act is sufficient to support `ConfirmedAbsent` or `ConfirmedPresent` assignments when combined with a completed evidence pathway across all three Phase 1 dimensions. Regulatory allergen labels, confirmed allergen matrices reviewed by the kitchen, and chef cards verified against current preparation protocols carry Verified CertaintyLevel. A single Verified communication act covering all three evidence dimensions is sufficient to support a `ConfirmedAbsent` determination under Phase 1 Rule 4.

**Asserted** — the communication act makes a direct claim about allergen presence or absence based on the source's knowledge, without formal verification. An Asserted communication act is sufficient to support `PotentiallyPresent` assessment and to contribute to evidence pathway construction, but is not independently sufficient to support `ConfirmedAbsent`. A manager's direct verbal confirmation that a dish contains no peanuts, a server's statement that the fryer is dedicated, and a menu description that explicitly lists all ingredients as a claim carry Asserted CertaintyLevel. Multiple Asserted acts covering all three evidence dimensions may together support `ConfirmedAbsent` when they are consistent and each covers a distinct dimension.

**Implied** — the communication act carries information that suggests an ExposureState without directly asserting it. Implied communication acts are sufficient to flag plausible introduction pathways under Phase 1 Rule 3 but are not sufficient to resolve them. A menu description that names a preparation method from which allergen content can be inferred — "Caesar dressing," "hollandaise," "wok-fried noodles" — carries Implied CertaintyLevel for the allergens canonically associated with those preparations. An Implied act produces `PotentiallyPresent` as its ExposureState contribution and flags the dimension for further investigation.

**Disclaimed** — the communication act explicitly hedges its own reliability. A Disclaimed communication act acknowledges a risk or uncertainty without confirming it. "May contain," "we cannot guarantee," and "prepared in a facility that also processes" are Disclaimed. A Disclaimed act maps to `PotentiallyPresent` and blocks `ConfirmedAbsent` assignment for the relevant dimension regardless of other evidence, because the source has explicitly declined to make a confirmable claim.

### Scope

Scope identifies which level of the food production and service system a communication act covers, and maps that level onto the corresponding Phase 1 evidence dimension. Scope is not merely a metadata tag — it actively determines which evidence dimension a communication act provides evidence for.

**Dish** — the communication act covers the specific FoodItem being assessed. A dish-level claim provides evidence for the ingredient composition dimension of the Phase 1 evidence pathway. "This dish contains no peanuts" is a dish-level Scope claim. It provides evidence about ingredient composition but says nothing about CrossContactSource or PreparationContext unless those dimensions are explicitly addressed.

**Kitchen** — the communication act covers the kitchen environment in which the FoodItem is prepared. A kitchen-level claim provides evidence for the CrossContactSource dimension. "Our kitchen is peanut-free" is a kitchen-level Scope claim. It provides evidence about the preparation environment but does not independently address ingredient composition or PreparationContext unless the claim is specific enough to cover those dimensions. A kitchen-level claim with Verified CertaintyLevel is sufficient to contribute to CrossContactSource clearance under Phase 1 Rule 4.

**Facility** — the communication act covers the production facility, which may be the restaurant kitchen or a supplier facility producing ingredients used in the kitchen. A facility-level claim introduces a supplier-level risk dimension that is outside the kitchen's direct operational control. "Produced in a facility that also processes tree nuts" is a facility-level Scope Disclaimer. It maps to `PotentiallyPresent` for tree nuts because the introduction pathway is at a level the kitchen cannot directly verify or control, regardless of the kitchen's own allergen protocols.

**Supplier** — the communication act covers the supply chain above the facility level, including raw ingredient sourcing and upstream processing. Supplier-level claims carry the highest uncertainty because they are furthest from the point of preparation and least subject to kitchen verification. A supplier-level claim without independent verification defaults to `Unknown` as its ExposureState contribution for the relevant dimension.

### DeclarationBasis

DeclarationBasis captures the institutional grounding of a communication act — not what the act says, but the basis on which it is made. DeclarationBasis determines the epistemic weight the system assigns to a communication act and feeds directly into CertaintyLevel assignment. It distinguishes four institutional grounds.

**Regulatory** — the communication act is grounded in a legal or regulatory obligation. Regulatory declarations are made because the source is required to make them — mandatory allergen labeling under the FASTER Act, for example. Regulatory grounding does not guarantee accuracy, but it does mean the source has a legal accountability relationship to the claim. Regulatory declarations carry Verified or Asserted CertaintyLevel depending on the specific regulatory context and how directly they address the allergen and dimension in question.

**Institutional** — the communication act is grounded in a documented internal protocol or system — an allergen matrix, a chef card, a confirmed kitchen preparation protocol, a food safety management system. Institutional grounding means the claim reflects a deliberate, documented organizational commitment rather than an individual's knowledge. Institutional declarations carry Asserted CertaintyLevel as a default and can support Verified CertaintyLevel when the protocol has been independently confirmed for the current preparation.

**Commercial** — the communication act is grounded in a marketing or commercial interest. Commercial declarations describe, promote, or characterize a FoodItem in ways designed to appeal to customers. "All-natural," "made with real butter," "peanut-free kitchen" used as a selling point, and menu descriptions that highlight premium ingredients while omitting structural ones are commercially grounded. Commercial declarations carry Implied CertaintyLevel at best, because the source's primary interest is persuasion rather than disclosure. A commercially grounded claim cannot independently support `ConfirmedAbsent`.

**Conversational** — the communication act is grounded in a real-time verbal exchange between a diner and a staff member. Conversational declarations carry Asserted CertaintyLevel when the staff member has direct knowledge of the preparation — a chef or kitchen manager confirming a specific protocol — and Implied CertaintyLevel when the staff member is relaying general knowledge or making a reasonable inference. Conversational declarations are often the most immediately available form of allergen information for a diner, and the system must handle them without either dismissing them or over-weighting them.

---

## Part 3: The Translation Framework

The seven Phase 2 concepts work together as a translation framework. A communication act enters the framework as natural language or a label; it exits as a structured ExposureState contribution that feeds into the Phase 1 exposure logic. The translation proceeds in three steps.

**Step 1 — Characterize the communication act.** Identify whether the act is a Mention, Omission, or Disclaimer. If it is a Mention or Disclaimer, assign RoleType, CertaintyLevel, Scope, and DeclarationBasis.

**Step 2 — Map to an evidence dimension.** Use Scope to determine which Phase 1 evidence dimension the act provides evidence for: Dish → ingredient composition, Kitchen → CrossContactSource, Facility or Supplier → supplier-level risk outside direct kitchen control.

**Step 3 — Determine ExposureState contribution.** Use the combination of the act type, RoleType, CertaintyLevel, and DeclarationBasis to determine the ExposureState contribution for the relevant dimension. That contribution is then evaluated under the Phase 1 exposure logic. If multiple communication acts cover the same dimension, the most conservative state takes precedence, consistent with the state precedence rule in Phase 1.

---

## Part 4: Communication Act Mapping Table

The following table maps common real-world communication acts to their Phase 2 characterization and resulting ExposureState contribution. This is not an exhaustive enumeration — it is an illustrative reference for applying the translation framework to the most common allergen communication patterns encountered in restaurant contexts.

| Communication Act | Act Type | RoleType | CertaintyLevel | Scope | DeclarationBasis | ExposureState Contribution |
|---|---|---|---|---|---|---|
| Allergen listed as named ingredient | Mention | Structural or Incidental | Verified | Dish | Regulatory or Institutional | ConfirmedPresent |
| "May contain peanuts" label | Disclaimer | — | Disclaimed | Dish or Facility | Regulatory | PotentiallyPresent |
| "Prepared in a facility that also processes tree nuts" | Disclaimer | — | Disclaimed | Facility | Regulatory | PotentiallyPresent |
| Allergen not mentioned in menu description | Omission | — | — | Dish | — | Unknown |
| "This dish is peanut-free" (server verbal, unverified) | Mention | — | Asserted | Dish | Conversational | Contributes to ConfirmedAbsent pathway; insufficient alone |
| "Our kitchen is entirely nut-free" (manager, confirmed protocol) | Mention | — | Verified | Kitchen | Institutional | Clears CrossContactSource dimension |
| "Peanut-free kitchen" on menu as marketing claim | Mention | — | Implied | Kitchen | Commercial | PotentiallyPresent — commercial grounding insufficient for ConfirmedAbsent |
| "Caesar dressing — housemade" with no further detail | Mention | Structural | Implied | Dish | Commercial | PotentiallyPresent — canonical fish introduction pathway identified |
| "Whipped with olive oil" (mashed potatoes) | Mention | Incidental or Commercial | Implied | Dish | Commercial | PotentiallyPresent — partial disclosure does not confirm dairy absence |
| Allergen matrix provided and confirmed current | Mention | Structural and Incidental | Verified | Dish and Kitchen | Institutional | Supports ConfirmedAbsent when all three dimensions covered |
| Chef card reviewed and confirmed against current prep | Mention | Structural and Incidental | Verified | Dish and Kitchen | Institutional | Supports ConfirmedAbsent when all three dimensions covered |
| "Vegan" label with no allergen disclosure | Mention | — | Implied | Dish | Commercial | PotentiallyPresent — dietary label does not constitute allergen evidence |
| "All-natural" or "made with whole ingredients" | Mention | — | Implied | Dish | Commercial | Unknown — marketing language carries no allergen evidential weight |
| "No butter added" (server verbal, modification confirmed by kitchen) | Mention | Incidental | Asserted | Dish | Conversational | Resolves ingredient-composition dimension for that allergen if RoleType is Incidental; does not resolve PreparationContext if Structural |
| "We use a dedicated fryer for fries only" (kitchen confirmed) | Mention | — | Asserted | Kitchen | Institutional | Clears CrossContactSource dimension for that fryer |
| "Hollandaise" named on menu | Mention | Structural | Implied | Dish | Commercial | PotentiallyPresent for eggs — canonical structural ingredient identified |
| Regulatory allergen label listing all major allergens present | Mention | Structural | Verified | Dish | Regulatory | ConfirmedPresent for listed allergens |

---

## Part 5: Worked Examples

The following worked examples apply the full translation framework to realistic allergen communication scenarios, showing how multiple communication acts combine to produce a final ExposureState determination under the Phase 1 exposure logic.

---

### Worked Example 1 — "Peanut-Free Kitchen" Marketing Claim

**Scenario:** A restaurant's menu states "peanut-free kitchen" as part of its brand description. A diner with a peanut allergy is assessing a grilled chicken dish. No other allergen information is provided.

**Communication Acts:**

Act 1: "Peanut-free kitchen" on menu.
- Act Type: Mention
- RoleType: not applicable (environmental claim, not ingredient-level)
- CertaintyLevel: Implied — the claim is made in a menu context without protocol documentation
- Scope: Kitchen
- DeclarationBasis: Commercial — the claim appears as a marketing statement
- ExposureState Contribution: The act provides evidence for the CrossContactSource dimension at Kitchen scope. However, Commercial DeclarationBasis produces Implied CertaintyLevel, which is insufficient to clear CrossContactSource. The contribution is `PotentiallyPresent` — the claim is noted but cannot be treated as confirmed.

Act 2: Peanuts not mentioned in dish description.
- Act Type: Omission
- ExposureState Contribution: `Unknown` for ingredient composition dimension — omission is not evidence of absence.

**Phase 1 Evaluation:** Ingredient composition dimension: `Unknown`. CrossContactSource dimension: `PotentiallyPresent` — commercial claim insufficient to clear. PreparationContext dimension: not assessed. State precedence: `PotentiallyPresent` takes precedence over `Unknown`. `ExposureState(peanuts) = PotentiallyPresent`.

**Safety Inference Outcome:** Blocked. The marketing claim cannot substitute for a verified kitchen-level protocol confirmation. The diner would need to obtain an Institutional or Regulatory declaration at Kitchen scope — an allergen matrix, a confirmed kitchen protocol, or a manager's verified assurance — to clear the CrossContactSource dimension.

---

### Worked Example 2 — Allergen Matrix with Full Coverage

**Scenario:** A restaurant provides a current allergen matrix reviewed and confirmed by the kitchen manager. The matrix confirms that a specific pasta dish contains wheat and dairy, contains no peanuts, tree nuts, eggs, fish, shellfish, soy, or sesame as ingredients, and is prepared in a kitchen section with no shared equipment with peanut-containing dishes. The preparation method involves boiling pasta in dedicated water and finishing with a cream sauce.

**Communication Acts:**

Act 1: Allergen matrix — confirmed current by kitchen manager.
- Act Type: Mention (of allergen absences and presences)
- CertaintyLevel: Verified — confirmed by an institutional source with direct kitchen knowledge
- Scope: Dish and Kitchen — the matrix covers ingredient composition and preparation environment
- DeclarationBasis: Institutional — the matrix reflects a documented organizational protocol
- ExposureState Contribution: Supports `ConfirmedAbsent` for peanuts across ingredient composition and CrossContactSource dimensions. Supports `ConfirmedPresent` for wheat and dairy.

Act 2: Preparation method — cream sauce finishing.
- Act Type: Mention
- RoleType: Structural — cream is a structural ingredient in the sauce
- CertaintyLevel: Verified — confirmed by the matrix and kitchen manager
- Scope: Dish
- DeclarationBasis: Institutional
- ExposureState Contribution: Confirms PreparationContext dimension is clear for peanuts — the preparation method involves no culinary convention that introduces peanuts.

**Phase 1 Evaluation for peanuts:** Ingredient composition: confirmed clear (Verified, Institutional). CrossContactSource: confirmed clear (Verified, Institutional, Kitchen scope). PreparationContext: confirmed clear — no preparation convention introduces peanuts. All three dimensions cleared. Rule 4 applies. `ExposureState(peanuts) = ConfirmedAbsent`.

**Safety Inference Outcome:** Permitted for peanuts. This is the worked example of a justified safety inference — one that requires all three dimensions to be genuinely cleared through a Verified, Institutional source before the Safety Inference Principle permits a conclusion.

---

### Worked Example 3 — Verbal Staff Assurance, Partial Coverage

**Scenario:** A diner with a dairy allergy asks a server whether the risotto contains dairy. The server says "I think it's made with vegetable broth, not cream." No allergen matrix or kitchen confirmation is available. The menu describes the dish as "creamy mushroom risotto."

**Communication Acts:**

Act 1: "Creamy mushroom risotto" — menu description.
- Act Type: Mention
- RoleType: Structural — "creamy" signals dairy as a likely structural preparation element; risotto canonically involves butter and parmesan through mantecatura
- CertaintyLevel: Implied
- Scope: Dish
- DeclarationBasis: Commercial
- ExposureState Contribution: `PotentiallyPresent` — canonical structural introduction pathway identified through preparation convention.

Act 2: Server verbal — "I think it's made with vegetable broth, not cream."
- Act Type: Mention (of dairy absence)
- CertaintyLevel: Implied — the server's use of "I think" explicitly signals uncertainty; this is not an Asserted claim
- Scope: Dish
- DeclarationBasis: Conversational
- ExposureState Contribution: Insufficient to resolve `PotentiallyPresent`. An Implied, Conversational declaration does not clear any evidence dimension. It is noted but does not advance the ExposureState toward `ConfirmedAbsent`.

**Phase 1 Evaluation:** Ingredient composition: `PotentiallyPresent` — broth composition unconfirmed, dairy presence in base not ruled out. CrossContactSource: not assessed. PreparationContext: `PotentiallyPresent` — mantecatura convention identified, not confirmed absent for this preparation. State precedence: `PotentiallyPresent`. `ExposureState(dairy) = PotentiallyPresent`.

**Safety Inference Outcome:** Blocked. The server's hedged verbal assurance does not constitute sufficient evidence to clear any evidence dimension. The diner would need a kitchen-confirmed, Institutional-grounded declaration covering all three dimensions — specifically confirming that the risotto base contains no dairy, that the mantecatura step uses no butter or parmesan, and that no shared cookware introduces dairy cross-contact — before a safety inference could be permitted.

---

### Worked Example 4 — "May Contain" Disclaimer with Clean Ingredient List

**Scenario:** A packaged bakery item lists its ingredients explicitly: flour, sugar, eggs, butter, vanilla. No nuts are listed. The packaging also states "may contain tree nuts."

**Communication Acts:**

Act 1: Ingredient list — flour, sugar, eggs, butter, vanilla.
- Act Type: Mention
- RoleType: Structural — these are the named compositional ingredients
- CertaintyLevel: Verified — regulatory ingredient labeling on packaged food
- Scope: Dish
- DeclarationBasis: Regulatory
- ExposureState Contribution: Confirms ingredient composition dimension is clear for tree nuts — no tree nut ingredient is present in the named composition.

Act 2: "May contain tree nuts" disclaimer.
- Act Type: Disclaimer
- CertaintyLevel: Disclaimed
- Scope: Facility — "may contain" disclaimers typically refer to shared facility processing
- DeclarationBasis: Regulatory
- ExposureState Contribution: `PotentiallyPresent` — an identified cross-contact or facility-level pathway exists.

**Phase 1 Evaluation for tree nuts:** Ingredient composition: confirmed clear (Verified, Regulatory). CrossContactSource and facility-level risk: `PotentiallyPresent` — the "may contain" disclaimer identifies an unconfirmed facility-level introduction pathway. State precedence: `PotentiallyPresent` takes precedence over the cleared ingredient dimension. `ExposureState(tree nuts) = PotentiallyPresent`.

**Safety Inference Outcome:** Blocked. This example validates a critical property of the framework: a clean ingredient list does not override a Disclaimer. The "may contain" statement introduces a `PotentiallyPresent` contribution at facility scope that the ingredient list — which only covers the dish-level ingredient composition dimension — cannot cancel. Both acts are evaluated; the more conservative state prevails.

---

## Part 6: Relationship to Phase 1 and Forward Compatibility

Phase 2 is additive. It does not modify the Phase 1 exposure logic, the ExposureState values, or the Safety Inference Principle. It provides the input characterization layer that Phase 1 requires — translating real-world communication acts into structured evidence contributions that the Phase 1 rules can evaluate.

The relationship between the two phases can be stated precisely: Phase 1 defines what must be true for a safety inference to be permitted; Phase 2 defines what real-world information is sufficient to establish that what Phase 1 requires is true. A communication act that is Verified, Institutional, and covers all three evidence dimensions at Dish and Kitchen scope can establish `ConfirmedAbsent` under Phase 1 Rule 4. A communication act that is Commercial, Implied, and covers only one dimension cannot — regardless of what it claims.

Phase 3 will use this framework empirically. The Phase 3 experimental design will compare baseline LLM allergen reasoning against ontology-grounded reasoning in which communication acts are characterized using the Phase 2 concepts before being evaluated under the Phase 1 exposure logic. The hypothesis is that ontology-grounded reasoning will reduce unjustified safety claims, reduce overconfidence, and correctly handle uncertainty — because the Phase 2 translation layer will prevent the system from treating an Implied, Commercial communication act as sufficient to support `ConfirmedAbsent`, regardless of how the question is framed.

Phase 4 will implement this framework in a layered formal architecture, including an RDF-compatible representation of the Phase 2 concepts and their relationships to the Phase 1 ontology nodes.
