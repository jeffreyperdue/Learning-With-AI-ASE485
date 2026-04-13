---
marp: true
theme: default
paginate: true
class: lead
title: ASE 485-001 Learning w/ AI Presentation
---

# Learning With AI

> Anna Dinius

---

## Learning Topics

1. User Testing Strategies
2. SCSS: features, fixes, and improvement suggestions

---

## How I Used AI

- Cross-validation for top user testing strategies
  - Consulted Gemini, ChatGPT, Copilot, and Claude
- Learn and synthesize user testing strategies
- Generate SCSS guidance
  - Prioritize future fixes for the codebase

---

## What I Use AI For

- Summarizing methods and tradeoffs for user testing strategies
- Producing a short testing roadmap adaptable to constraints
- Reviewing SCSS: identifying tokens, mixins, and refactor candidates
- Drafting concise next steps and priorities

---

## How I Learned Using AI

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

## Questions?
