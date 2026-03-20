# Corner Radius Rules Reference

Corner radius is one of the most overlooked consistency systems in UI design. Most developers pick a radius value that "looks right" and apply it inconsistently — resulting in designs that feel subtly off without the viewer knowing why.

---

## The Nested Radius Rule (Most Important)

**Outer radius = Inner radius + Padding**

When one rounded element sits inside another rounded element, the outer element's radius must be *larger* than the inner element's radius — specifically, larger by the amount of padding between them.

### Why This Matters
If the inner element has the same or larger radius than the outer, its corners appear to "poke out" or clip through the outer container. The eye notices this as wrong even if the viewer can't articulate it.

### The Formula
```
outer_radius = inner_radius + padding
```

### Examples

**Card with a button inside:**
```
Card padding: 16px
Button border-radius: 8px
→ Card border-radius should be: 8 + 16 = 24px
```

**Input inside a form panel:**
```
Panel padding: 24px
Input border-radius: 6px
→ Panel border-radius should be: 6 + 24 = 30px
```

**Badge inside a chip:**
```
Chip padding: 8px
Inner dot border-radius: 4px (full circle)
→ Chip border-radius should be: 4 + 8 = 12px
```

### Visual Diagnosis
If the inner element's corners look like they're touching or crossing the outer element's border, the outer radius is too small. Increase it.

If the inner corners look like they're floating far from the outer corners with too much gap, the outer radius may be too large (though this is rarely a problem).

---

## Radius Scale

Like spacing, corner radii should come from a **predefined scale** — not arbitrary values.

### Recommended Scale

| Token | Value | Use Case |
|---|---|---|
| `radius-xs` | 2px | Subtle rounding — tooltips, small tags |
| `radius-sm` | 4px | Tight components — chips, badges, code blocks |
| `radius-md` | 8px | Default — buttons, inputs, small cards |
| `radius-lg` | 12px | Medium cards, dropdowns, popovers |
| `radius-xl` | 16px | Large cards, panels |
| `radius-2xl` | 24px | Modals, sheets, large containers |
| `radius-full` | 9999px | Pills — toggles, tags, avatar badges |

### Rules
- **Stick to the scale.** Arbitrary values like 7px, 11px, 13px, 22px signal inconsistency.
- **Use CSS variables or design tokens.** Define once, reference everywhere.
- **The scale should feel geometric.** A ×2 progression works well (4 → 8 → 16 → 32).

---

## Size-Proportional Radius

Corner radius should feel proportional to the element it's applied to. The same absolute radius value looks very different on small vs large elements.

### The Rule of Thumb
**radius ÷ height ≈ consistent visual weight across sizes**

| Element Height | Feels Right | Too Small | Too Large |
|---|---|---|---|
| 24px (badge) | 4–6px | 1–2px | 12px+ (becomes pill) |
| 40px (button) | 6–10px | 2–3px | 20px+ (becomes pill) |
| 48px (input) | 6–12px | 2–3px | 24px+ |
| 120px (card) | 12–20px | 2–4px | 48px+ |
| 400px (modal) | 16–24px | 4–6px | 48px+ |

### Signs of Size-Proportion Mismatch
- A large modal or page card with 4px radius — barely looks rounded, feels flat
- A small badge with 16px radius — looks unexpectedly blobby
- All elements having the same 8px radius regardless of size — creates inconsistency in how "rounded" things feel at different scales

---

## Pill Shapes

A **pill** shape occurs when `border-radius ≥ 50%` of the element's height. The corners fully curve, creating a stadium/capsule shape.

### When to Use Pills
- Tags and labels: `<span class="tag">Design</span>`
- Toggle switches
- Status badges ("Active", "Pending")
- Avatar group indicators
- Search bars (optional — matters for brand tone)
- Progress bars

### When NOT to Use Pills
- Cards (looks toy-like)
- Modals (doesn't feel stable)
- Large containers (too bubbly)
- Tables (inconsistent with data density)

### The Pill Trap
Applying `border-radius: 50%` to a non-square element creates a pill. Applying it to a *square* element creates a **circle**. Make sure you know which one you're creating.

```css
/* Square element → circle */
width: 40px; height: 40px; border-radius: 50%;

/* Wide element → pill */
width: 120px; height: 40px; border-radius: 9999px; /* or 50% */
```

---

## Zero Radius (Sharp Corners)

`border-radius: 0` is a valid design choice — not a default or forgotten value.

### When Sharp Corners Work
- Flat/brutalist design languages
- High-density data UIs (tables, spreadsheets, terminals)
- Industrial or technical tools
- When the brand deliberately avoids softness (fintech, security, enterprise)

### When Sharp Corners Hurt
- Consumer apps where warmth matters
- Forms (sharp-cornered inputs feel cold and uninviting)
- Cards in social or lifestyle contexts

### The Rule
If you're using 0px radius, it should be applied **consistently** and feel intentional. A mix of 0px and rounded elements with no system looks like an accident.

---

## Contextual Radius

Some contexts have specific radius conventions that users have come to expect:

### Bottom Sheets & Mobile Drawers
```
Top corners: rounded (16–24px)
Bottom corners: 0px (flush with screen edge)
```
This signals the sheet is "attached" to the bottom of the screen.

### Floating Modals & Dialogs
```
All four corners: rounded (16–24px)
```
Floating elements should feel detached from the screen — full rounding reinforces this.

### Dropdown Menus
```
All corners rounded (8px), OR
Top corners match the trigger button radius
Bottom corners rounded
```
When a dropdown appears directly below a button, the top corners of the dropdown can be square (0px) to feel connected to the button.

### Toast / Snackbar Notifications
```
All corners: rounded (8–12px)
```
Toasts float above content and should feel self-contained and friendly.

### Table Cells
```
Typically 0px (sharp) inside the table
First/last row corners may be rounded to match the table container
```

### Buttons Inside Inputs (Search, Inline Actions)
```
Button radius should match or be slightly less than the input radius
They should feel like one connected unit
```
Example: Input with 8px radius + internal search icon button with 6px radius on the right side only.

---

## Common Mistakes

| Mistake | Why It's Wrong | Fix |
|---|---|---|
| Same radius on all elements regardless of size | Large elements look barely rounded, small elements look blobby | Use size-proportional radius |
| Inner element radius > outer element radius | Inner corners appear to poke through the outer border | Apply: outer = inner + padding |
| Arbitrary values (7px, 11px, 13px) | Looks inconsistent and unintentional | Use a defined radius scale |
| Mixing rounded and square corners with no system | Feels inconsistent, designed by accident | Pick a language: all rounded, all square, or a clear rule for when each is used |
| Pills used everywhere | Looks playful/childish in serious contexts | Reserve pills for small, label-like elements |
| Square corners on consumer-facing forms | Feels cold and uninviting | Use at least 4–8px on inputs in consumer products |
| Forgetting to match radius in dark mode | Sometimes developers only update background colors in dark mode and forget border-radius context | Dark mode should use same radius system |

---

## CSS Reference

```css
/* Define your radius scale as variables */
:root {
  --radius-xs:   2px;
  --radius-sm:   4px;
  --radius-md:   8px;
  --radius-lg:   12px;
  --radius-xl:   16px;
  --radius-2xl:  24px;
  --radius-full: 9999px;
}

/* Nested radius example */
.card {
  padding: 16px;
  border-radius: var(--radius-2xl); /* 24px = inner (8px) + padding (16px) */
}

.card .button {
  border-radius: var(--radius-md); /* 8px inner element */
}

/* Contextual radius — bottom sheet */
.bottom-sheet {
  border-radius: var(--radius-2xl) var(--radius-2xl) 0 0; /* top-left top-right bottom-right bottom-left */
}

/* Pill */
.tag {
  border-radius: var(--radius-full);
}
```

---

## Code-Specific Corner Radius Checks

When auditing CSS/React/Vue code, check these directly.

### Arbitrary value detection
```
Collect all border-radius values in the codebase.

Not on the radius scale (2, 4, 8, 12, 16, 24, 9999px) → 🟡 Warning
  Common arbitrary values to flag: 3px, 5px, 6px, 7px, 10px, 11px, 13px, 14px, 20px

Tailwind:
  rounded-sm (2px), rounded (4px), rounded-md (6px), rounded-lg (8px),
  rounded-xl (12px), rounded-2xl (16px), rounded-3xl (24px), rounded-full (9999px) → ✅
  rounded-[7px], rounded-[11px] → 🟡 (arbitrary Tailwind values)

Deduplication: same arbitrary value in 5+ places → one grouped issue:
  🟡 "border-radius: 7px — 6 occurrences. Use 8px (--radius-md) instead."
```

### Tokenized vs hardcoded
```
  → border-radius: var(--radius-md) → ✅ tokenized
  → border-radius: 8px (hardcoded, no variable) → 🟡 if token system exists

If CSS custom properties are defined (:root { --radius-md: 8px }):
  → Components using hardcoded values instead of the token → 🟡 each occurrence
  → Compute radius coverage %: var(--radius-*) / total border-radius × 100
  → < 50% coverage → 🟡 Warning
```

### Nested radius rule (code)
```
The formula: outer_radius = inner_radius + padding

Detectable pattern in CSS:
  .card {
    padding: 16px;
    border-radius: 24px; /* = inner(8px) + padding(16px) ✅ */
  }
  .card .button {
    border-radius: 8px;
  }

Flag if:
  → A child element has border-radius and its parent has border-radius
    AND parent.radius < child.radius + parent.padding → 🟡 Warning
    (inner corners will appear to poke through the outer border)

  → Parent and child have the same border-radius value → 🟡
    (outer needs to be larger by the padding amount)

Tailwind nested check:
  → Parent: rounded-2xl (16px) with p-4 (16px)
  → Child: rounded-lg (8px)
  → Required: parent should be rounded-2xl (24px) = 8 + 16 → child is fine
  → But if parent is rounded-lg (8px) and child is rounded-md (6px) with p-2 (8px):
    parent (8px) < child (6px) + padding (8px) = 14px → 🟡 outer too small
```

### Pill shapes — intentional check
```
border-radius: 50% on non-square elements → creates pill ✅ (if intentional)
border-radius: 9999px or rounded-full → pill → ✅ (if applied to tag/badge/toggle)

Flag as 🟢 Tip if:
  → rounded-full applied to a large container (modal, card, panel) → looks toy-like
  → rounded-full on text-heavy elements → readability concern

border-radius: 50% on width = height element → circle ✅ (avatar, icon container)
border-radius: 50% on unequal width/height → unintended pill → 🟡
```

### Contextual radius patterns
```
Bottom sheet / drawer (anchored to screen bottom):
  Correct: border-radius: 24px 24px 0 0 (top-left top-right bottom-right bottom-left)
  Or Tailwind: rounded-t-3xl
  → All four corners rounded on a bottom-anchored sheet → 🟡 (should be square at bottom)

Dropdown connected to trigger button:
  → Dropdown top corners rounded to match button bottom corners → ✅
  → Dropdown all corners rounded when visually connected to button → 🟡

Modal / dialog (floating):
  → All four corners rounded → ✅
  → Two corners rounded, two square on a floating modal → 🟡
```
