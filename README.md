# 🎨 Design Auditor — Claude Skill

A Claude skill that audits designs against **17 professional design categories** — built for developers, non-designers, and anyone who wants expert design validation without needing to know the rules themselves.

Works with **Figma files** (via Figma MCP), **code** (HTML/CSS/React/Vue), **screenshots**, and written descriptions. Supports **English and Korean**.

---

## What It Does

Drop in a Figma link, paste your CSS, or upload a screenshot — Design Auditor checks your work against 17 categories of design rules and gives you:

- A **score out of 100**
- Issues ranked by severity (🔴 Critical / 🟡 Warning / 🟢 Tip)
- **Plain-language explanations** of *why* each rule matters
- A **top 3 priority fix list**
- An offer to fix issues directly in your code or Figma file

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
"Check my design" → full audit
"Is this accessible?" → accessibility-focused audit
"Review my form" → forms & microcopy audit
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
    └── i18n.md                     — RTL support, locale formatting, i18n
```

---

## Changelog

### v1.1.2
- Deterministic scoring formula: 🔴 −8pts, 🟡 −4pts, 🟢 −1pt with visible score breakdown
- Audit confidence level declared per report (🟢 High / 🟡 Medium / 🔴 Low)
- Strict output template for consistent report structure every time
- Proactive fix offer after each critical and warning issue
- typography.md: fluid typography with clamp() examples
- color.md: 60-30-10 color proportion rule with dark mode variant
- spacing.md: when and how to intentionally break the 8-point grid
- tokens.md: token health score formula (5-area, 100-point scale)

### v1.1.1
- Added Korean language support — skill detects Korean input and responds with full audit report in Korean
- Korean trigger phrases: 디자인 검토, UI 검토, 접근성 확인, 색상 대비 and more

### v1.1.0
- Added 4 new audit categories: Corner Radius, Elevation & Shadows, Iconography, Navigation Patterns, Design Tokens
- Added 5 new reference files
- Total: 17 categories, 14 reference files

### v1.0.0
- Initial release with 13 audit categories and 7 reference files

---

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
