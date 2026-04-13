---
marp: true
theme: default
paginate: true

---

## Spring 2026 · Jeffrey Perdue

# Learning With AI

Subtitle will go here

---
<!-- _class: light -->

## The Central Idea

# Answer Machine vs. Thinking Partner

| ❌ Answer Machine | ✅ Thinking Partner |
|---|---|
| Ask a question | Probe the reasoning |
| Accept the output | Push back on the output |
| Move on | Ask for the edge cases |
| Never understand the 'why' | Restate in your own words |
| Knowledge doesn't compound | Build a mental model |

> *The AI shapes the path and flags the wrong turns. You do the thinking.*

---


# How My Coding Sessions Evolved

| Stage | When | What Changed |
|---|---|---|
| 1 — One-Shot | Spring '25 | "Implement X." Pure vibe coding. |
| 2 — + Requirements | Summer '25 | SRS doc first, then implement with it as context. |
| 3 — + Design Review | Fall '25 | SOLID & GoF check before coding. Two or more LLMs debate the approach. |
| 4 — + Edge Cases | Winter '26 | Critical edge-case analysis and self-verified tests. |
| **5 — + Narrow scope considerably per "prompt"** | **Spring '26** | **Single-function scope. Precision over breadth.** |

---
<!-- _class: light -->


# A Typical Session Loop

1. **Get the concept** — Definition + concrete example. No abstract rules without a worked case first.
2. **Break it aggressively** — "Show me step-by-step." "What law is used here?" Demand granularity until it clicks.
3. **Stress test it** — "Why isn't this the same as X?" "What are the edge cases?" "What are other alternatives and why did you not choose them" Push to failure.
4. **Take comprehensive notes** — Break everything down to natural language I fully understand. I use Notion but am exploring Obsidian.
5. **Extract the pattern** — "When do I use this?" Build the decision rule, not just the solution.
6. **Take comprehensive notes** — Flashcards / Notion blocks. Documentation-driven retention combined with spaced repetition.

---

## The AI Tools I Use and Their Primary Personal Use Cases

| Tool | Share | Primary Use |
|---|---|---|
| **Claude** | ~65% | Primary reasoning & validation, idea refinement, code review via Cursor/Sonnet, first stop for LLM output checks |
| **ChatGPT** | ~15% | Large-context project files, initial reasoning partner, high file-size document work |
| **NotebookLM** | ~10% | Research synthesis, audio overview / podcast generation |
| **Gemini / Perplexity / etc.** | ~10% | Image creation, research verification, specialized micro-tools |

---
<!-- _class: light -->

## Personal Takeaways

- **Always ask for an example first** — Don't move on from an explanation until you've seen it applied to something real. Examples make abstract ideas stick.
- **Say it back/Type ot out in your own words** — After getting an explanation, try to summarize it yourself. If you struggle, you probably need another pass at it.
- **Use a structure, don't just explore** — Having a clear set of steps to follow gets you further than open-ended tinkering. Structure keeps you moving.
- **Write things down as you learn them** — You'll remember more if you capture the idea somewhere while it's fresh, rather than trusting you'll recall it later.
- **Start smaller than you think you need to** — Picking a narrow, specific thing to build teaches you more than trying to build something big all at once.


---
<!-- _class: light -->

## Learning With AI Topics - Menu Import / Parsing

# NomNomSafe · AI-Assisted Menu Import

**The problem:** Restaurant owners onboard by entering menu items manually — but their menus already exist as PDFs, spreadsheets, and Word docs.

**The solution:** Upload a file → AI extracts and structures items → human reviews before anything saves.

| Layer | What's There |
|---|---|
| File parsing | PDF (`pdf-parse`), CSV, DOCX (`mammoth`) via Express + `multer` |
| AI service | Gemini 2.5 Flash — temp 0.1, structured JSON output |
| Review step | Every parsed item is editable and requires explicit confirmation |
| Client API | `menuImportApi.importFile()` wired in React |

**Live tests:** Had LLM (Claude) create a few menus of different types -> all items parsed, categories inferred, allergens correctly extracted

---

<!-- _class: light -->

## Learning With AI Topics - Menu Import / Parsing

# The Critical Rule in the Prompt

AI's default behavior on allergens: *"not listed = probably safe."*
That's called **Omission-as-Absence** and it's wrong.

The fix lived in the system prompt itself:

> *"For `possible_allergens`, only include allergens that are explicitly stated
> in the source text. If allergen information is absent or ambiguous, return `[]`.
> Do NOT guess based on ingredients."*

**Four failure modes studied (Phase 0 research):**
1. Overconfident Safety — asserts "safe for X" without evidence
2. Omission-as-Absence — treats no mention as a confirmation of absence
3. Cross-Contact Blindness — ignores shared fryer / prep surface risk
4. Preparation-Context Blindness — ignores how a dish is cooked

**Design principle:** AI suggestions are surfaced for human confirmation.
Nothing is saved without explicit review. "Unknown" is never collapsed to "safe."



---
<!-- _class: light -->

## Learning With AI Topics - Ontology (Process)

# How AI Helps Me Solve the Problem

**My role:** define the problem, evaluate outputs, and decide what counts as valid evidence.

**AI’s role:**
- Analyze failure patterns across prompts (overconfidence, omission-as-absence, cross-contact blindness, preparation-context blindness)
- Generate counterexamples and edge cases that stress-tested my assumptions
- Help convert informal menu ambiguity into structured ontology concepts
- Rephrase technical ideas clearly so I could iterate faster and communicate better

**Process outcome:**  
AI accelerated iteration, but I kept the decision boundary: no rule was accepted without structural justification.

---
<!-- _class: light -->

## Learning With AI Topics — Ontology (Outcome)

# Food Allergen Ontology · Evidence Before Safety

**What I built from that process:**
- Core entities: `FoodItem`, `Ingredient`, `Allergen`, `PreparationContext`, `CrossContactSource`
- Exposure states: `ConfirmedPresent`, `PotentiallyPresent`, `Unknown`, `ConfirmedAbsent`
- Scenario-based validation on ambiguous real-menu style cases

**Safety inference gate (key rule):**  
Infer “safe” **only if** `ExposureState = ConfirmedAbsent`.

**What this prevents:**
- “Not listed = safe” assumptions
- Ignoring shared equipment / cross-contact pathways
- Ignoring preparation method as an allergen pathway
- Confident answers when evidence is incomplete

**Takeaway:**  
The ontology constrains AI reasoning under uncertainty, so outputs are evidence-based instead of confidence-based.



