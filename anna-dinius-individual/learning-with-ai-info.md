# Learning with AI Information

## Original Direction

### Topics

1. Food allergen ontology
2. Market validation & user-focused research

#### Topic 1 Goals

Understand:

- Basic ontology concepts
- Ontology design through the lens of food allergens
- Importance of ontology

> More information can be found on the GitHub repo README

#### Topic 2 Goals

Explore:

- User outreach methods & effectiveness
- User testing methods & applications
- Data collection strategies
- Data analysis

> More information can be found on the GitHub repo README

## What I Actually Learned (Updated Direction)

My final learning direction shifted to:

1. User testing strategies for an MVP in a realistic, time-constrained context
2. SCSS maintainability and improvement opportunities in the NomNom Safe codebase

### Topic 1: User Testing Strategies (What I Learned)

Through Weeks 10 and 11, I used Gemini, ChatGPT, Copilot, and Claude to compare and synthesize user-testing methods for the business side of NomNom Safe.

I learned that:

- Strong testing plans combine qualitative depth (why users behave a certain way) with quantitative validation (how often something happens)
- For my constraints (3 weeks, remote scheduling challenges, real business-owner participants), the most practical sequence is:
  - heuristic/AI-assisted pre-checks
  - a small set of remote moderated usability sessions
  - unmoderated validation + short survey/analytics follow-up
- A small moderated sample can still surface major usability issues effectively
- AI is most useful for synthesis, prioritization, and setup support—not for replacing real user testing sessions

### Topic 2: SCSS Features, Fixes, and Improvement Suggestions (What I Learned)

In Week 13, I used AI to review SCSS usage and identify maintainability improvements for the business-side UI.

I learned that:

- The codebase already has solid foundations (design tokens, shared mixins, and a strong map+mixin example in checkbox styling)
- Important consistency gaps exist, including mixed `@import`/`@use`, repeated hardcoded values, inconsistent breakpoint usage, and undefined tokens
- High-impact improvements should be prioritized in this order:
  1. Fix undefined CSS variables and duplicate style definitions
  2. Add/standardize semantic tokens for borders, shadows, and surfaces
  3. Unify responsive breakpoints
  4. Gradually migrate to Sass module patterns (`@use`/`@forward`)
  5. Split large global styling into cleaner partials
- SCSS quality affects long-term maintainability, consistency, and collaboration speed—not just visual output

### Overall Outcome

I originally planned to focus on food allergen ontology and broader market validation, but my actual work centered on practical product testing strategy and maintainable front-end styling. AI was most valuable when used as a research and analysis assistant, while final judgment and prioritization remained human-led.
