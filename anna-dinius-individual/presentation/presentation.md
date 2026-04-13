---
marp: true
theme: default
paginate: true
class: lead
---

# Learning With AI — Topics & Findings

- Presenter: Anna Dinius
- Course: ASE 485 — Learning With AI
- Date: April 2026

---

## Learning Topics

- User Testing Strategies (learned via AI)
- SCSS: features, fixes, and improvement suggestions

---

## How I Used AI This Project

- Consulted Gemini, ChatGPT, Copilot, and Claude for cross‑validation
- Used AI to learn and synthesize user testing strategies (did not run user tests)
- Used AI to generate SCSS guidance and prioritized fixes for the codebase

---

## What I Use AI For

- Summarizing methods and tradeoffs for user testing
- Producing a short testing roadmap adaptable to constraints
- Reviewing SCSS: identifying tokens, mixins, and refactor candidates
- Drafting concise next steps and priorities

---

## My Approach to Using AI to Learn

- Start with targeted prompts and consult multiple models
- Treat AI outputs as draft guidance requiring human judgement
- Focus on actions that fit time and participant constraints
- Prioritize fixes that reduce friction for future testing or deployment

---

## Key Findings — User Testing Strategies

- Consolidated ~17 strategies into practical combinations and sequences
- Best balance for constraints: heuristic audit → 5 remote moderated tests → unmoderated validation
- AI audits are effective pre‑test cleanup; human sessions remain essential for depth
- Rule of 5: small moderated samples uncover most major usability issues

---

## Week-by-Week Summary

- Week 10 — Collected testing strategies from Gemini, ChatGPT, Copilot
- Week 11 — Consolidated guidance; produced recommended testing sequences
- Week 12 — Break; pivoted to using AI for learning rather than running tests
- Week 13 — Reviewed SCSS and produced improvement priorities for the codebase

---

## SCSS Findings — Strengths

- Design tokens in `:root` and reusable responsive mixins exist
- Good example patterns: `Checkbox.scss` uses maps + mixins

---

## SCSS Findings — Gaps

- Mixed `@import` and `@use`; inconsistent breakpoints and hardcoded colors
- Missing tokens (`--check-box`, `--light-salmon`) and duplicated global rules
- Large `global.scss` with mixed concerns

---

## SCSS Recommendations (prioritized)

1. Fix undefined CSS variables and remove duplicate rules
2. Introduce a small set of semantic tokens for borders/shadows
3. Unify breakpoints (use `respond-to` / breakpoint map)
4. Gradually migrate `@import` → `@use` for shared partials
5. Split `global.scss` into partials once tokens and breakpoints stabilize

---

## Recommended Next Steps

1. Run an AI‑assisted heuristic audit to clean MVP before recruiting
2. If scheduling is possible: conduct 5 remote moderated sessions for depth
3. Run unmoderated tests + short surveys to validate fixes at scale
4. Implement SCSS quick fixes (tokens, breakpoints) to reduce UI drift

---

## Resources & Notes

- Sources: `roadmap.md`, `user-testing-methods.md`, `prompts-and-responses.md`, `learning-progress.md`, `scss-*` files
- AI consulted: Gemini, ChatGPT, Copilot, Claude
