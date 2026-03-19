# Figma MCP Workflow Reference

## Overview

The Figma MCP gives Claude direct read and write access to Figma files. This reference covers how to use it safely and effectively during design audits.

---

## Reading a Design

### Tool: `resolve_shortlink`
Use when the user shares a short Figma URL (e.g. `fig.com/abc123`).
```
input: { shortlink: "https://fig.com/abc123" }
output: { nodeId: "123:456", fileKey: "AbCdEfGh" }
```
Use the returned nodeId for all subsequent calls.

### Tool: `get_design_context`
The primary tool for reading design data. Call on any frame, component, or layer.

**What it returns:**
- Layer tree (names, types, parent-child relationships)
- Typography: fontFamily, fontSize, fontWeight, lineHeight, letterSpacing
- Colors: fills (hex + opacity), strokes, background colors
- Layout: width, height, x, y, padding (top/right/bottom/left), gap (auto-layout)
- Component info: which components are used, their names, any overrides

**Best practices:**
- Start with the top-level frame, not individual layers
- If the frame is very complex (100+ layers), request specific sections
- Component names are a signal of design system health — look for names like "Button/Primary/Default" (good) vs "Rectangle 42" (bad)

### Tool: `get_screenshot`
Always call this alongside `get_design_context`. The visual rendering catches issues that data alone misses:
- Visual density and crowding
- Color contrast as it actually appears
- Alignment issues
- Overall hierarchy and balance

```
input: { nodeId: "123:456" }
output: { imageUrl: "..." }
```

### Tool: `get_design_pages`
Call this on the file key **before** auditing any node. Returns the list of all pages in the file.

**Decision logic based on page count:**

```
1 page  → Proceed directly to get_design_context. No user prompt needed.

2–5 pages → Note page names at the top of the audit report.
            If user gave a specific node URL, audit that frame and note
            which page it belongs to. Offer to audit other pages afterward.

6+ pages → Present a page selection widget before auditing:
           "This file has [N] pages — which would you like to audit?"
           Options: [list of page names] + "Audit all pages"

           If "Audit all pages" selected:
             - Run get_design_context + get_screenshot on the top-level frame
               of each page sequentially
             - Score each page independently
             - Produce a ranked summary at the end (worst score first)

No node ID given (just file URL) → Always use get_design_pages first,
                                    then present page selection widget.
```

**Report header line** (include whenever 2+ pages exist):
- English: `"File: [N] pages — auditing '[page name]' (page [N] of [N])."`
- Korean: `"파일: [N]개 페이지 — '[페이지 이름]' 감사 중 ([N]/[N])."`

### Tool: `get_metadata`
Returns file-level info: title, last modified, owner. Use to confirm you're in the right file.

---

## Auditing from Context Data

When you have the `get_design_context` output, check these specific fields:

### Typography Checks
```
Look for in context data:
- fontFamily → Are there more than 2 distinct families?
- fontSize → Any below 12? Body text below 14?
- lineHeight → Calculate ratio: lineHeight / fontSize. Should be 1.4–1.6 for body.
- fontWeight → Does weight vary meaningfully across hierarchy levels?
- letterSpacing → Negative on body text? (bad)
```

### Color Checks
```
Look for:
- fills[].color → Convert to hex, check contrast against background
- opacity → Low opacity text is a common contrast failure
- Multiple fills → Complex blending may affect contrast
```

### Spacing Checks
```
Look for:
- paddingTop, paddingRight, paddingBottom, paddingLeft → Are they consistent? Multiples of 8?
- itemSpacing (gap) → Multiple of 8?
- x, y positions → Do sibling elements align to shared values?
```

### Component Health Checks
```
Look for:
- Layer names like "Frame 12", "Group 7", "Rectangle" → unnamed 🔴
- Layer names like "Button/Primary/Hover" → named component instance ✅
- Layer names like "Header", "Card Item" (frames, not components) → named frame ⚠️
- Nodes with no componentId → detached instance or raw layer 🟡

Tally all non-hidden layers in the frame:
  component_pct  = (named component instances / total) × 100
  unnamed_pct    = (unnamed layers / total) × 100
  detached_count = number of detached instances

Thresholds:
  component_pct ≥ 60% → ✅ Healthy
  component_pct 30–59% → 🟡 Partial
  component_pct < 30%  → 🔴 Low coverage

Always show in report header:
  "Component health: [N]% coverage · [N] detached · [N] unnamed layers"

Issue flags:
  unnamed_pct > 20%     → 🟡 Warning
  detached_count > 0    → 🟡 Warning
  component_pct < 30%   → 🔴 Critical
```

---

## Making Edits in Figma

### Tool: `perform_editing_operations`
Use this to fix issues directly. **Always follow the safety rules below.**

### Safety Rules for Editing

**Rule 1: Always confirm before editing**
Never apply edits without asking:
> "I can fix [specific issue] on the [element name] layer. Want me to go ahead?"

**Rule 2: Target specific node IDs**
Get node IDs from `get_design_context` output. Never guess or construct IDs.

**Rule 3: One change at a time for critical properties**
For risky changes (colors, layout, component swaps), do one edit and check the result before continuing.

**Rule 4: Never bulk-edit without confirmation**
Don't say "I'll fix all the spacing issues" and run 20 operations. List what you'll change, confirm, then execute.

**Rule 5: Take a screenshot after editing**
After each batch of edits, call `get_screenshot` to verify the result looks correct.

---

### Common Edit Patterns

#### Fix node width (snap to grid)
```
operation: "SET_WIDTH"
nodeId: "[frame or component node ID]"
value: 60  // snapped from 60.17px to nearest 8pt multiple
```

#### Fix node height (touch target)
```
operation: "SET_HEIGHT"
nodeId: "[interactive element node ID]"
value: 44  // WCAG minimum touch target
```

#### Fix a text color for contrast
```
operation: "SET_FILL_COLOR"
nodeId: "[text layer node ID]"
color: { r: 0.2, g: 0.2, b: 0.2, a: 1.0 }  // #333333 — passes 4.5:1 on white
```

**Hex to 0–1 conversion:** divide each channel by 255. `#7C3AED` → `{r: 0.486, g: 0.227, b: 0.929, a: 1.0}`

#### Fix font size
```
operation: "SET_FONT_SIZE"
nodeId: "[text layer node ID]"
fontSize: 16
```

#### Fix padding on a frame
```
operation: "SET_PADDING"
nodeId: "[frame node ID]"
paddingTop: 16
paddingRight: 16
paddingBottom: 16
paddingLeft: 16
```

#### Fix spacing between auto-layout items
```
operation: "SET_ITEM_SPACING"
nodeId: "[auto-layout frame node ID]"
itemSpacing: 8
```

#### Fix auto-layout direction
```
operation: "SET_LAYOUT_MODE"
nodeId: "[auto-layout frame node ID]"
layoutMode: "HORIZONTAL"  // or "VERTICAL" or "NONE"
```

#### Fix auto-layout alignment
```
operation: "SET_PRIMARY_AXIS_ALIGN_ITEMS"
nodeId: "[auto-layout frame node ID]"
primaryAxisAlignItems: "CENTER"  // SPACE_BETWEEN, MIN, MAX, CENTER

operation: "SET_COUNTER_AXIS_ALIGN_ITEMS"
nodeId: "[auto-layout frame node ID]"
counterAxisAlignItems: "CENTER"  // MIN, MAX, CENTER, BASELINE
```

#### Fix auto-layout sizing (hug vs fixed vs fill)
```
operation: "SET_PRIMARY_AXIS_SIZE_TYPE"
nodeId: "[auto-layout frame node ID]"
primaryAxisSizeType: "AUTO"  // "AUTO" = hug contents, "FIXED" = fixed size

operation: "SET_COUNTER_AXIS_SIZE_TYPE"
nodeId: "[auto-layout frame node ID]"
counterAxisSizeType: "AUTO"
```

#### Rename a layer (for component hygiene)
```
operation: "RENAME_LAYER"
nodeId: "[layer node ID]"
name: "Button/Primary/Default"
```

---

### ⚠️ Component Instance Caveat

**You cannot directly edit layers inside a component instance.** If a node ID belongs to a layer inside an instance (not the main component), `perform_editing_operations` will fail or have no effect.

**How to detect this:** In `get_design_context`, instances show as `type: "INSTANCE"`. Any child layer of an instance is read-only from outside.

**What to do instead:**
1. Check if the parent frame is an instance — look for `componentId` or `mainComponent` in the context data
2. If it is an instance: inform the user that the fix must be applied to the **main component**, not the instance
3. Find the main component node ID (from `componentId` in the context) and apply the edit there — it will propagate to all instances
4. If the main component is in a different file (shared library), note that it cannot be edited via MCP and give design direction instead

**Example message to user:**
> "The node `68:27912` is inside a component instance. I'll apply this fix to the main component instead, which will update all instances automatically."

---

### Rule 6: Always verify with a screenshot after editing
After each `perform_editing_operations` call:
```
1. Call get_screenshot on the same nodeId
2. Show the screenshot to the user
3. If the visual doesn't match intent → call perform_editing_operations again with corrected values
4. Never assume the edit worked without visual confirmation
```

---

## Reading Component Structure

Well-structured Figma files use a naming convention like:
```
ComponentName/Variant/State
Button/Primary/Default
Button/Primary/Hover
Button/Secondary/Disabled
Icon/Arrow/Right
```

When auditing, note if components follow this pattern. If they don't, flag it as a 🟡 Warning — it means the design won't scale well and handoff to developers will be harder.

---

## Common Figma-Specific Issues to Flag

| Issue | Figma Signal | Severity |
|---|---|---|
| No components used | All layers are "Frame", "Rectangle", "Group" | 🟡 |
| Detached components | Layer shows "⚠ Detached" | 🟡 |
| Inconsistent text styles | No shared text styles defined | 🟡 |
| Inconsistent color styles | No shared color styles defined | 🟡 |
| Missing auto-layout | Fixed-position elements that should flex | 🟢 |
| No grids defined | Layout grid not applied to frames | 🟢 |
| Unlabeled frames | Frames named "Frame 1", "Frame 2" | 🟢 |
| Missing variants | Component has no hover/disabled states | 🟡 |
| Images not masked | Raw image fills without mask shapes | 🟢 |

---

## When Figma MCP Isn't Available

If Figma MCP tools aren't connected, ask the user to:
1. Export a screenshot (PNG) and share it
2. Or share specific values they see in Figma's right panel (colors, font sizes, spacing)
3. Or copy-paste the CSS Figma generates (right panel → Inspect → CSS)

The CSS export from Figma is particularly useful — it contains exact font sizes, colors, and spacing values that can be checked programmatically.
