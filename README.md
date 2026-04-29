# 🎨 Design Auditor — Claude Skill

A Claude skill that audits designs against **18 professional design categories** — built for developers, non-designers, and anyone who wants expert design validation without needing to know the rules themselves.

Works with **Figma files** (via Figma MCP), **code** (HTML/CSS/React/Vue), **screenshots**, **wireframes**, and written descriptions. Supports **English and Korean**.

Compatible with **Claude**, **Manus**, and other agents that support SKILL.md-based skills.

---

## What It Does

Drop in a Figma link, paste your CSS, upload a screenshot, or share a wireframe — Design Auditor checks your work against 18 categories of design rules and gives you:

- A **score out of 100** with per-category breakdown
- A separate **Accessibility Score** (WCAG coverage across Cat 2, 6, 7, 16)
- A separate **Ethics Score** (dark patterns and manipulative design across Cat 18)
- A **🚫 Blocker tier** for legal/compliance violations (WCAG AA, GDPR, PECR) — separate from Critical issues
- Issues ranked by severity (🚫 Blocker / 🔴 Critical / 🟡 Warning / 🟢 Tip)
- **Plain-language explanations** of *why* each rule matters
- An **Issue Priority Matrix** — every issue plotted by effort vs. impact
- Before/after code diffs when fixing issues in HTML/CSS/React/Vue
- Direct fixes in your **Figma file** via Figma MCP
- **Figma Code Connect** — detects design-to-code mapping gaps (`get_code_connect_suggestions`)
- **Wireframe to Spec** — converts wireframes into annotated dev-ready specs
- **Visual audit report** exportable to Canva for stakeholder sharing
- **Developer handoff report** — CSS spec table, a11y checklist, critical fixes
- **Export report as Markdown** — ready for Notion, GitHub, Linear, or Jira

---

## The 19 Audit Categories

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
| 19 | **Nielsen's 10 Usability Heuristics Rules** | Nielsen's 10 Usability Heuristics are the most widely used framework for evaluating interface usability. They were developed by Jakob Nielsen and are grounded in decades of usability research |

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
"Wireframe to spec" → annotated dev-ready spec from wireframe
```

**Korean:**
```
"디자인 검토해줘" → 전체 감사
"접근성 확인해줘" → 접근성 중심 감사
"내 디자인 괜찮아 보여?" → 전체 감사
"UI 검토해줘" → 전체 감사
"색상 대비 확인해줘" → 색상 및 접근성 감사
"와이어프레임 스펙 출력해줘" → 와이어프레임 스펙 모드
```

---

## Example Output

```
## 🔍 Design Audit Report

**Input:** Checkout flow · 3 frames
**Type:** Figma MCP
**Confidence:** 🟢 High
**Component health:** 71% coverage · 2 detached instances · 8 unnamed layers
**Code Connect:** 8 components mapped · 3 unmapped

### Overall Score: 62/100
100 − (1 × 🚫 12) − (2 × 🔴 8) − (4 × 🟡 4) − (2 × 🟢 1) = 62/100
A legal compliance failure and contrast issues are the main drag.

### Accessibility Score: 55/100 — significant gaps ⚠️ Contains legal compliance failures
### Ethics Score: 85/100 — minor concerns

### 🚫 Blockers (−12pts each)
- **Text contrast failure** — #aaa on white = 2.3:1 → Fix: use #595959 (7:1)
  Legal basis: WCAG 2.1 SC 1.4.3

### 🔴 Critical Issues (−8pts each)
- **Missing form labels** — placeholder-only inputs lose label on type
  → Fix: add <label> above each input.

### 🟡 Warnings (−4pts each)
- **Off-grid spacing** — padding: 13px → Fix: use 12px or 16px
- **CTA hierarchy inversion** — "Accept all" primary, "Reject all" grey text
  → Fix: equivalent visual weight (GDPR requirement)
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
    ├── figma-mcp.md                — Figma MCP workflow, Code Connect, page structure, safe editing
    ├── heuristics.md               - Nielsen's 10 Usability Heuristics: H1/H2/H3/H6/H7/H10 gap coverage
    ├── states.md                   — Loading, empty, error, success states + code checks
    ├── microcopy.md                — Button labels, errors, tone, per-role audit guide
    ├── animation.md                — Easing curves, duration scales, reduced motion, code checks
    ├── i18n.md                     — RTL support, locale formatting, i18n
    └── ethics.md                   — Dark patterns, ethical design, persuasion vs manipulation
```

---

## Changelog

### v1.2.8

Coming Soon.

------------------------------------------------------------------------------------------------------------------------------------------

### v1.2.7

**URL inputs — full support added**

The skill now recognises and audits from any URL type, not just Figma links.

| URL Type | Confidence | How it's handled |
|---|---|---|
| Live website (`https://...`) | 🟡 Medium | `web_fetch`, login handling, multi-page offer |
| Vercel / Netlify preview | 🟡 Medium | Same as live website |
| GitHub file URL | 🟢 High | Converts to raw URL, full code audit |
| GitHub repo URL | 🟡 Medium | Browses key files, scope selector widget |
| CodeSandbox / StackBlitz | 🟡–🟢 | Source if accessible, preview fallback |
| CodePen | 🟡 Medium | Debug URL fetch, source extraction |
| Storybook | 🟡 Medium | Component/variant detection from URL path |

Each URL type has a dedicated spec: how to fetch, what confidence to set, what limitations to declare in the report header, how to handle login walls, and what to offer the user after the audit.

GitHub file URLs get full 🟢 High confidence — raw source is treated identically to pasted code. All other URL types get 🟡 Medium with explicit limitation notes in the report header.

**Agent compliance — mandatory output enforcement**

Following reports of third-party agents (e.g. TRAE Solo) producing free-form UX reviews instead of scored reports, two mandatory enforcement notices were added:

- `⚠️ OUTPUT FORMAT IS MANDATORY — DO NOT DEVIATE` at the top of Step 2
- `⚠️ MANDATORY — ALL AGENTS — NO EXCEPTIONS` at the top of the Strict Output Template

Both explicitly prohibit substituting a generic critique for the scored report. Skipping the score is never acceptable — agents must estimate and flag with 🟡 Medium confidence if uncertain.

**Input widget updated** — "Live URL" and "GitHub URL" added as input type options alongside Figma, Screenshot, and Code.

**All version stamps updated** — report footers now correctly show v1.2.7.

------------------------------------------------------------------------------------------------------------------------------------------

### v1.2.6

**Wireframe to Spec mode**

New output mode alongside the standard audit. When a wireframe is detected (greyscale, box placeholders, skeleton fidelity), the skill offers Spec mode before running a scored audit.

Produces a structured design specification: Layout & Dimensions, Spacing, Typography, Components Required (with states), Copy Placeholders, Interaction Notes, Accessibility Requirements, and an Open Questions section that surfaces gaps the wireframe doesn't answer.

- No score or severity labels — Spec mode annotates, it doesn't audit
- Estimated values clearly flagged with `~` prefix
- Uses Figma layer data when available; falls back to standard defaults from reference files
- Output as downloadable `.md` file
- Auto-detected from wireframe input; also available in "What next?" widget post-audit
- Triggers on: "wireframe to spec", "annotate my wireframe", "turn this wireframe into a spec", "spec out this design"

**Canva visual audit report**

After any audit, "Export to Canva" generates a visual report card in Canva — score rings, issue summary, top 3 fixes, positives. Stakeholder-friendly format for sharing with non-technical audiences. Separate from the full technical Markdown export.

**Trigger vocabulary expanded**

Wireframe-specific trigger phrases added to YAML description. Stale version stamps in report templates fixed (were showing v1.2.2).

------------------------------------------------------------------------------------------------------------------------------------------

### v1.2.5

**Figma MCP — three new tools integrated**

- **`get_code_connect_suggestions`** — AI-suggested Figma→code component mappings. Enriches Cat 5 and Cat 17. Flags unmapped components. Adds Code Connect line to report header.
- **`get_code_connect_map`** — confirmed mappings, powers the handoff table
- **`create_design_system_rules`** — generates enforcement rules for the repo, offered post-audit when token/component health is poor. Always requires explicit confirmation.

**Scoring calibration — Blocker tier**

| Severity | Deduction | Basis |
|---|---|---|
| 🚫 Blocker | −12pts | Legal/compliance — WCAG AA, GDPR, PECR |
| 🔴 Critical | −8pts | Usability failure |
| 🟡 Warning | −4pts | Degrades experience |
| 🟢 Tip | −1pt | Polish |

Blocker examples: contrast below 4.5:1 (SC 1.4.3), keyboard-inaccessible element (SC 2.1.1), missing alt (SC 1.1.1), pre-checked marketing consent (GDPR), skip link missing (SC 2.4.1).

Accessibility Score updated: Blockers use −12. Any Blocker appends "⚠️ Contains legal compliance failures".

**Trigger vocabulary expanded** — 15+ new phrases: "is this GDPR compliant", "check my onboarding", "review my checkout", "is this manipulative", "any dark patterns here", "is my form accessible", and more.

------------------------------------------------------------------------------------------------------------------------------------------

### v1.2.4

**New: Category 18 — Ethical Design & Dark Patterns**

20 manipulative patterns across 5 groups: Deceptive Interface, Coercive Flows, Consent & Privacy, False Urgency & Scarcity, Emotional Manipulation.

Ethics severity model: 🔴 Deceptive (−15pts) · 🟡 Questionable (−7pts) · 🟢 Noted (0pts). Ethics Score shown alongside Accessibility Score.

New `ethics.md` reference file — 877 lines with full pattern taxonomy, detection signals, and Ethical Persuasion reference.

------------------------------------------------------------------------------------------------------------------------------------------

### v1.2.3

Code parity complete — all 17 categories now check from source code.

- **Cat 3** — off-grid detection, z-index audit, padding consistency, content margin, logical properties
- **Cat 16** — `<nav>` semantics, `aria-current`, skip link, tab vs nav misuse, keyboard handling, breadcrumb structure
- **`spacing.md`, `navigation.md`, `animation.md`, `corner-radius.md`** — all with full code-specific audit sections

------------------------------------------------------------------------------------------------------------------------------------------

### v1.2.2

- `get_design_pages` as mandatory F1.5 step — file structure before auditing
- Type Scale Stack triggers on every audit — extracts font sizes directly from `get_design_context`
- Component health metric in report header — coverage %, detached instances, unnamed layers
- 2-frame cross-check for consistency (Cat 5)
- Microcopy reads every text node, classifies by role, cites exact text + node ID
- Issue deduplication — same root cause → one grouped entry
- Framework detection + before/after code diffs for code input
- Code superpowers added to Cat 6, 7, 8, 9, 10, 13, 17
- Standardised report template with fixed sections
- Re-audit spec, Explain an issue depth, Developer Handoff Report template

------------------------------------------------------------------------------------------------------------------------------------------

### v1.2.1
- Scoring formula always shown in every report
- Color contrast via design tokens — `get_variable_defs` drives Cat 2
- `animation.md` added — full Cat 8 reference
- Figma fix loop partial failure recovery
- Auto-layout ops and component instance caveat in `figma-mcp.md`

------------------------------------------------------------------------------------------------------------------------------------------

### v1.2.0
- 5 interactive audit widgets: Type Scale Stack, Contrast Checker, 8pt Grid Visualizer, States Coverage Map, Issue Priority Matrix
- Smart defaults — scope, stage, WCAG level inferred from request
- Component-type detection and confidence scoring
- Session progress tracker with sparkline
- Full Korean coverage

------------------------------------------------------------------------------------------------------------------------------------------

### v1.1.5
- Figma Variables integration, audit goal context, WCAG level selector
- Separate Accessibility Score, Developer handoff mode
- Fix all Critical loop, bilingual widget labels

------------------------------------------------------------------------------------------------------------------------------------------

### v1.1.4
- Audit scope selector: Full / Quick / Custom
- Partial audit mode, severity filter, Markdown export

------------------------------------------------------------------------------------------------------------------------------------------

### v1.1.3
- Figma MCP fallback, per-category scores, before/after diffs, re-audit delta

------------------------------------------------------------------------------------------------------------------------------------------

### v1.1.2
- Deterministic scoring formula, confidence level, strict output template

------------------------------------------------------------------------------------------------------------------------------------------

### v1.1.1
- Korean language support

------------------------------------------------------------------------------------------------------------------------------------------

### v1.1.0
- 17 categories: added Corner Radius, Elevation, Iconography, Navigation, Design Tokens

------------------------------------------------------------------------------------------------------------------------------------------

### v1.0.0
- Initial release: 13 audit categories, 7 reference files

------------------------------------------------------------------------------------------------------------------------------------------

## Contributing

Found a design rule that should be in here? Open an issue or PR.

Areas that could use expansion:
- UX flow analysis & information architecture
- Data visualization & charts
- Native mobile (iOS/Android) specific patterns
- Print / PDF layout rules

---

## License

MIT — use it, fork it, build on it.

---

*Built with [Claude](https://claude.ai) · Skill format by [Anthropic](https://anthropic.com)*
