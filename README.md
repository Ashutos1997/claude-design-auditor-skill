# 🎨 Design Auditor — Claude Skill

A Claude skill that audits designs against **17 professional design categories** — built for developers, non-designers, and anyone who wants expert design validation without needing to know the rules themselves.

Works with **Figma files** (via Figma MCP), **code** (HTML/CSS/React/Vue), **screenshots**, and written descriptions. Supports **English and Korean**.

Compatible with **Claude**, **Manus**, and other agents that support SKILL.md-based skills.

---

## What It Does

Drop in a Figma link, paste your CSS, or upload a screenshot — Design Auditor checks your work against 17 categories of design rules and gives you:

- A **score out of 100** with per-category breakdown
- Issues ranked by severity (🔴 Critical / 🟡 Warning / 🟢 Tip)
- **Plain-language explanations** of *why* each rule matters
- A **top 3 priority fix list**
- Before/after code diffs when fixing issues
- An offer to fix issues directly in your code or Figma file
- **Export report as Markdown** — ready for Notion, GitHub, Linear, or Jira

---

## The 17 Audit Categories

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

---

## Who It's For

- **Developers** building UIs who don't have a design background
- **Designers** who want a fast second opinion or WCAG check
- **Teams** using Figma MCP or VS Code who want design validation in their workflow
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
## Design Audit Report

### Overall Score: 68/100
Solid structure with good layout instincts, but critical contrast
failures and missing form labels need to be fixed before shipping.

### 🔴 Critical Issues
- **Body text contrast**: #aaa on white = 2.3:1 ratio → Fix: use #595959
  (7:1) → Why: many users literally can't read low-contrast text.
- **Missing form labels**: Placeholder-only inputs lose their label
  when typing → Fix: add <label> above each input.

### 🟡 Warnings
- **Off-grid spacing**: padding: 13px, gap: 14px → Fix: use multiples
  of 8 (8, 16, 24px) → Why: arbitrary values create subtle visual jitter.

### ✅ What's Working Well
- Clean page structure with logical section flow
- Hero CTA button has strong contrast and good sizing

### 🎯 Top 3 Priority Fixes
1. Fix all text contrast (body, cards, nav)
2. Add visible labels to all form inputs
3. Put all spacing on the 8-point grid
```

---

## Skill Structure

```
design-auditor/
├── SKILL.md                        — Main skill instructions (17 categories)
└── references/
    ├── typography.md               — Font rules, sizing, hierarchy
    ├── color.md                    — WCAG contrast, palette guidance
    ├── spacing.md                  — 8-point grid, layout, proximity
    ├── corner-radius.md            — Nested radius rule, scale, pill shapes
    ├── elevation.md                — Shadow scale, elevation hierarchy
    ├── iconography.md              — Icon families, sizing, touch targets
    ├── navigation.md               — Tabs, breadcrumbs, back buttons, mobile nav
    ├── tokens.md                   — Design tokens, semantic naming, dark mode
    ├── figma-mcp.md                — Figma MCP workflow & safe editing
    ├── states.md                   — Loading, empty, error, success states
    ├── microcopy.md                — Button labels, errors, tone, terminology
    ├── animation.md                — Easing curves, duration scales, reduced motion, Figma Smart Animate naming, anti-patterns
    └── i18n.md                     — RTL support, locale formatting, i18n

```

---

## Changelog

### v1.2.3

**Category 3 — Spacing & Layout**
- Off-grid value detection: flags any px value not divisible by 4, with Tailwind arbitrary value support (`p-[13px]` → 🟡)
- Deduplication: same off-grid value across 5+ places groups into one issue with count
- Padding consistency check: mismatched sides on cards/panels, mixed shorthand across same component type
- z-index audit: flags values outside expected ranges, `z-index: 9999` on non-dev-tool elements, duplicate z-index in overlapping contexts, z-index on `position: static`
- Content margin check: missing `max-width`, content touching screen edge, non-responsive margins
- Logical properties: flags `margin-left/right`, `padding-left/right` as 🟢 Tip (upgrades to 🟡 if RTL audit active)

**Category 16 — Navigation Patterns**
- Semantic nav element: `<nav>` vs `<div role="navigation">`, multiple `<nav>` without `aria-label`
- Active state: `aria-current="page"` detection, CSS-class-only active state flagged as 🟡, color-only active signal flagged as 🟡
- Skip navigation link: first focusable element check, missing skip link → 🟡, broken target ID → 🔴 Critical
- Tab vs nav misuse: `<nav>` used for view switching, `role="tablist"` used for page routing
- Keyboard navigability: `<div onClick>` without `role="button"` + `tabindex` → 🔴, dropdown Escape/arrow key handler check
- Breadcrumb structure: `<nav aria-label="Breadcrumb">`, `<ol>` vs `<ul>`, `aria-current="page"` on last item, non-linked intermediate crumbs

**Reference Files Updated**

- **`spacing.md`** — Added code-specific section: off-grid detection logic, padding consistency patterns, z-index audit table, content margin rules, logical property suggestions
- **`navigation.md`** — Added code-specific section: semantic structure, active state implementation, skip link pattern, tab vs nav misuse, keyboard/dropdown handling, full breadcrumb HTML spec
- **`animation.md`** — Added code-specific section: `prefers-reduced-motion` global and targeted patterns, duration thresholds (> 600ms → 🔴), easing extraction and classification, infinite loop check, `transition: all` warning, Framer Motion and React Spring specific checks
- **`corner-radius.md`** — Added code-specific section: arbitrary value detection with Tailwind support, tokenized vs hardcoded check with coverage %, nested radius rule in CSS, pill shape intent check, contextual radius patterns (bottom sheet, dropdown, modal)

------------------------------------------------------------------------------------------------------------------------------------------

### v1.2.2

**Figma workflow**
- Added `get_design_pages` as a mandatory F1.5 step — skill now reads full file structure before auditing any node
- Multi-page files (6+) now present a page selection widget before starting
- "Audit all pages" mode runs sequential audits and produces a ranked summary by score

**Type Scale Stack widget**
- Now triggers on every Figma and code audit — no longer waits for a typography issue to be found
- Extracts font sizes directly from `get_design_context` and maps them to roles (h1, h2, body, caption) using a deterministic algorithm
- Tailwind `text-*` class → px mapping table added; `clamp()` fluid type supported

**Component health metric**
- F2 now tallies all layers during context scan: component coverage %, detached instances, unnamed layers
- Component health line always appears in report header on Figma audits
- Low coverage (< 30%) flags as 🔴 Critical; detached instances and unnamed layers flag as 🟡 Warning

**Category 5 — Consistency**
- Added 2-frame cross-check mode: when 2+ frames are audited in a session, automatically compares button radius, primary color, body font size, input height, and icon style between frames

**Category 12 — Microcopy**
- Now reads every text node from `get_design_context` and classifies by role (CTA, label, placeholder, error, empty state, header)
- Cites exact text content and node ID in every issue
- Added placeholder prefix rule (`eg:` → 🟡), required field legend check, and duplicate-label detection

**Issue deduplication**
- Same root cause across N nodes → one grouped entry with exact count and node IDs
- Deduplication applies to scoring: repeated issue = one deduction, not N

**Scoring transparency**
- Score arithmetic now shown in every report: `100 − (3 × 8) − (5 × 4) − (1 × 1) = 55/100`

**Framework detection (Step 1.6)**
- Detects HTML/CSS, React/JSX, Vue, Tailwind, CSS-in-JS, and CSS custom properties before extracting any values
- Framework declaration shown in report header; fixes and diffs are framework-aware

**Code fix output (Step 1.7)**
- Fixes now output as actual before/after code diffs, not descriptions
- Format adapts to framework: Tailwind class swaps, CSS property changes, JSX prop changes, aria additions

**Code audit scope selector**
- Large files (150+ lines) now prompt a scope widget before auditing: Full / Accessibility / Tokens / Responsive / Typography+Color / Motion+States

**Per-category code superpowers added**
- **Cat 6 (Accessibility)**: `aria-label` on icon buttons, `alt` attributes, `focus: outline: none` detection, `role` attributes, `tabIndex` misuse, `aria-describedby` linking
- **Cat 7 (Forms)**: `input type` correctness, `autocomplete` attributes, `aria-describedby` on errors, `fieldset`+`legend` for groups, `inputMode`, `novalidate` vs custom validation, `disabled` vs `readonly`
- **Cat 8 (Motion)**: `prefers-reduced-motion` detection, duration value extraction, `animation-iteration-count: infinite` check, `transition: all` warning
- **Cat 9 (Dark Mode)**: `prefers-color-scheme` detection, CSS var swap vs hardcoded pattern, pure black check, Tailwind dark mode audit
- **Cat 10 (Responsive)**: Breakpoint coverage scan, fixed-width trap detection, overflow risks, image responsiveness, viewport meta tag, iOS font-size zoom bug, `100vh` vs `dvh`
- **Cat 13 (i18n)**: Hardcoded string detection, RTL CSS logical properties, `Intl` API usage, `dir` attribute
- **Cat 17 (Tokens)**: CSS custom property coverage % per category, JS theme object audit, Tailwind arbitrary value detection, semantic naming check

**Reference Files Updated**
- **`color.md`** — Added WCAG luminance formula (exact math), code color pair extraction with DOM traversal, opacity blending, and gradient worst-case rules
- **`states.md`** — Added full code-specific state checks: `aria-busy`, `aria-invalid`, `aria-live`, `aria-disabled`, focus ring detection, empty state conditional render patterns
- **`typography.md`** — Added type scale role-mapping algorithm, rem/em → px conversion table, Tailwind `text-*` → px mapping, `clamp()` audit rules
- **`elevation.md`** — Added code shadow audit: hardcoded vs tokenized detection, blur/offset ratio rule, pure black check, dark mode shadow detection, Tailwind shadow class reference
- **`microcopy.md`** — Added placeholder prefix rule, no-duplicate-label rule, node-ID citation format, required field legend section, per-role text audit guide
- **`figma-mcp.md`** — Expanded `get_design_pages` from one line to full F1.5 decision logic; expanded component health tally with thresholds and issue flags

**Report Structure**
- **Standardised report template** with fixed sections: REPORT HEADER (input, type, framework, confidence, scope, component health, token coverage), SCORES, ISSUES with node/line citations, POSITIVES, conditional CROSS-FRAME and RE-AUDIT DELTA blocks, REPORT FOOTER with version stamp
- Sections labelled `*(always)*` or `*(conditional)*` for clarity

**Quality of Life Improvements**
- Fixed version number in YAML frontmatter (`1.2.1` → `1.2.2`)
- Merged duplicate `## Step 0` headings into one
- Moved Tone Guidelines to top of skill (after Step 0) so they apply from the first step
- Added `references/tokens.md` to the Reference Files list (was missing)
- Updated all 13 reference file descriptions to reflect current content
- Specced out **Re-audit** behaviour: same scope, no re-asking settings, delta summary, unchanged issues collapsed
- Specced out **Explain an issue**: depth adapts to beginner vs experienced mode, cites WCAG criterion for experienced users
- Added structured **Developer Handoff Report** template: CSS/token spec table, a11y checklist, critical fixes table
- Added **Export report** spec: full audit as downloadable `.md` with dev handoff appended

------------------------------------------------------------------------------------------------------------------------------------------

### v1.2.1

Changed
- **Scoring formula now always shown explicitly** — every audit report includes the full
  arithmetic breakdown (e.g. `100 − (3×8) − (5×4) − (2×1) = 54/100`) so users can see
  exactly which issues are costing points and what fixing them is worth
- **Medium confidence modifier now visible in score** — when screenshot input applies the
  −50% deduction modifier, the adjusted formula is shown inline

Added
- **`references/animation.md`** — full reference file for Category 8 (Motion), covering
  purpose taxonomy, duration scale, easing guide with CSS cubics, Figma Smart Animate
  naming conventions, reduced motion rules, autoplay/loop policy, stagger limits, and
  a severity table. Cat 8 was the only audited category without a dedicated reference file.
- **Auto-layout edit operations in `figma-mcp.md`** — added `SET_LAYOUT_MODE`,
  `SET_PRIMARY_AXIS_ALIGN_ITEMS`, `SET_COUNTER_AXIS_ALIGN_ITEMS`,
  `SET_PRIMARY_AXIS_SIZE_TYPE` / `SET_COUNTER_AXIS_SIZE_TYPE` with examples
- **Component instance caveat in `figma-mcp.md`** — documented how to detect instance
  nodes, how to find and edit the main component instead, and what to do when the main
  component lives in a shared library
- **Partial failure recovery in fix loop (F5)** — typed failure classification (node not
  found / cannot edit instance / invalid operation / permission denied / unknown), per-failure
  user messaging in EN + KO, running failed-fixes log shown at end of loop with manual
  instructions, explicit rule: never stop the loop because one fix failed
- **Pre-flight check before each Figma edit** — verifies node ID exists, node is not inside
  a component instance, and operation type matches node type before calling
  `perform_editing_operations`
- **Color contrast via design tokens (Cat 2 + F3.5)** — `get_variable_defs` now drives Cat 2
  programmatically: extracts color token pairs, computes WCAG luminance ratios inline,
  pre-populates Contrast Checker widget with exact hex pairs and token names. Cat 2 upgrades
  to 🟢 High confidence when tokens are available — no screenshot required

------------------------------------------------------------------------------------------------------------------------------------------

### v1.2.0
- **5 interactive audit widgets** — contextual tools now render inline at the exact moment they're relevant during an audit, not at the end:
  - **Type Scale Stack** (fires on Cat 1): renders detected font sizes at actual scale, flags duplicate sizes, ratios too close to distinguish (<1.15×), and body text below minimum — interactive, add/remove sizes live
  - **Contrast Checker** (fires on Cat 2): pre-populates the failing fg/bg pair, shows all 5 WCAG levels simultaneously, calculates and suggests the nearest passing hex fix automatically
  - **8pt Grid Visualizer** (fires on Cat 3): pre-populates the offending px value on a ruler, shows valid grid neighbours and snap distances, pre-colors all common spacing values as on/off-grid, supports 4pt/8pt base toggle
  - **States Coverage Map** (fires on Cat 11): interactive 6×9 grid of components × states — click or keyboard-navigate to mark cells present/missing/N/A, coverage bar with threshold labels (≥80% good, 50–79% gaps, <50% critical)
  - **Issue Priority Matrix** (fires at end of every audit): replaces static "Top 3 fixes" list — all issues plotted on effort × impact axes across four zones (Quick wins, Big lifts, Polish later, Reconsider), severity shown as both color and letter (C/W/T) for colorblind accessibility, click any dot to request a fix
- **Smart defaults** — scope, design stage, and WCAG level are now inferred from the request instead of asking 4 sequential questions upfront; only asks when genuinely ambiguous, and then asks once with a single combined widget; inferred settings stated at top of report
- **Component-type detection** — auto-detects what's being audited (Full page, Form, Modal, Navigation, Card/list, Dashboard, Single component) and skips irrelevant categories; detected type and skipped categories declared at top of every report
- **Confidence scoring acts on the audit** — 🟢 High (Figma/code) runs full deductions with exact values; 🟡 Medium (screenshot) applies −50% modifier on value-dependent issues, skips Design Tokens, adds a warning banner; 🔴 Low (description only) refuses to assign a score and asks for visuals instead
- **Screenshot-specific fix path** — fixes for screenshot audits are now spatial design direction ("increase the gap between label and input") never code snippets, since there is no source to edit
- **Session progress tracker** — records score, a11y score, and issue counts per audit; after 2+ audits shows a sparkline of score progression (e.g. 58 → 71 → 84) and explicitly acknowledges fixed issues: "✅ Fixed since last audit: Color & Contrast (+3pts)"
- **Full Korean coverage for all new features** — every new user-facing string (all 5 widget intros, inferred settings banner, quick audit line, component detected line, medium confidence banner, re-audit delta summary, radar chart intro, Figma MCP unavailable message, "What next?" fallback) now has a Korean translation alongside English
- **states.md updated** — "6 states" corrected to "9 states"; Hover, Focus, and Active added to the component state table with design priority ratings; N/A guidance added for the States Coverage Map widget
- **color.md updated** — contrast checker tool reference now points to the inline widget as primary during audits, with the WebAIM external link retained as a fallback
- Skill packaged as `design-auditor.skill` (zip archive, 61KB) with `version: 1.2.0` in SKILL.md frontmatter

------------------------------------------------------------------------------------------------------------------------------------------

### v1.1.5
- Figma Variables integration: `get_variable_defs` now called on every Figma audit — Category 17 (Design Tokens) shows real token coverage % (e.g. "4 of 7 colors tokenized — 57%")
- Audit goal context: choose design stage (Early concept / Dev handoff / Production) — severity thresholds adjust accordingly
- WCAG level selector: choose AA (standard) or AAA (enhanced) before every audit — contrast thresholds applied correctly per level
- Separate Accessibility Score out of 100 shown alongside overall score, covering Categories 2, 6, 7, and 16
- Developer handoff report mode: terse output with node IDs and exact values, no educational context — optimised for sharing with devs
- "Fix all Critical" loop: per-issue confirmation (Yes / Skip / Stop) instead of batch-applying all fixes at once
- Bilingual widget labels: all ask_user_input options now show English / 한국어 side by side for full Korean UX consistency

------------------------------------------------------------------------------------------------------------------------------------------

### v1.1.4
- Audit scope selector: choose Full (17 categories), Quick (top 5), or Custom (pick your own) before every audit
- Partial audit mode: single components auto-detected — irrelevant categories skipped and declared upfront
- Severity filter widget: after reports with 5+ issues, filter to show only 🔴 / 🔴+🟡 / everything
- Export as Markdown: "Export report as text" now outputs a clean copy-pastable block for Notion, GitHub, Linear, or Jira

------------------------------------------------------------------------------------------------------------------------------------------

### v1.1.3
- Figma MCP fallback: if Figma MCP is unavailable, skill now asks for a screenshot or code instead of failing silently
- Per-category scores added to every report (e.g. Typography: 8/10, Color: 6/10)
- Before/After code diffs shown when fixing issues — see exactly what changed and why
- Re-audit delta mode: when auditing an updated design, shows score change and which issues were resolved since last audit
- Post-report "What next?" widget with fix, explain, re-audit, and export options
- Ambiguous input widget when no design is shared upfront
- Expanded trigger keywords: "pixel perfect", "UI critique", "Figma audit", "CSS check", "review this component" and more

------------------------------------------------------------------------------------------------------------------------------------------

### v1.1.2
- Deterministic scoring formula: 🔴 Critical = −8pts, 🟡 Warning = −4pts, 🟢 Tip = −1pt — score breakdown shown in every report
- Audit confidence level declared per report (🟢 High / 🟡 Medium / 🔴 Low)
- Strict output template for consistent, predictable report structure
- Proactive fix offer after every 🔴 and 🟡 issue
- Enhanced reference files: fluid typography with `clamp()`, 60-30-10 color rule + dark mode variant, intentional 8-point grid exceptions, token health score formula

------------------------------------------------------------------------------------------------------------------------------------------

### v1.1.1
- Added Korean language support — skill detects Korean input and responds with full audit report in Korean
- Korean trigger phrases: 디자인 검토, UI 검토, 접근성 확인, 색상 대비 and more

------------------------------------------------------------------------------------------------------------------------------------------

### v1.1.0
- Added 4 new audit categories: Corner Radius, Elevation & Shadows, Iconography, Navigation Patterns, Design Tokens
- Added 5 new reference files
- Total: 17 categories, 14 reference files

------------------------------------------------------------------------------------------------------------------------------------------

### v1.0.0
- Initial release with 13 audit categories and 7 reference files

------------------------------------------------------------------------------------------------------------------------------------------

## Contributing

Found a design rule that should be in here? Open an issue or PR. The goal is to make this the most comprehensive plain-language design reference available inside Claude.

Areas that could use expansion:
- Data visualization & charts
- Native mobile (iOS/Android) specific patterns
- Design tokens & component API conventions
- Print / PDF layout rules

---

## License

MIT — use it, fork it, build on it.

---

*Built with [Claude](https://claude.ai) · Skill format by [Anthropic](https://anthropic.com)*
