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

## Updated Direction

### Topics

1. User testing strategies
2. SCSS

### Topic 1 Goals

- Gather information on most effective user testing methods, including benefits, drawbacks, how they work, and best context to use them in
- Understand basic user testing methods
- Analyze the usefulness of each method within the context of NomNom Safe (Business Side)
- Develop a testing strategy for NomNom Safe (Business Side)

### Topic 2 Goals

- Analyze current use of SCSS in NomNom Safe (Business Side) codebase
- Identify areas for improvement
- Identify and understand unused SCSS features that will improve the quality and maintainability of the stylesheets
- Develop a priority order for implementing improvements

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
