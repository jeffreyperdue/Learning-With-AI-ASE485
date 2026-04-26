# Scenario Test Suite — Core Exposure-State Ontology
**Project:** NomNomSafe Ontology Research
**Phase:** 1 — Core Exposure-State Ontology
**Deliverable:** R3.4
**Location:** `docs/individual/jeff-perdue/ontology/scenario_test_suite.md`

---

## Introduction

This document is the Phase 1 scenario test suite. Its purpose is to validate that the Core Exposure-State Ontology defined in R3.3 behaves correctly across a range of realistic allergen safety scenarios — specifically, that it permits justified safety inference when a completed `ConfirmedAbsent` assessment exists across all three evidence dimensions, and blocks unjustified inference when the assessment is incomplete, when a plausible introduction pathway remains unresolved, or when allergen presence is confirmed.

Phase 1 succeeds when both conditions hold consistently across the full scenario set. A failure in either direction — a blocked inference that should be permitted, or a permitted inference that should be blocked — indicates a gap or ambiguity in the ontology that must be resolved before Phase 2 begins.

The suite contains eighteen scenarios organized into six categories: simple dishes with complete evidence, shared fryer and cross-contact cases, oil and ingredient ambiguity cases, bakery and dessert products, modification request cases, and missing preparation context cases. Each scenario identifies the `FoodItem` and `Allergen` under assessment, works through the exposure logic to determine `ExposureState`, states the safety inference outcome, and provides a rationale connecting the result to the specific ontology rules and Phase 0 failure categories it validates.

---

## Category A — Simple Dishes with Complete Evidence

These scenarios establish the baseline behavior of the ontology when evidence is sufficiently complete to support a determinate conclusion. They validate that `ConfirmedAbsent` is correctly assigned when all three evidence dimensions are cleared, and that `ConfirmedPresent` is correctly assigned when allergen presence is known.

---

### Scenario 1 — Plain Grilled Chicken, Peanut Assessment (Justified Inference Permitted)

**FoodItem:** Grilled chicken breast, prepared on a dedicated grill used exclusively for plain proteins, seasoned with salt, pepper, and olive oil only. Kitchen has confirmed ingredient composition, grill exclusivity, and preparation method.

**Allergen:** Peanuts

**ExposureState Determination:** Rule 1 does not apply — peanuts are not present in any ingredient or substance. Rule 2 does not apply — the dedicated grill has been confirmed exclusive and introduces no cross-contact source for peanuts. Rule 3 does not apply — no plausible unconfirmed introduction pathway exists; the preparation method involves no culinary convention that typically incorporates peanuts. Rule 4 applies — all three evidence dimensions have been assessed and confirmed clear. `ExposureState(peanuts) = ConfirmedAbsent`.

**Safety Inference Outcome:** Permitted. `ExposureState = ConfirmedAbsent` satisfies the Safety Inference Principle.

**Rationale:** This is the baseline justified inference case. It demonstrates that the ontology does not block inference categorically — it blocks inference when evidence is insufficient. When all three dimensions are genuinely cleared through explicit confirmation, `ConfirmedAbsent` is correctly assigned and the safety inference follows. The scenario is intentionally simple to isolate the permission condition cleanly.

---

### Scenario 2 — Peanut Sauce Noodle Bowl, Peanut Assessment (ConfirmedPresent)

**FoodItem:** Noodle bowl served with housemade peanut sauce, listed explicitly on the menu.

**Allergen:** Peanuts

**ExposureState Determination:** Rule 1 applies — peanuts are directly and positively included as a named ingredient in the peanut sauce component of the dish. `ExposureState(peanuts) = ConfirmedPresent`.

**Safety Inference Outcome:** Blocked. `ExposureState = ConfirmedPresent` does not satisfy the Safety Inference Principle.

**Rationale:** This case validates the clean `ConfirmedPresent` assignment and confirms that the Safety Inference Principle correctly blocks inference when allergen presence is known. It also establishes that `ConfirmedPresent` is not a failure state — it is the honest and correct representation of a dish that contains the allergen. The ontology's role is to represent exposure accurately, not to produce safe outcomes by default.

---

### Scenario 3 — Caesar Salad, Fish Assessment (PotentiallyPresent from PreparationContext)

**FoodItem:** Caesar salad with housemade dressing, described only as "housemade" with no further ingredient detail provided.

**Allergen:** Fish

**ExposureState Determination:** Rule 1 does not apply — fish is not listed as an ingredient. Rule 2 does not apply — no cross-contact source for fish has been identified in the salad preparation environment. Rule 3 applies — Caesar dressing canonically contains anchovies and Worcestershire sauce, both of which introduce fish as a functional preparation-context element. The description "housemade" does not confirm or deny this. A plausible and identified introduction pathway exists through the preparation convention associated with this named dish type. `ExposureState(fish) = PotentiallyPresent`.

**Safety Inference Outcome:** Blocked. `ExposureState = PotentiallyPresent` does not satisfy the Safety Inference Principle.

**Rationale:** This scenario validates that named culinary conventions are treated as identified introduction pathways under Rule 3, even when the allergen is not listed in the menu description. A system that reasoned only from the ingredient list would find no fish and might assign `ConfirmedAbsent` or `Unknown`. The `PreparationContext` dimension requires the system to consult the culinary convention associated with Caesar dressing before completing the assessment. This directly validates the ontology's response to the Preparation-Context Blindness failure category.

---

## Category B — Shared Fryer and Cross-Contact Cases

These scenarios validate that `CrossContactSource` is correctly treated as a mandatory and independent evidence dimension. They confirm that a clean ingredient composition does not produce `ConfirmedAbsent` when the cross-contact dimension has not been assessed or cleared.

---

### Scenario 4 — Plain French Fries, Gluten Assessment (PotentiallyPresent from CrossContactSource)

**FoodItem:** French fries listed as potatoes and vegetable oil. Prepared in a shared fryer also used for breaded items including onion rings and fried chicken tenders. Fryer exclusivity has not been confirmed.

**Allergen:** Wheat (gluten)

**ExposureState Determination:** Rule 1 does not apply — wheat is not present in the listed ingredients. Rule 2 does not apply — cross-contact through the shared fryer has not been confirmed to occur, only identified as a plausible pathway. Rule 3 applies — the shared fryer is an identified `CrossContactSource` for wheat. The fryer is used for breaded items that contain wheat, and exclusivity has not been confirmed. A plausible and identified introduction pathway exists. `ExposureState(wheat) = PotentiallyPresent`.

**Safety Inference Outcome:** Blocked. `ExposureState = PotentiallyPresent` does not satisfy the Safety Inference Principle.

**Rationale:** This is the canonical cross-contact scenario. The ingredient list is clean — potatoes and oil contain no wheat. But ingredient composition alone is insufficient for `ConfirmedAbsent` under Rule 4. The `CrossContactSource` dimension has not been cleared. This scenario validates that the ontology correctly requires fryer exclusivity to be confirmed, not assumed, before a safety inference is permitted. It directly validates the ontology's response to the Cross-Contact Blindness failure category.

---

### Scenario 5 — Plain French Fries, Gluten Assessment (Justified Inference Permitted)

**FoodItem:** French fries listed as potatoes and vegetable oil. Prepared in a dedicated fryer used exclusively for plain potato products. Kitchen has confirmed fryer exclusivity and that no wheat-containing items are prepared in or near the fryer.

**Allergen:** Wheat (gluten)

**ExposureState Determination:** Rule 1 does not apply — wheat is not present in any ingredient. Rule 2 does not apply — fryer exclusivity has been confirmed and no cross-contact source for wheat exists. Rule 3 does not apply — no plausible unconfirmed introduction pathway exists. Rule 4 applies — all three evidence dimensions have been assessed and confirmed clear. `ExposureState(wheat) = ConfirmedAbsent`.

**Safety Inference Outcome:** Permitted. `ExposureState = ConfirmedAbsent` satisfies the Safety Inference Principle.

**Rationale:** This scenario is the confirmed-clear counterpart to Scenario 4. It validates that the ontology does not categorically block inference for shared-fryer scenarios — it blocks inference specifically when the cross-contact dimension has not been resolved. When fryer exclusivity is confirmed, the `CrossContactSource` dimension is cleared, and if ingredient composition and preparation context are also clear, `ConfirmedAbsent` is correctly assigned.

---

### Scenario 6 — Grilled Salmon, Shellfish Assessment (PotentiallyPresent from CrossContactSource)

**FoodItem:** Grilled salmon fillet at a seafood-focused restaurant. Salmon is a fin fish and contains no shellfish ingredient. The grill is shared and used to prepare shrimp, scallops, and lobster tail throughout service. Grill cleaning between items is not confirmed.

**Allergen:** Shellfish

**ExposureState Determination:** Rule 1 does not apply — shellfish is not an ingredient in salmon. Rule 2 does not apply — cross-contact has not been confirmed to occur, only identified as a plausible pathway. Rule 3 applies — the shared grill is a `CrossContactSource` for shellfish in a high-volume seafood preparation environment. The probability of residual shellfish protein transfer is structurally elevated by the preparation context. A plausible and identified introduction pathway exists. `ExposureState(shellfish) = PotentiallyPresent`.

**Safety Inference Outcome:** Blocked. `ExposureState = PotentiallyPresent` does not satisfy the Safety Inference Principle.

**Rationale:** This scenario validates that the `PreparationContext` of a restaurant environment — not only the equipment used for a specific dish — modulates the `CrossContactSource` assessment. A seafood-focused kitchen with a shared grill presents an elevated cross-contact profile for all items prepared on that surface. The ontology correctly identifies the shared grill as an unresolved `CrossContactSource` and assigns `PotentiallyPresent` rather than defaulting to `Unknown` or prematurely assigning `ConfirmedAbsent`.

---

## Category C — Oil and Ingredient Ambiguity Cases

These scenarios validate the ontology's handling of partially known ingredient compositions — cases where a listed ingredient is a compound whose full substance-level composition is not disclosed. They confirm that partial knowledge does not satisfy the `ConfirmedAbsent` requirement.

---

### Scenario 7 — Stir-Fry Vegetables, Soy Assessment (PotentiallyPresent from Ingredient Ambiguity)

**FoodItem:** Stir-fried mixed vegetables listed as "vegetables, vegetable oil, and house sauce." The composition of "house sauce" has not been disclosed.

**Allergen:** Soy

**ExposureState Determination:** Rule 1 does not apply — soy is not listed as a named ingredient. Rule 2 is not yet determinable — cross-contact sources have not been assessed. Rule 3 applies — "house sauce" is a compound ingredient whose composition is not fully known, and soy sauce is a canonical component of stir-fry house sauces. Additionally, wok-fried preparations conventionally use soy-based seasoning as a functional preparation element. A plausible and identified introduction pathway exists through both the undisclosed compound ingredient and the preparation convention. `ExposureState(soy) = PotentiallyPresent`.

**Safety Inference Outcome:** Blocked. `ExposureState = PotentiallyPresent` does not satisfy the Safety Inference Principle.

**Rationale:** This scenario validates two overlapping sources of `PotentiallyPresent`: an undisclosed compound ingredient and a preparation convention. Either alone would be sufficient to assign `PotentiallyPresent` under Rule 3. The scenario also illustrates that the `Substance` level of the ontology is relevant here — "house sauce" is an `Ingredient` whose constituent `Substance` instances are unknown, and until they are known, the ingredient composition dimension cannot be confirmed clear.

---

### Scenario 8 — Sautéed Green Beans, Tree Nut Assessment (Unknown from Incomplete Assessment)

**FoodItem:** Sautéed green beans listed simply as "green beans, oil, garlic." No preparation context information has been provided. The type of oil is not specified. Kitchen cross-contact profile is unknown.

**Allergen:** Tree nuts

**ExposureState Determination:** Rule 1 does not apply — tree nuts are not listed. Rule 2 cannot be evaluated — the `CrossContactSource` profile has not been assessed. Rule 3 cannot be evaluated with confidence — the preparation context is not sufficiently known to identify or rule out plausible introduction pathways. Rule 4 cannot apply — the assessment is incomplete. Rule 5 applies — the evidence pathway is insufficient to assign any substantive state. `ExposureState(tree nuts) = Unknown`.

**Safety Inference Outcome:** Blocked. `ExposureState = Unknown` does not satisfy the Safety Inference Principle.

**Rationale:** This scenario validates the `Unknown` default state and confirms that an incomplete assessment correctly produces `Unknown` rather than defaulting to `ConfirmedAbsent`. The dish does not mention tree nuts, but that omission is not evidence of absence. The cross-contact profile and preparation context have not been evaluated, meaning the assessment has not been completed. `Unknown` is the mandatory and correct output. This directly validates the ontology's response to the Omission-as-Absence failure category.

---

### Scenario 9 — Mashed Potatoes, Dairy Assessment (PotentiallyPresent from PreparationContext)

**FoodItem:** Mashed potatoes described as "whipped with olive oil." No further preparation detail provided. Standard kitchen preparation methods have not been confirmed.

**Allergen:** Milk (dairy)

**ExposureState Determination:** Rule 1 does not apply — dairy is not listed as an ingredient. Rule 2 has not been assessed. Rule 3 applies — "whipped with olive oil" describes one ingredient used in preparation but does not confirm the complete preparation. Mashed potatoes conventionally involve butter and cream as structural elements, and the presence of olive oil does not confirm the absence of dairy — it may describe a premium ingredient added alongside conventional dairy elements, or it may describe a genuinely dairy-free preparation. The description is ambiguous and a plausible introduction pathway exists through standard preparation convention. `ExposureState(dairy) = PotentiallyPresent`.

**Safety Inference Outcome:** Blocked. `ExposureState = PotentiallyPresent` does not satisfy the Safety Inference Principle.

**Rationale:** This scenario validates that a partial ingredient disclosure — one that names a safe ingredient — does not confirm the absence of an unsafe one. "Whipped with olive oil" is not a complete preparation declaration. It is a description of one element. The preparation convention for mashed potatoes includes dairy as a standard structural component, and until that convention has been either confirmed or ruled out for this specific preparation, `PotentiallyPresent` is the correct state.

---

## Category D — Bakery and Dessert Products

These scenarios validate the ontology's handling of high-complexity preparation environments where allergen introduction pathways are numerous and often undisclosed. They confirm that marketing language and simple ingredient descriptions do not substitute for completed evidence pathways.

---

### Scenario 10 — Chocolate Fudge Cake, Tree Nut Assessment (PotentiallyPresent from CrossContactSource and PreparationContext)

**FoodItem:** Chocolate fudge cake with raspberry coulis, described on a dessert menu. Produced in a bakery kitchen that also produces nut-containing items. No dedicated nut-free preparation area has been confirmed.

**Allergen:** Tree nuts

**ExposureState Determination:** Rule 1 does not apply — tree nuts are not listed as ingredients. Rule 2 does not apply — cross-contact has not been confirmed. Rule 3 applies on two grounds: first, the shared bakery environment is an identified `CrossContactSource` for tree nuts, with no confirmed separation; second, fudge-based preparations, almond extract in coulis, and nut garnishes added at service are plausible preparation-context introduction pathways. `ExposureState(tree nuts) = PotentiallyPresent`.

**Safety Inference Outcome:** Blocked. `ExposureState = PotentiallyPresent` does not satisfy the Safety Inference Principle.

**Rationale:** Dessert items represent a structurally elevated risk profile because bakery environments commonly work with multiple nut-containing products in close proximity, and because nuts appear in numerous forms that may not be captured by a high-level menu description — extracts, garnishes, fudge compounds, praline elements. This scenario validates that both the shared environment `CrossContactSource` and the preparation-context pathways contribute independently to `PotentiallyPresent`, and that neither must be confirmed individually for the state to be correctly assigned.

---

### Scenario 11 — Sourdough Bread, Wheat Assessment (ConfirmedPresent)

**FoodItem:** House-baked sourdough bread.

**Allergen:** Wheat

**ExposureState Determination:** Rule 1 applies — sourdough bread is produced from wheat flour as its primary structural ingredient. Sourdough fermentation reduces gluten content but does not eliminate wheat proteins. Wheat is directly and positively included as a substance in the primary ingredient. `ExposureState(wheat) = ConfirmedPresent`.

**Safety Inference Outcome:** Blocked. `ExposureState = ConfirmedPresent` does not satisfy the Safety Inference Principle.

**Rationale:** This scenario validates that culinary knowledge embedded in a named dish type — the fact that sourdough is a wheat-based preparation — is sufficient to assign `ConfirmedPresent` under Rule 1, even without an explicit ingredient declaration. It also validates an important medical distinction: fermentation does not remove wheat proteins, and a wheat allergy is an immune response to those proteins, not a sensitivity to gluten alone. The ontology correctly treats sourdough as wheat-containing without requiring explicit menu confirmation.

---

### Scenario 12 — Churros, Dairy Assessment (Unknown without Kitchen Confirmation)

**FoodItem:** Churros listed as containing flour, sugar, and cinnamon. No information provided about preparation method, frying oil, dipping sauces, or kitchen cross-contact profile.

**Allergen:** Milk (dairy)

**ExposureState Determination:** Rule 1 does not apply — dairy is not listed. Rule 2 cannot be evaluated — cross-contact sources are unknown. Rule 3 is partially evaluable — some churro preparations substitute milk or butter into the dough, and dairy-based dipping sauces are common accompaniments, but neither has been confirmed for this preparation. The three-ingredient description is not a complete preparation declaration, and the preparation context has not been assessed. Rule 5 applies — the assessment is incomplete. `ExposureState(dairy) = Unknown`.

**Safety Inference Outcome:** Blocked. `ExposureState = Unknown` does not satisfy the Safety Inference Principle.

**Rationale:** This scenario is designed in contrast to Scenario 9. Where Scenario 9 produces `PotentiallyPresent` because a specific plausible pathway is identifiable from the preparation convention, this scenario produces `Unknown` because the preparation convention for churros does not as strongly canonicalize dairy in the way that mashed potatoes or risotto do — the risk is real but the pathway is not as clearly identified without more preparation context. The more important validation point is that the three-ingredient list does not produce `ConfirmedAbsent`. An incomplete description is not a complete declaration.

---

## Category E — Modification Request Cases

These scenarios validate the Modification Request Rule defined in R3.3. They confirm that a modification request changes `ExposureState` only when it resolves all introduction pathways across all three evidence dimensions, and that a recorded request without kitchen confirmation of execution does not change `ExposureState`.

---

### Scenario 13 — Pan-Seared Scallops, Dairy Assessment, Butter-Off Request (Modification Insufficient)

**FoodItem:** Pan-seared scallops with herb butter. Diner has requested that the butter be left off. Kitchen has not confirmed that the preparation method has been modified — only that the request has been recorded.

**Allergen:** Milk (dairy)

**ExposureState Determination:** The butter-off request addresses the finishing garnish — an ingredient-composition element. It does not address the preparation method. Pan-searing conventionally involves basting the protein with butter during cooking as a functional technique, not only as a finishing element. The `PreparationContext` dimension has not been resolved by the modification request. Rule 3 applies — the preparation convention for pan-seared scallops introduces dairy through the cooking process, and the recorded modification request does not confirm that this has been changed. Additionally, the kitchen has not confirmed execution of any preparation modification. `ExposureState(dairy) = PotentiallyPresent`.

**Safety Inference Outcome:** Blocked. `ExposureState = PotentiallyPresent` does not satisfy the Safety Inference Principle.

**Rationale:** This scenario directly validates the Modification Request Rule. The diner's request is reasonable on its surface — removing the butter topping sounds like it removes the dairy. But the `PreparationContext` dimension reveals that dairy enters the dish during cooking, not only at plating. A modification that addresses only the ingredient-composition dimension does not change `ExposureState` when the allergen is also introduced through `PreparationContext`. The recorded request is evidence of intent, not evidence of outcome. This is one of the two empirically grounded cases from Phase 0 that motivated the Modification Request Rule.

---

### Scenario 14 — Pan-Seared Scallops, Dairy Assessment, Confirmed Preparation Modification (Justified Inference Permitted)

**FoodItem:** Pan-seared scallops. Diner has requested a fully dairy-free preparation. Kitchen has confirmed: scallops will be seared in olive oil only, no butter will be used at any stage of preparation, a clean pan will be used, and no shared surfaces with dairy-containing preparations will be involved. All three evidence dimensions have been assessed and confirmed clear.

**Allergen:** Milk (dairy)

**ExposureState Determination:** Rule 1 does not apply — dairy is not present in any ingredient of the confirmed modified preparation. Rule 2 does not apply — the confirmed use of a clean pan and separation from shared dairy-containing surfaces eliminates the identified `CrossContactSource`. Rule 3 does not apply — the preparation convention that introduced dairy (butter basting) has been confirmed replaced with a dairy-free technique. Rule 4 applies — all three evidence dimensions have been assessed and confirmed clear by the kitchen. `ExposureState(dairy) = ConfirmedAbsent`.

**Safety Inference Outcome:** Permitted. `ExposureState = ConfirmedAbsent` satisfies the Safety Inference Principle.

**Rationale:** This scenario is the confirmed-clear counterpart to Scenario 13. It validates that a modification request can produce `ConfirmedAbsent` — but only when the modification reaches and resolves all three evidence dimensions and the kitchen has confirmed execution. The contrast between Scenarios 13 and 14 is the core validation target of the Modification Request Rule: the difference between a recorded request and a confirmed execution across all introduction pathways.

---

### Scenario 15 — Creamy Mushroom Risotto, Dairy Assessment, Dairy-Free Request (Modification Insufficient)

**FoodItem:** Creamy mushroom risotto. Diner has requested a dairy-free version. Kitchen has confirmed that parmesan will be omitted from plating. No confirmation has been provided about the base preparation, the mantecatura technique, or whether the risotto was pre-cooked in a dairy-containing base.

**Allergen:** Milk (dairy)

**ExposureState Determination:** The request as confirmed addresses only the finishing garnish — parmesan at plating. It does not address the structural preparation method. Risotto is conventionally finished through mantecatura, incorporating butter and parmesan as emulsifying agents during the final cooking stage, not only as toppings. Additionally, risotto is frequently prepared in large batches in restaurant contexts, making individual dairy-free modification operationally complex. The `PreparationContext` dimension has not been resolved, and the batch preparation question introduces an additional unconfirmed `CrossContactSource`. Rule 3 applies. `ExposureState(dairy) = PotentiallyPresent`.

**Safety Inference Outcome:** Blocked. `ExposureState = PotentiallyPresent` does not satisfy the Safety Inference Principle.

**Rationale:** This scenario validates the Modification Request Rule in the context of a structurally dairy-dependent preparation. The risotto case is more complex than the scallop case because dairy is incorporated at multiple stages — the base, the mantecatura, and the garnish — and batch preparation introduces a `CrossContactSource` that is independent of the per-order preparation method. A modification that addresses only one of these pathways is insufficient. This is the second empirically grounded case from Phase 0 that motivated the Modification Request Rule.

---

## Category F — Missing Preparation Context Cases

These scenarios validate the ontology's behavior when preparation context is absent or insufficient. They confirm that the `Unknown` state is correctly assigned when the evidence pathway cannot be completed, and that `ConfirmedAbsent` cannot be reached without a completed `PreparationContext` assessment.

---

### Scenario 16 — "Rich Sauce," Egg Assessment (PotentiallyPresent from Named Preparation Convention)

**FoodItem:** Eggs Benedict served with a sauce described only as "rich sauce" on the menu.

**Allergen:** Eggs

**ExposureState Determination:** Rule 1 does not apply — eggs are not explicitly listed as an ingredient in the sauce. Rule 2 has not been assessed for cross-contact. Rule 3 applies — "rich sauce" in the context of Eggs Benedict canonically refers to hollandaise, which is fundamentally an egg-yolk emulsion. The preparation convention associated with this named dish type provides a specific and identifiable introduction pathway for eggs through the sauce, regardless of whether eggs are listed in the menu description. `ExposureState(eggs) = PotentiallyPresent`.

**Safety Inference Outcome:** Blocked. `ExposureState = PotentiallyPresent` does not satisfy the Safety Inference Principle.

**Rationale:** This scenario validates that preparation conventions associated with named dish types constitute identifiable introduction pathways under Rule 3, even when the allergen is entirely absent from the menu description. The `PreparationContext` dimension requires the system to consult what "rich sauce" means in the context of Eggs Benedict — and that consultation produces a specific, identified pathway. This directly validates the ontology's response to the Preparation-Context Blindness failure category and mirrors the Phase 0 hollandaise prompt performance.

---

### Scenario 17 — Plain Beef Burger No Bun Listed, Wheat Assessment (Unknown from Incomplete Assessment)

**FoodItem:** Beef burger described as "beef patty, lettuce, tomato." No bun is listed. It is not confirmed whether the bun is absent from the dish or simply absent from the description. No information is provided about patty composition, grill sharing, or kitchen cross-contact profile.

**Allergen:** Wheat

**ExposureState Determination:** Rule 1 cannot be applied — it is unknown whether the patty contains wheat binders, and it is unknown whether a bun is present. Rule 2 cannot be evaluated — cross-contact sources have not been assessed. Rule 3 cannot be applied with confidence — without knowing whether a bun is present or what the patty composition is, no specific plausible pathway can be identified or ruled out. Rule 5 applies — the evidence pathway is incomplete. `ExposureState(wheat) = Unknown`.

**Safety Inference Outcome:** Blocked. `ExposureState = Unknown` does not satisfy the Safety Inference Principle.

**Rationale:** This scenario validates a subtle but important point: the absence of a bun in the menu description does not confirm the absence of a bun in the dish, and does not confirm the absence of wheat in the patty or the preparation environment. The description is ambiguous — "no bun listed" could mean bunless or could mean incompletely described. `Unknown` is the correct output because the assessment cannot be completed from the available information. This validates the Omission-as-Absence gap: a missing mention is not an affirmative finding.

---

### Scenario 18 — Vegan Label, Dairy Assessment (PotentiallyPresent from Label Ambiguity)

**FoodItem:** A menu item labeled "vegan." No further ingredient or preparation detail is provided. The kitchen's cross-contact profile is unknown.

**Allergen:** Milk (dairy)

**ExposureState Determination:** Rule 1 does not apply — dairy is not listed. Rule 2 cannot be evaluated — cross-contact sources are unknown. Rule 3 applies — "vegan" is a dietary intent label, not an allergen safety declaration. Vegan labeling operates under different standards than allergen labeling, and cross-contact with dairy-containing items in a non-dedicated kitchen is a plausible and structurally grounded introduction pathway regardless of the dish's intended ingredient composition. The label does not constitute a completed evidence pathway for any of the three dimensions. `ExposureState(dairy) = PotentiallyPresent`.

**Safety Inference Outcome:** Blocked. `ExposureState = PotentiallyPresent` does not satisfy the Safety Inference Principle.

**Rationale:** This scenario validates that dietary labels — vegan, all-natural, organic, plant-based — do not substitute for a completed allergen evidence pathway. A vegan label confirms dietary intent regarding animal product inclusion. It does not confirm ingredient composition at the substance level, it does not confirm cross-contact profile, and it does not confirm preparation context in an allergen safety sense. The cross-contact pathway remains an identified and unresolved risk. This directly validates the Overconfident Safety failure category — the framing of the label as a safety signal is precisely the positive-claim framing that produced OCS failures in the Phase 0 dataset.

---

## Summary

### Inference Outcome Distribution

| Scenario | FoodItem | Allergen | ExposureState | Inference |
|---|---|---|---|---|
| 1 | Grilled chicken, dedicated grill | Peanuts | ConfirmedAbsent | Permitted |
| 2 | Peanut sauce noodle bowl | Peanuts | ConfirmedPresent | Blocked |
| 3 | Caesar salad, housemade dressing | Fish | PotentiallyPresent | Blocked |
| 4 | French fries, shared fryer | Wheat | PotentiallyPresent | Blocked |
| 5 | French fries, dedicated fryer | Wheat | ConfirmedAbsent | Permitted |
| 6 | Grilled salmon, seafood restaurant | Shellfish | PotentiallyPresent | Blocked |
| 7 | Stir-fry with house sauce | Soy | PotentiallyPresent | Blocked |
| 8 | Sautéed green beans, no context | Tree nuts | Unknown | Blocked |
| 9 | Mashed potatoes, whipped with olive oil | Dairy | PotentiallyPresent | Blocked |
| 10 | Chocolate fudge cake, bakery kitchen | Tree nuts | PotentiallyPresent | Blocked |
| 11 | House-baked sourdough | Wheat | ConfirmedPresent | Blocked |
| 12 | Churros, three-ingredient description | Dairy | Unknown | Blocked |
| 13 | Pan-seared scallops, butter-off request only | Dairy | PotentiallyPresent | Blocked |
| 14 | Pan-seared scallops, confirmed full modification | Dairy | ConfirmedAbsent | Permitted |
| 15 | Creamy mushroom risotto, parmesan-off request only | Dairy | PotentiallyPresent | Blocked |
| 16 | Eggs Benedict, "rich sauce" description | Eggs | PotentiallyPresent | Blocked |
| 17 | Beef burger, no bun listed | Wheat | Unknown | Blocked |
| 18 | Vegan-labeled menu item | Dairy | PotentiallyPresent | Blocked |

### Validation Assessment

The suite contains eighteen scenarios across six categories. Justified inference is permitted in three cases — Scenarios 1, 5, and 14 — each of which represents a genuinely completed evidence pathway with kitchen confirmation across all three dimensions. Unjustified inference is blocked in fifteen cases, covering all four Phase 0 failure categories, all four `ExposureState` values, and all six scenario types specified in the Phase 1 roadmap.

The ontology correctly assigns `ConfirmedAbsent` only when all three evidence dimensions have been affirmatively cleared. It correctly assigns `ConfirmedPresent` when allergen presence is known. It correctly assigns `PotentiallyPresent` when a specific and identifiable introduction pathway exists but has not been confirmed. It correctly assigns `Unknown` when the evidence pathway is incomplete and no specific pathway can be identified or ruled out. The Safety Inference Principle correctly blocks inference in all non-`ConfirmedAbsent` cases without exception.

Phase 1 validation conditions are satisfied. The ontology permits justified inference and blocks unjustified inference consistently across the full scenario set. Phase 2 may proceed.
