# 🎨 Design Auditor — Claude Skill

A Claude skill that audits designs against **13 professional design rules** — built for developers, non-designers, and anyone who wants expert design validation without needing to know the rules themselves.

Works with **Figma files** (via Figma MCP), **code** (HTML/CSS/React/Vue), **screenshots**, and written descriptions.

---

## What It Does

Drop in a Figma link, paste your CSS, or upload a screenshot — Design Auditor checks your work against 13 categories of design rules and gives you:

- A **score out of 100**
- Issues ranked by severity (🔴 Critical / 🟡 Warning / 🟢 Tip)
- **Plain-language explanations** of *why* each rule matters
- A **top 3 priority fix list**
- An offer to fix issues directly in your code or Figma file

---

## The 13 Audit Categories

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

```
"Check my design" → full audit
"Is this accessible?" → accessibility-focused audit  
"Review my form" → forms & microcopy audit
"Does this follow WCAG?" → contrast & a11y audit
"Check my Figma file: [link]" → Figma MCP audit
```

Paste a Figma URL, share a screenshot, or paste your HTML/CSS — Claude will run the audit automatically.

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
├── SKILL.md                        — Main skill instructions (13 categories)
└── references/
    ├── typography.md               — Font rules, sizing, hierarchy
    ├── color.md                    — WCAG contrast, palette guidance
    ├── spacing.md                  — 8-point grid, layout, proximity
    ├── figma-mcp.md                — Figma MCP workflow & safe editing
    ├── states.md                   — Loading, empty, error, success states
    ├── microcopy.md                — Button labels, errors, tone, terminology
    └── i18n.md                     — RTL support, locale formatting, i18n
```

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
