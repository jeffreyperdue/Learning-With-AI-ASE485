# SCSS features and practices — reference for NomNom Safe (Business-Side)

This document expands on **`scss-improvement-suggestions.md`**. It explains how each SCSS/CSS feature works, approximate difficulty, and how it applies to this React client (Sass **1.87.x**, Create React App / `react-scripts`).

**Scope:** Styling lives under `client/src`. The `server/` folder has no SCSS in this repo.

---

## 1. `@use` and `@forward` (module system)

### How it works

- **`@use 'path/to/file'`** loads a Sass module **once** per compilation chain, exposes its members with a **namespace** (by default the filename), and makes top-level CSS from that file run once when first used.
- **`@forward 'path'`** re-exports another module so consumers can import a single “barrel” file.

Example (conceptual):

```scss
// styles/_index.scss
@forward 'variables';
@forward 'mixins';

// Component.scss
@use '../../styles' as *; // if you forward everything with care
// or
@use '../../styles/mixins' as m;
@include m.respond-to(mobile) { ... }
```

### Difficulty

**Medium** for a one-time migration; **low** for new files if you start fresh with `@use` only.

### Why use it in this project

You currently mix `@import` and `@use`. Unifying on `@use` reduces accidental **duplicate CSS** when the same partial is imported through multiple paths and makes it obvious which file “owns” a variable or mixin.

### Pitfalls

- Members are **namespaced** unless you `as *` (use sparingly to avoid name clashes).
- **`@import` of files that only contain CSS custom properties** still works, but migrating `_variables.scss` may require adjusting how you reference `$breakpoint-mobile` vs `var(--turq)`.

---

## 2. Maps (`$map: (key: value)`) and `map.get`

### How it works

Maps store structured data. In modern Sass, prefer **`map.get($map, $key)`** (the **module** syntax) over legacy `map-get` (still works but the module system is the direction of the language).

Your **`Checkbox.scss`** already uses maps for `$themes` and `$sizes`, which is idiomatic.

### Difficulty

**Low** once you are comfortable reading nested maps.

### Why use it in this project

You have repeated visual families:

- Button variants (primary, secondary, danger)
- Toast / alert variants
- Tag/badge colors on menu items

A **single map** (or one map per concern) plus a small mixin can replace copy-pasted blocks and keep tokens aligned with `_variables.scss`.

### Example direction (not drop-in code)

```scss
$button-variants: (
	primary: (
		bg: var(--mint-green),
		bg-hover: var(--mint-green-dark),
	),
	danger: (
		bg: var(--delete-red),
		bg-hover: ...,
	),
);
```

---

## 3. Mixins (`@mixin` / `@include`) and `@content`

### How it works

- **Mixins** emit CSS at the inclusion site; they can take arguments and optional **blocks** via `@content`.

```scss
@mixin focus-ring($color: var(--turq)) {
	&:focus-visible {
		outline: none;
		box-shadow: 0 0 0 2px $color;
		@content;
	}
}

.my-button {
	@include focus-ring;
}
```

### Difficulty

**Low** for simple mixins; **medium** when combining responsive + themes + `@content`.

### Why use it in this project

You repeat **focus / hover rings** with slightly different rgba values in `Step1.scss`, `AddressFields.scss`, `MenuCard.scss`, etc. A **`focus-ring`** mixin (and optionally a **`transition-interactive`** mixin) centralizes behavior and makes **keyboard focus** styling consistent for accessibility.

Your existing **`respond-to`** and **`button-responsive`** mixins are the right idea; extend that pattern to rings, cards, and form controls.

---

## 4. Functions (`@function`)

### How it works

`@function` returns a value (number, color, string) for use in properties. Unlike mixins, functions **do not** emit CSS by themselves.

```scss
@use 'sass:math';

@function spacing($n) {
	@return $n * 8px;
}

.card {
	padding: spacing(2); // 16px
}
```

### Difficulty

**Low** for math helpers; **medium** if you encode design-system logic (ramps, fluid type).

### Why use it in this project

Spacing in the codebase mixes `px`, `rem`, and arbitrary values. A small **spacing scale** function (or CSS variables like `--space-1` … `--space-6`) improves rhythm between `SetUp`, forms, and menu panels without changing visuals drastically.

---

## 5. Placeholder selectors (`%name`) and `@extend`

### How it works

- **`%semantic-name { ... }`** defines a “silent” class: it does not emit CSS until something **`@extend %semantic-name`**.
- **`@extend`** merges selectors, which can reduce duplication but can also create **unexpected specificity** or bloated selectors if overused.

### Difficulty

**Medium** (easy to misuse).

### Why use it in this project (sparingly)

If many components share an identical **card chrome** (border, radius, shadow) and you do not want a mixin to duplicate declarations, one **`%card-shell`** plus `@extend` in each BEM block can work. Prefer **mixins** when the shared block needs parameters or when `@extend` would cross too many unrelated components.

**Rule of thumb:** Use `@extend` only within a **small family** of related selectors (same BEM block or same folder).

---

## 6. Nesting, `&`, and BEM

### How it works

Sass nesting builds selectors with **`&`** representing the parent. You already use BEM-style elements/modifiers (e.g. `.menu-card__title`, `.modal__header`).

### Difficulty

**Low**.

### Why it matters here

Deep nesting (many levels) makes overrides harder and obscures the real CSS selector. Keep nesting to **2–3 levels** in most component files; flatten when readability drops.

---

## 7. Built-in color modules: `color.adjust`, `color.scale`, vs `darken()` / `lighten()`

### How it works

- Legacy **`darken($color, 10%)`** is still available.
- Modern Sass encourages **`sass:color`** with **`color.adjust`** or **`color.scale`** for predictable, perceptual adjustments.

`MenuItemPanel.scss` uses `darken(#dc3545, 10%)` and `darken(#3eb489, 5%)` — effective, but tied to **hex literals** instead of tokens.

### Difficulty

**Low** to swap APIs; **medium** to move those colors to CSS variables (see `color-mix` below).

### Why use it in this project

Aligning hover/darken steps with **`var(--mint-green)`** avoids one-off greens/reds that drift from the palette.

---

## 8. CSS `color-mix()` (with design tokens)

### How it works

`color-mix(in srgb, var(--turq) 8%, transparent)` produces a tint similar to your repeated `rgba(0, 128, 128, 0.08)` without hardcoding RGB.

### Difficulty

**Low** per usage; **medium** to verify across your **browserslist** (your production list is modern enough for broad `color-mix` support; always spot-check Safari/older targets if you expand the list).

### Why use it in this project

`MenuCard.scss`, `Step1.scss`, and others duplicate turquoise tints. **`color-mix`** keeps hover states **derivable from tokens**, so a future palette tweak updates every mixed state automatically.

---

## 9. Breakpoint maps and generated media queries

### How it works

Store breakpoints in a map, then either:

- Loop `@each` to emit utility classes, or
- Provide one **`@mixin mq($name)`** that reads from the map.

Your **`respond-to`** mixin uses named tiers (`mobile`, `tablet`, `desktop`). A map version avoids long `@if / @else if` chains and matches how you already centralize **`$breakpoint-mobile`** / **`$breakpoint-tablet`**.

### Difficulty

**Medium** to refactor existing mixin; **low** for new code if designed once.

### Why use it in this project

`MenuItemPanel.scss` uses **`max-width: 600px`**, which is not the same as **`$breakpoint-mobile` (640px)**. A single source of truth removes subtle layout differences between screens.

---

## 10. CSS custom properties vs Sass variables (strategic use)

### How it works

- **Sass variables (`$x`)** are resolved at **compile time**.
- **CSS variables (`var(--x)`)** are resolved at **runtime** in the browser.

Your `_variables.scss` correctly uses **`:root`** for theme colors and Sass `$` only for breakpoints and header heights used inside `calc` / media queries.

### Difficulty

**Low** conceptually; **medium** when mixing (e.g. passing `var(--mint-green)` into `darken()` — **avoid**; use `color-mix` or predefine hover tokens).

### Why it matters

Runtime tokens enable **future theming** (dark mode, white-label) without recompiling Sass. Keep **layout constants** that Sass must manipulate in **`$`** or plain values.

---

## 11. Partial file organization and the “global bundle”

### How it works

Split large entry files into **`_partial.scss`** files and compose them from **`global.scss`**. With `@use`, each partial’s CSS runs according to the dependency graph.

### Difficulty

**Medium** (mostly file moves and import updates).

### Why use it in this project

`global.scss` is long and mixes concerns. Partials improve **code review** and onboarding: e.g. `_typography.scss`, `_forms.scss`, `_layout.scss`, `_utilities.scss`.

---

## 12. Loading fonts and CSP / privacy

### How it works

`@import url('https://fonts.googleapis.com/...')` inside Sass triggers a **browser request** for the stylesheet (and then font files). This is not “SCSS security,” but it affects **privacy** (Google sees requests) and **CSP** directives (`style-src`, `font-src`).

### Difficulty

**Low** to move the `<link>` to `public/index.html`; **medium** if you self-host fonts and update CSP.

### Why use it in this project

Separating **fonts** from **`_variables.scss`** keeps tokens file pure and makes security headers easier to reason about in production.

---

## 13. “Security” in styling (realistic scope)

SCSS compilation is **build-time** in this app, so there is no SCSS-specific injection surface. Relevant practices:

- **Never** build CSS strings from untrusted user content at runtime without sanitization.
- Align with **CSP** when you add inline styles or third-party CSS.
- Be cautious with **`url()`** values if they ever become dynamic (path traversal / unexpected requests are rare in React but worth noting).

---

## Difficulty cheat sheet

| Topic                                        | Difficulty | Effort in this codebase             |
| -------------------------------------------- | ---------- | ----------------------------------- |
| New mixin for focus rings                    | Low        | Small; high payoff                  |
| Semantic CSS variables for neutrals/shadows  | Low–medium | Medium; many find-replace passes    |
| Replace raw `@media` with shared breakpoints | Low        | Small per file                      |
| `@use` migration                             | Medium     | Incremental; test each screen       |
| Split `global.scss`                          | Medium     | Mechanical but touches many imports |
| `color-mix` for tints                        | Low–medium | Per component; verify browsers      |
| Map-driven button/toast themes               | Medium     | Similar to Checkbox pattern         |

---

## How this ties to files you already have

| File                                                  | Role                                         |
| ----------------------------------------------------- | -------------------------------------------- |
| `client/src/styles/_variables.scss`                   | Tokens + Sass layout vars; font import today |
| `client/src/styles/_mixins.scss`                      | Breakpoints, buttons, layout helpers         |
| `client/src/styles/global.scss`                       | App-wide base + many legacy/utility rules    |
| `client/src/components/common/Checkbox/Checkbox.scss` | Reference implementation for maps + mixins   |

Use **Checkbox.scss** as a template when introducing **variant-driven** styling elsewhere: define data in maps, keep mixins small, and expose plain BEM classes to React components.

---

## Additional reading (official)

- [Sass documentation: @use](https://sass-lang.com/documentation/at-rules/use)
- [Sass documentation: mixins](https://sass-lang.com/documentation/at-rules/mixin)
- [MDN: color-mix()](https://developer.mozilla.org/en-US/docs/Web/CSS/color_value/color-mix)
