# SCSS improvement suggestions (NomNom Safe — Business-Side)

This document summarizes findings from a review of styles under **`client/`** only. The **`server/`** tree in this repository contains no `.scss`, `.sass`, or component CSS files, so there is nothing to analyze there from an SCSS perspective.

## Current strengths

- **Design tokens in CSS custom properties** (`client/src/styles/_variables.scss`): Colors, radii, borders, and typography are largely centralized on `:root`, which supports theming and runtime overrides better than SCSS variables alone.
- **Shared responsive mixin** (`respond-to` in `_mixins.scss`): Used consistently in `global.scss`, auth flows, layout, and several components.
- **Reusable layout and button mixins**: `full-height-minus-header`, `button-responsive`, `card-base`, and `panel-section` reduce repeated layout math.
- **Advanced SCSS in one place**: `client/src/components/common/Checkbox/Checkbox.scss` uses **maps** (`$themes`, `$sizes`) and parameterized **mixins** (`checkbox-base`, `checkbox-theme`), which is a good pattern to replicate elsewhere.

## Gaps and inconsistencies

### 1. Module system: `@import` vs `@use`

Most files use **`@import`** (with mixed paths: `variables` vs `variables.scss`). A few use **`@use`** (`SignInForm.scss`, `SignUpForm.scss`, `SplashScreen.scss`). Sass is moving toward **`@use` / `@forward`** for clearer namespaces, load-once semantics, and easier dead-code reasoning.

**Suggestion:** Gradually migrate shared partials to `@use` with namespaces (e.g. `@use '../styles/variables' as vars`) and `@forward` a single `styles/_index.scss` entry if you want one import line per component.

### 2. Hardcoded colors and shadows vs tokens

Many component files repeat hex/rgba values that overlap with or drift from `_variables.scss`, for example:

- Borders and neutrals: `#ccc`, `#eee`, `#ddd`, `#555`, `#f9f9f9`, `#f0f0f0`
- Overlays and shadows: `rgba(0, 0, 0, 0.08)`–`0.15`, `rgba(0, 128, 128, 0.08)` for focus/hover rings
- One-off brand colors: e.g. `#35a07b` on hover in `MenuCard.scss` instead of `var(--mint-green-dark)` or a dedicated token

**Suggestion:** Extend `:root` with semantic tokens (e.g. `--border-subtle`, `--surface-muted`, `--shadow-card`, `--focus-ring-turq`) and replace literals in high-traffic components first (`MenuItemPanel`, `MenuCard`, `Modal`, `Step1`).

### 3. Undefined or missing CSS variables

- `global.scss` uses `var(--check-box)` for `.unchecked`, but **`--check-box` is not defined** in `_variables.scss` (likely a bug or leftover name).
- `MenuItemPanel.scss` references **`var(--light-salmon)`**, which is not declared in `_variables.scss`.

**Suggestion:** Add the missing tokens to `_variables.scss` or align names with existing variables (`--salmon-light`, etc.) so behavior is predictable across builds.

### 4. Duplicated global rules vs component SCSS

`FormField.scss` redefines `.form-field-container` and `.required`, which already exist in `global.scss`. That splits the “source of truth” and risks divergence.

**Suggestion:** Keep one definition (prefer component file + delete from global, or the reverse) and use **BEM-style block classes** only in the component that owns them.

### 5. Competing button patterns

`global.scss` defines both `.button` (using `button-responsive`) and `.btn` with overlapping responsibilities and **absolute positioning** on `.btn`, which is fragile for reuse.

**Suggestion:** Consolidate on one button abstraction (mixin + modifiers) and reserve layout (e.g. `position`) for parent-specific classes.

### 6. Mixed responsive strategies

Some files use `@include respond-to(...)`; others use raw **`@media (max-width: 600px)`** (e.g. `MenuItemPanel.scss`). Breakpoints then diverge from `$breakpoint-mobile` / `$breakpoint-tablet`.

**Suggestion:** Prefer the shared mixin (or a single map of breakpoints + a generic `@mixin media($key)`) so tablet/mobile behavior stays aligned.

### 7. `global.scss` size and legacy blocks

`global.scss` mixes resets, typography, forms, setup flows, menus, and utilities. Large globals increase **specificity wars** and make it harder to see what a given screen actually loads.

**Suggestion:** Split into partials (`_base.scss`, `_forms.scss`, `_setup.scss`, …) and `@use` them from `global.scss`, or move screen-specific rules next to their routes/components.

### 8. Font loading inside `_variables.scss`

Google Fonts are loaded via **`@import url(...)`** inside `_variables.scss`. That couples tokens with network I/O and can affect **privacy** (request to Google) and **CSP** (`style-src` / font policies).

**Suggestion:** Load fonts from `public/index.html` (link tags) or a dedicated `_fonts.scss` imported once from `global.scss`; document CSP and `font-src` if you harden production hosting.

### 9. Security and safety (practical notes)

SCSS itself does not “secure” the app, but styling choices interact with **Content-Security-Policy**, **third-party font CDNs**, and (if you ever build CSS from user strings) **injection**. Today’s codebase compiles SCSS at build time, which is safe; avoid interpolating untrusted user input into inline styles or dynamically generated style tags without sanitization.

### 10. Features you are not yet using (high value for this project)

| Feature / practice                                                            | Why it fits NomNom Safe                                                                                         |
| ----------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------- |
| **`@use` / `@forward`**                                                       | Cleaner imports, avoids duplicate global CSS when partials grow                                                 |
| **Breakpoint map + loop**                                                     | One list of breakpoints; less copy-paste than string `respond-to` branches                                      |
| **`@mixin` + content for focus-visible**                                      | Consistent accessible focus rings (replacing repeated `box-shadow: 0 0 0 2px rgba(...)` blocks)                 |
| **`color-mix()` in CSS** (supported in your target browsers via browserslist) | Derive hover/focus tints from `var(--turq)` without hardcoding rgba                                             |
| **`@function` for spacing/type scale**                                        | e.g. `spacing(2) → 8px` for rhythm across forms and cards                                                       |
| **`%placeholder` + `@extend`** (use sparingly)                                | Deduplicate identical selector groups if many components share the same “card shell”                            |
| **Module-level `$_private` variables**                                        | With `@use`, hide implementation details of mixins                                                              |
| **Sass built-ins: `map.get`, `meta.type-of`, `color.adjust`**                 | Modern replacement for older `map-get` / `darken()` patterns (still valid; `color.adjust` is clearer long-term) |

### 11. Complexity already done well (expand the pattern)

`Checkbox.scss` demonstrates **maps + theme mixins**. Similar patterns would suit:

- Toast variants (success / warning / error borders already partially custom in `Toast.scss`)
- Status badges and tags in menu items
- Primary / secondary / danger buttons

## Suggested priority order

1. **Fix undefined CSS variables** (`--check-box`, `--light-salmon`) and remove duplicate `.form-field-container` / `.required` definitions.
2. **Introduce a small set of semantic tokens** for borders, surfaces, and shadows; replace the most repeated hex values in `MenuItemPanel`, `MenuCard`, and `Modal`.
3. **Unify breakpoints** (replace stray raw `@media` with `respond-to` or a map-driven mixin).
4. **Plan `@import` → `@use` migration** for new files first, then convert high-churn partials.
5. **Split `global.scss`** once the above stabilizes, to reduce merge conflicts and mental load.

## Companion document

For explanations of each SCSS feature, difficulty, and how to apply it in this project, see **`scss-features-reference.md`** in the same folder.
