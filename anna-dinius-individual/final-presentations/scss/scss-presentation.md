---
marp: true
theme: default
paginate: true
class: lead
title: ASE 485-001 Learning w/ AI Presentation on SCSS
---

# Learning With AI

## Topic: SCSS

### What I Learned

> Anna Dinius

---

## AI Consulted

- Cursor IDE agents

---

## My Learning Process Using AI

> See the [`week13`](https://github.com/jeffreyperdue/Learning-With-AI-ASE485/tree/main/anna-dinius-individual/week13) folder in the GitHub repo for more details on prompts and responses

1. Consulted Cursor IDE agents to analyze the use of SCSS throughout the Business Side codebase to:
   - Identify areas for improvement, including bugs and other issues
   - Identify and explain unused SCSS features that would improve the quality and maintainability of the stylesheets
2. Read through the full response to understand the issues and how to fix them
3. Asked clarifying questions for anything that was uncertain to gain a better understanding

---

## Suggestions for Improvement

> See [`scss-improvement-suggestions.md`](https://github.com/jeffreyperdue/Learning-With-AI-ASE485/blob/main/anna-dinius-individual/week13/scss-improvement-suggestions.md) for details on each suggestion

1. **`@use` / `@forward`** - for clean imports & reduced duplication
2. **Breakpoint map + loop** - for consistency & maintainability
3. **`@mixin` + content for focus-visible** - for UI consistency
4. **`color-mix()` in CSS** - to avoid hardcoding rgba
5. **`@function` for spacing/type scale** - for consistency
6. **`%placeholder` + `@extend`** - for deduplication
7. **Module-level `$_private` variables** - for encapsulation
8. **Sass built-ins: `map.get`, `meta.type-of`, `color.adjust`** - for long-term clarity

---

## Features

### Total: 13

> See [`scss-features-reference.md`](https://github.com/jeffreyperdue/Learning-With-AI-ASE485/blob/main/anna-dinius-individual/week13/scss-features-reference.md) for details on each feature

1. `@use` and `@forward` (module system)
2. Maps (`$map: (key: value)`) and `map.get`
3. Mixins (`@mixin` / `@include`) and `@content`
4. Functions (`@function`)
5. Placeholder selectors (`%name`) and `@extend`
6. Nesting, `&`, and BEM
7. Built-in color modules: `color.adjust`, `color.scale`, vs `darken()` / `lighten()`

---

## Features (continued)

8. CSS `color-mix()` (with design tokens)
9. Breakpoint maps and generated media queries
10. CSS custom properties vs Sass variables (strategic use)
11. Partial file organization and the “global bundle”
12. Loading fonts and CSP / privacy
13. “Security” in styling (realistic scope)
