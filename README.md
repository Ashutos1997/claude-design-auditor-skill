# 🎨 Design Auditor — Claude Skill

A Claude skill that audits designs against **18 professional design categories** — built for developers, non-designers, and anyone who wants expert design validation without needing to know the rules themselves.

Works with **Figma files** (via Figma MCP), **code** (HTML/CSS/React/Vue), **screenshots**, and written descriptions. Supports **English and Korean**.

Compatible with **Claude**, **Manus**, and other agents that support SKILL.md-based skills.

---

## What It Does

Drop in a Figma link, paste your CSS, or upload a screenshot — Design Auditor checks your work against 18 categories of design rules and gives you:

- A **score out of 100** with per-category breakdown
- A separate **Accessibility Score** (WCAG coverage across Cat 2, 6, 7, 16)
- A separate **Ethics Score** (dark patterns and manipulative design across Cat 18)
- Issues ranked by severity (🔴 Critical / 🟡 Warning / 🟢 Tip)
- **Plain-language explanations** of *why* each rule matters
- An **Issue Priority Matrix** — every issue plotted by effort vs. impact
- Before/after code diffs when fixing issues in HTML/CSS/React/Vue
- Direct fixes in your **Figma file** via Figma MCP
- **Developer handoff report** — CSS spec table, a11y checklist, critical fixes
- **Export report as Markdown** — ready for Notion, GitHub, Linear, or Jira

---

## The 18 Audit Categories

| # | Category | What It Checks |
|---|---|---|
| 1 | **Typography** | Hierarchy, font count, size, line height, contrast |
| 2 | **Color & Contrast** | WCAG ratios, semantic color use, palette consistency |
| 3 | **Spacing & Layout** | 8-point grid, proximity, alignment, whitespace |
| 4 | **Visual Hierarchy** | Primary action clarity, reading patterns, size/contrast mapping |
| 5 | **Consistency** | Component reuse, icon families, corner radius, interaction states |
| 6 | **Accessibility (A11y / WCAG)** | Touch targets, focus states, alt text, form labels, reading order |
| 7 | **Forms & Inputs** | Labels, sizing, validation timing, error placement, submit states |
| 8 | **Motion & Animation** | Purpose, duration, easing, reduced-motion support |
| 9 | **Dark Mode** | Not just inverted, surface elevation, saturation, icon legibility |
| 10 | **Responsive & Adaptive** | Breakpoints, overflow, touch targets, type scaling |
| 11 | **Loading, Empty & Error States** | Skeletons, empty state anatomy, error levels, success confirmation |
| 12 | **Content & Microcopy** | Button labels, error messages, tone consistency, terminology |
| 13 | **Internationalization & RTL** | Text expansion, RTL mirroring, locale-aware formatting, font support |
| 14 | **Elevation & Shadows** | Shadow scale, elevation hierarchy, dark mode depth |
| 15 | **Iconography** | Icon families, optical sizing, touch targets, meaning consistency |
| 16 | **Navigation Patterns** | Tabs, breadcrumbs, back buttons, mobile nav, active states |
| 17 | **Design Tokens & Variables** | Semantic naming, hardcoded values, dark mode token swapping |
| 18 | **Ethical Design & Dark Patterns** | Confirmshaming, false urgency, pre-checked consent, CTA hierarchy inversion, privacy zuckering, hidden costs, and 15 more manipulative patterns across 5 groups |

---

## Who It's For

- **Developers** building UIs who don't have a design background
- **Designers** who want a fast second opinion or WCAG check
- **Teams** using Figma MCP or VS Code who want design validation in their workflow
- **Product managers and founders** who want to ship ethical, accessible products
- **Anyone** who's ever wondered "does this look right?"

---

## How to Install

1. Download [`design-auditor.skill`](../../releases/latest) from the releases page
2. Go to [Claude.ai](https://claude.ai) → **Customize** → **Skills**
3. Click **Upload skill** and select the file
4. Done — the skill is now active in your conversations

---

## How to Use

Once installed, just talk to Claude naturally:

**English:**
```
"Check my design" → choose scope (full / quick / custom), then audit
"Is this accessible?" → accessibility-focused audit
"Review my form" → partial audit, relevant categories only
"Does this follow WCAG?" → contrast & a11y audit
"Check my Figma file: [link]" → Figma MCP audit
"Any dark patterns here?" → ethics audit
```

**Korean:**
```
"디자인 검토해줘" → 전체 감사
"접근성 확인해줘" → 접근성 중심 감사
"내 디자인 괜찮아 보여?" → 전체 감사
"UI 검토해줘" → 전체 감사
"색상 대비 확인해줘" → 색상 및 접근성 감사
```

Paste a Figma URL, share a screenshot, or paste your HTML/CSS — Claude will run the audit automatically and respond in the same language you used.

---

## Example Output

```
## 🔍 Design Audit Report

**Input:** Checkout flow · 3 frames
**Type:** Figma MCP
**Confidence:** 🟢 High
**Component health:** 71% coverage · 2 detached instances · 8 unnamed layers

### Overall Score: 68/100
100 − (3 × 🔴 8) − (4 × 🟡 4) − (2 × 🟢 1) = 68/100
Contrast failures and missing form labels are the main drag.

### Accessibility Score: 62/100 — significant gaps
### Ethics Score: 85/100 — minor concerns

### 🔴 Critical Issues
- **Body text contrast** — #aaa on white = 2.3:1 → Fix: use #595959 (7:1)
  Why: many users literally can't read low-contrast text.
- **Missing form labels** — placeholder-only inputs lose label on type
  → Fix: add <label> above each input.

### 🟡 Warnings
- **Off-grid spacing** — padding: 13px, gap: 14px
  → Fix: use multiples of 8 (8, 16, 24px)
- **CTA hierarchy inversion** — "Accept all" is primary, "Reject all" is grey text
  → Fix: give both options equivalent visual weight (GDPR requirement)

### ✅ What's Working Well
- Clean page structure with logical section flow
- Hero CTA button has strong contrast and good sizing
```

---

## Skill Structure

```
design-auditor/
├── SKILL.md                        — Main skill instructions (18 categories)
└── references/
    ├── typography.md               — Font rules, sizing, hierarchy, type scale algorithm
    ├── color.md                    — WCAG luminance formula, contrast, palette guidance
    ├── spacing.md                  — 8-point grid, layout, proximity, code checks
    ├── corner-radius.md            — Nested radius rule, scale, pill shapes, code checks
    ├── elevation.md                — Shadow scale, elevation hierarchy, code shadow audit
    ├── iconography.md              — Icon families, sizing, touch targets
    ├── navigation.md               — Tabs, breadcrumbs, back buttons, mobile nav, code checks
    ├── tokens.md                   — Design tokens, semantic naming, dark mode architecture
    ├── figma-mcp.md                — Figma MCP workflow, page structure, safe editing
    ├── states.md                   — Loading, empty, error, success states + code checks
    ├── microcopy.md                — Button labels, errors, tone, per-role audit guide
    ├── animation.md                — Easing curves, duration scales, reduced motion, code checks
    ├── i18n.md                     — RTL support, locale formatting, i18n
    └── ethics.md                   — Dark patterns, ethical design, persuasion vs manipulation
```

---

## Changelog

### v1.2.4

**New: Category 18 — Ethical Design & Dark Patterns**

Added an ethics audit layer covering 20 manipulative design patterns across 5 groups:

- **Group A — Deceptive Interface**: Confirmshaming, CTA hierarchy inversion, trick questions, disguised ads, bait and switch, hidden costs, visual misdirection
- **Group B — Coercive Flows**: Roach motel, obstruction, forced action, nagging
- **Group C — Consent & Privacy**: Privacy zuckering, pre-checked consent, interface interference, drip pricing
- **Group D — False Urgency & Scarcity**: Countdown timers, false scarcity, false social proof
- **Group E — Emotional Manipulation**: Guilt-based copy, fear appeals, toying with emotion

Every pattern includes: definition, real-world example, why it's harmful, Figma detection signals, code detection signals, severity, fix, and a context note to prevent false positives on legitimate persuasion.

**Ethics severity model** (separate from design severity):
- 🔴 Deceptive — −15pts (actively misleads or coerces)
- 🟡 Questionable — −7pts (exploitative depending on context)
- 🟢 Noted — 0pts (persuasive but ethical, informational only)

**Ethics Score** shown alongside Accessibility Score in every report. Bands: 90–100 Ethically sound → < 50 Do not ship.

**New reference file: `ethics.md`** — full pattern taxonomy, detection scope table, ethics severity model, ethics score formula, and an Ethical Persuasion reference of 12 legitimate techniques the skill should never flag.

------------------------------------------------------------------------------------------------------------------------------------------

### v1.2.3

**Code parity complete — all 17 categories now check from source code**

**Category 3 — Spacing & Layout**
- Off-grid value detection: flags any px value not divisible by 4, with Tailwind arbitrary value support (`p-[13px]` → 🟡). Same value across 5+ places groups into one issue with count.
- Padding consistency: mismatched sides on cards/panels, mixed shorthand across same component type
- z-index audit: values outside expected ranges, `z-index: 9999` on non-overlay elements, duplicate z-index in overlapping contexts, z-index on `position: static`
- Content margin: missing `max-width`, content touching screen edge, non-responsive margins
- Logical property suggestions: flags `margin-left/right` as 🟢 Tip — upgrades to 🟡 if RTL audit active

**Category 16 — Navigation Patterns**
- Semantic nav: `<nav>` vs `<div>`, multiple `<nav>` without `aria-label`
- Active state: `aria-current="page"` detection, CSS-class-only active state → 🟡, color-only active signal → 🟡
- Skip navigation link: first focusable element check — missing → 🟡, broken target ID → 🔴 Critical
- Tab vs nav misuse: `<nav>` for view switching, `role="tablist"` for page routing
- Keyboard handling: `<div onClick>` without `role="button"` → 🔴, dropdown Escape/arrow key handler check
- Breadcrumb structure: `<nav aria-label="Breadcrumb">`, `<ol>` requirement, `aria-current="page"` on last item

**Reference files updated:** `spacing.md`, `navigation.md`, `animation.md`, `corner-radius.md` — all with full code-specific audit sections

------------------------------------------------------------------------------------------------------------------------------------------

### v1.2.2

**Figma workflow:** `get_design_pages` mandatory F1.5 step, multi-page selection widget, "Audit all pages" mode

**Type Scale Stack widget:** triggers on every audit, extracts font sizes from `get_design_context`, deterministic role-mapping algorithm

**Component health metric:** layer tally in F2, coverage % + detached instances + unnamed layers in report header

**Category 5:** 2-frame cross-check for button radius, primary color, font size, input height, icon style

**Category 12:** reads every text node, classifies by role, cites exact text + node ID per issue

**Issue deduplication:** same root cause → one grouped entry with count and node IDs

**Code input:** framework detection (HTML/CSS, React, Vue, Tailwind, CSS-in-JS), before/after code diffs, scope selector for large files

**Code superpowers added:** Cat 6, 7, 8, 9, 10, 13, 17 — direct checks from source code

**Reference files updated:** `color.md`, `states.md`, `typography.md`, `elevation.md`, `microcopy.md`, `figma-mcp.md`

**Standardised report template** with fixed sections, always/conditional labels

**QoL:** merged duplicate Step 0, Tone Guidelines moved to top, `tokens.md` added to reference list, Re-audit and Explain specs, Developer Handoff Report template

------------------------------------------------------------------------------------------------------------------------------------------

### v1.2.1
- Scoring formula always shown explicitly in every report
- Color contrast via design tokens — `get_variable_defs` drives Cat 2 programmatically
- `references/animation.md` added — full Cat 8 reference file
- Figma fix loop partial failure recovery — typed classification, never stops on single failure
- Auto-layout operations and component instance caveat added to `figma-mcp.md`

------------------------------------------------------------------------------------------------------------------------------------------

### v1.2.0
- 5 interactive audit widgets: Type Scale Stack, Contrast Checker, 8pt Grid Visualizer, States Coverage Map, Issue Priority Matrix
- Smart defaults — scope, stage, WCAG level inferred from request
- Component-type detection — auto-detects and skips irrelevant categories
- Confidence scoring acts on audit — 🟢/🟡/🔴 behaviours
- Session progress tracker with sparkline
- Full Korean coverage for all new features

------------------------------------------------------------------------------------------------------------------------------------------

### v1.1.5
- Figma Variables integration, audit goal context, WCAG level selector
- Separate Accessibility Score, Developer handoff report mode
- "Fix all Critical" loop with per-issue confirmation
- Bilingual widget labels

------------------------------------------------------------------------------------------------------------------------------------------

### v1.1.4
- Audit scope selector: Full / Quick / Custom
- Partial audit mode, severity filter, Markdown export

------------------------------------------------------------------------------------------------------------------------------------------

### v1.1.3
- Figma MCP fallback, per-category scores, before/after diffs, re-audit delta mode

------------------------------------------------------------------------------------------------------------------------------------------

### v1.1.2
- Deterministic scoring formula, audit confidence level, strict output template

------------------------------------------------------------------------------------------------------------------------------------------

### v1.1.1
- Korean language support

------------------------------------------------------------------------------------------------------------------------------------------

### v1.1.0
- 17 categories total: added Corner Radius, Elevation & Shadows, Iconography, Navigation Patterns, Design Tokens

------------------------------------------------------------------------------------------------------------------------------------------

### v1.0.0
- Initial release: 13 audit categories, 7 reference files

------------------------------------------------------------------------------------------------------------------------------------------

## Contributing

Found a design rule that should be in here? Open an issue or PR. The goal is to make this the most comprehensive plain-language design reference available inside Claude.

Areas that could use expansion:
- Data visualization & charts
- Native mobile (iOS/Android) specific patterns
- UX flow analysis & information architecture
- Print / PDF layout rules

---

## License

MIT — use it, fork it, build on it.

---

*Built with [Claude](https://claude.ai) · Skill format by [Anthropic](https://anthropic.com)*
