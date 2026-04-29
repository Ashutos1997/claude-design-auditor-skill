# Nielsen's Usability Heuristics Reference

This reference covers Category 19 of the Design Auditor skill.

Nielsen's 10 Usability Heuristics are the most widely used framework for evaluating interface usability. They were developed by Jakob Nielsen and are grounded in decades of usability research.

**Scope of this category:**
4 heuristics are already fully covered by existing categories and are not re-audited here:
- H4 (Consistency and Standards) → Cat 5
- H5 (Error Prevention) → Cat 7
- H8 (Aesthetic and Minimalist Design) → Cat 4
- H9 (Help Users Recover from Errors) → Cat 11 + Cat 12

This category audits the 6 heuristics with genuine gaps: H1, H2, H3, H6, H7, H10.

---

## Usability Score

The Usability Score is separate from the Overall Score and the Accessibility Score.
Start at 100. Apply standard deduction tiers from the main audit:
- 🚫 Blocker: −12pts (rare in heuristics — reserved for complete absence of fundamental feedback)
- 🔴 Critical: −8pts
- 🟡 Warning: −4pts
- 🟢 Tip: −1pt

**Floor is 0.**

**Scoring bands:**
- **90–100** → Heuristically sound
- **70–89** → Minor usability gaps
- **50–69** → Significant usability issues — likely frustrating real users
- **< 50** → Fundamental usability failures — warrants user testing before shipping

Display as: **Usability Score: X/100**

---

## H1 — Visibility of System Status (gaps only)

*Cat 11 covers loading, empty, and error states. This section covers the gaps Cat 11 doesn't.*

**Definition:** The system should always keep users informed about what is going on, through appropriate feedback within a reasonable time.

**Why it matters:** When users don't know what the system is doing, they lose trust, repeat actions, or abandon the task. Silence after an action is one of the most common causes of double-submission and frustration.

**Gaps not covered by Cat 11:**

### H1a — No feedback on button/CTA click

**Figma detection:**
- Identify primary action buttons (submit, save, send, confirm)
- Check if a loading state variant exists in the component
- Flag if: button has no loading/processing state → 🔴 Critical

**Code detection:**
```
Look for async action handlers on buttons:
  → onClick → async function / fetch / axios / supabase / API call
  → Button has no disabled or loading state during the async operation → 🔴

Patterns to flag:
  <button onClick={handleSubmit}>Submit</button>
  async function handleSubmit() { await api.post(...) }
  // No button.disabled = true or loading state set → 🔴

Correct pattern:
  <button disabled={isLoading} onClick={handleSubmit}>
    {isLoading ? <Spinner /> : 'Submit'}
  </button>
```

**Severity:** 🔴 Critical — users will click multiple times

**Fix:** Every button that triggers an async action must have a loading state. Disable the button during processing. Show a spinner or "Saving…" label.

**Context note:** Static buttons (navigation, modal open) don't need loading states. Only buttons that trigger async operations or processing.

---

### H1b — No success confirmation after form submit

**Figma detection:**
- On multi-step forms or standalone forms: check if a success/confirmation screen or toast exists after submission
- Flag if: form audit reveals no confirmation state in the component or flow

**Code detection:**
```
Look for form submission handlers:
  → onSubmit / handleSubmit → async operation
  → No toast, alert, redirect, or success message triggered after resolve → 🔴

Patterns to flag:
  async function handleSubmit() {
    await api.post('/contact', data)
    // Nothing happens after → 🔴
  }

Correct patterns:
  → toast.success('Message sent!') → ✅
  → router.push('/success') → ✅
  → setSubmitted(true) → show confirmation UI → ✅
```

**Severity:** 🔴 Critical

**Fix:** Every form submission must confirm completion. Use a toast for minor actions, a confirmation screen for significant ones (order placed, account created).

**Context note:** Real-time forms (search-as-you-type, autosave) don't need explicit confirmation on every keystroke — only on intentional submit actions.

---

### H1c — No progress indicator on multi-step flows

**Figma detection:**
- Identify multi-step flows (onboarding, checkout, signup, forms with 3+ screens)
- Flag if: no step indicator (numbered steps, progress bar, breadcrumb trail) is visible

**Code detection:**
```
Multi-step flow patterns:
  → Component with step/currentStep state and no progress indicator rendered → 🟡

Patterns to flag:
  const [step, setStep] = useState(1)
  // No <ProgressBar step={step} total={4} /> or step indicator → 🟡

Correct patterns:
  → <StepIndicator current={2} total={4} /> → ✅
  → <ProgressBar value={50} /> → ✅
  → "Step 2 of 4" text label → ✅
```

**Severity:** 🟡 Warning

**Fix:** Any flow with 3+ steps needs a visible progress indicator. Users need to know how far they are and how much remains.

**Context note:** Linear single-purpose flows (e.g. a 2-step payment) can use a simple "Step 1 of 2" label. Full step indicator components are recommended for flows with 4+ steps.

---

## H2 — Match Between System and the Real World

**Definition:** The system should speak the user's language — using words, phrases, and concepts familiar to the user rather than system-oriented language.

**Why it matters:** Technical jargon, error codes, and developer-facing language in the UI forces users to translate system concepts into human meaning. Every translation step is a potential point of confusion or abandonment.

### H2a — Technical jargon in UI copy

**Figma detection:**
- Read all visible text nodes
- Flag any of the following if they appear in user-facing UI (not dev console):
  - Raw error codes: `404`, `500`, `ECONNREFUSED`, `null`, `undefined`, `NaN`
  - Developer terms: `payload`, `endpoint`, `boolean`, `string`, `array`, `object`, `API`, `JSON`, `HTTP`, `timeout`
  - Database terms: `query failed`, `foreign key`, `constraint violation`, `transaction rolled back`
  - System paths: `/usr/local/...`, stack traces

**Code detection:**
```
Search string literals and error message strings for:
  → Raw HTTP status codes shown to users: "Error 404", "500 Internal Server Error" → 🔴
  → Developer terms in user-facing strings:
    "Invalid payload" → 🔴
    "Null value returned" → 🔴
    "API request failed" → 🟡 (acceptable in some contexts)
    "Connection timeout" → 🟡 (too technical — use "Connection lost. Check your internet.")
  → Stack traces or file paths in error messages → 🔴
  → i18n keys shown raw: "error.auth.token_expired" instead of resolved string → 🔴
```

**Severity:** 🔴 Critical (raw error codes, stack traces) · 🟡 Warning (developer terminology)

**Fix:** Translate all system language into user language:
- "Error 404" → "We can't find that page"
- "Invalid payload" → "Something went wrong with your request — please try again"
- "ECONNREFUSED" → "Can't connect to the server — check your internet connection"
- "null" → remove entirely or replace with meaningful empty state

**Context note:** Technical content in developer tools, API documentation, or admin/debug panels is acceptable — those users expect and understand it. Only flag jargon in consumer-facing UI.

---

### H2b — Icons that don't match their real-world meaning

**Figma detection:**
- Identify icon + label pairs in nav, buttons, and actions
- Flag if: an icon's conventional meaning doesn't match its function in this UI
  - Floppy disk for save → ✅ (still universally understood)
  - Magnifying glass for search → ✅
  - Magnifying glass for "zoom in" (same icon as search in same product) → 🟡
  - House icon for "Dashboard" (when user's home is elsewhere) → 🟢 Tip
  - Hamburger menu on desktop in a non-menu context → 🟡

**Code detection:**
```
Icon component name vs aria-label or button context:
  → <SearchIcon /> inside a button labeled "Zoom" → 🟡
  → <TrashIcon /> used for "Archive" action → 🟡
  → <HeartIcon /> used for "Bookmark" or "Save" (not favourites) → 🟡
  
Check aria-label against icon component name for semantic mismatch:
  <button aria-label="Delete"><ArchiveIcon /></button> → 🟡
```

**Severity:** 🟡 Warning

**Fix:** Use the icon that matches the action's real-world mental model. When an icon's meaning is ambiguous in context, always pair it with a visible text label.

**Context note:** Some icon meanings have evolved (e.g. floppy disk = save) — don't flag established conventions even if the real-world object is obsolete. Only flag mismatches that would genuinely confuse a new user.

---

### H2c — Date, number, and unit formats that don't match user locale

*Cat 13 covers this in detail for i18n/RTL contexts. This check applies even when full i18n is not implemented.*

**Figma detection:**
- Look for hardcoded date formats in text nodes: "MM/DD/YYYY", "DD-MM-YY"
- Flag if: a specific format is hardcoded that may not match the user's locale

**Code detection:**
```
→ Hardcoded date format strings: "MM/DD/YYYY", new Date().toLocaleDateString() without locale → 🟡
→ Hardcoded currency symbols ($, £, €) outside a locale formatter → 🟡
→ Large numbers without thousand separators in UI text → 🟢 Tip

Correct patterns:
  → new Intl.DateTimeFormat(userLocale, options).format(date) → ✅
  → new Intl.NumberFormat(userLocale).format(number) → ✅
```

**Severity:** 🟡 Warning

**Fix:** Use `Intl.DateTimeFormat` and `Intl.NumberFormat` for all date and number rendering. Never hardcode format strings.

---

## H3 — User Control and Freedom (gaps only)

*Cat 16 covers back buttons and breadcrumbs. Cat 11 covers undo for destructive actions. This section covers the remaining gaps.*

**Definition:** Users often choose system functions by mistake and will need a clearly marked "emergency exit" to leave the unwanted state without having to go through an extended dialogue.

**Why it matters:** When users feel trapped — in a modal, flow, or state they didn't intend — they lose confidence in the product. An obvious exit path is a sign of respect for user autonomy.

### H3a — Modal/dialog without a close mechanism

**Figma detection:**
- Identify modal and dialog components
- Flag if: no close button (×), no "Cancel" CTA, and no visible backdrop-click dismissal signal
- Flag if: close button is < 24×24px or has < 3:1 contrast (too hard to find)

**Code detection:**
```
Modal/dialog patterns:
  → <dialog>, role="dialog", or modal class with no close button → 🔴
  → No Escape key handler on modal → 🟡
  → No backdrop click handler (when modal is dismissible) → 🟡

Patterns to flag:
  <div role="dialog">
    <h2>Confirm action</h2>
    <button onClick={confirm}>Yes</button>
    // No cancel/close button → 🔴
  </div>

Correct pattern:
  <div role="dialog" aria-modal="true">
    <button aria-label="Close" onClick={onClose}>×</button>
    <h2>Confirm action</h2>
    <button onClick={confirm}>Yes</button>
    <button onClick={onClose}>Cancel</button>
  </div>
```

**Severity:** 🔴 Critical

**Fix:** Every modal must have a visible close mechanism — an × button, a Cancel CTA, or both. Modals must also respond to Escape key. Never trap users in a modal without an exit.

**Context note:** Intentionally uncloseable modals (e.g. session expired, forced upgrade) are acceptable in specific product contexts — but even these must have a clear next action so users aren't stuck indefinitely.

---

### H3b — Multi-step flow with no way to go back

**Figma detection:**
- On multi-step flows: check each step for a visible Back button or previous step navigation
- Flag if: steps 2+ have no backward navigation
- Flag if: flow can only be exited by completing it (no cancel/exit option)

**Code detection:**
```
Multi-step flow with no back:
  → step > 1 rendered with no back button / setStep(step - 1) handler → 🟡
  → Wizard flow with no cancel or exit path → 🟡

Patterns to flag:
  {step === 2 && <Step2 onNext={() => setStep(3)} />}
  // No onBack prop or cancel option → 🟡

Correct pattern:
  <Step2
    onNext={() => setStep(3)}
    onBack={() => setStep(1)}
    onCancel={handleCancel}
  />
```

**Severity:** 🟡 Warning (back navigation) · 🔴 Critical (no exit from a committed flow like checkout)

**Fix:** Every step in a multi-step flow must have a Back option. Long flows (3+ steps) must also have a Cancel or Save & Exit option so users can leave without losing progress.

**Context note:** Some flows legitimately prevent going back for data integrity reasons (e.g. payment processing step). In these cases, disable the Back button with a clear explanation rather than hiding it.

---

### H3c — Destructive action with no confirmation or undo

*Cat 11 covers this partially. This check focuses on detection signals.*

**Figma detection:**
- Identify destructive actions: delete, remove, archive, cancel subscription, clear all
- Flag if: no confirmation dialog, warning modal, or undo mechanism is shown
- Flag if: destructive CTA is styled the same as safe CTAs (no red/warning treatment)

**Code detection:**
```
Destructive action patterns:
  → onClick={handleDelete} with no confirmation dialog triggered → 🟡
  → window.confirm() used (browser native — poor UX but functional) → 🟢 Tip
  → No toast with undo after soft-delete → 🟡

Patterns to flag:
  <button onClick={() => deleteItem(id)}>Delete</button>
  // No confirmation, no undo → 🟡

Correct patterns:
  → Custom confirmation dialog: "Delete 'Project Alpha'? This cannot be undone." → ✅
  → Soft-delete with toast: "Deleted — Undo" (5 second window) → ✅
```

**Severity:** 🟡 Warning (reversible actions) · 🔴 Critical (permanently destructive with no confirmation)

**Fix:** Permanent destructive actions must have a confirmation dialog that names the item being deleted. Reversible destructive actions should offer an Undo toast (3–5 seconds). Never use a generic "Are you sure?" — name the thing being deleted.

---

## H6 — Recognition Rather Than Recall

**Definition:** Minimize the user's memory load by making objects, actions, and options visible. The user should not have to remember information from one part of the interface to another.

**Why it matters:** Human short-term memory is limited. Every time the UI requires a user to remember something from a previous step or screen, it increases cognitive load and error rate.

### H6a — Icon-only navigation with no visible labels

**Figma detection:**
- Identify navigation components (sidebar nav, bottom nav, toolbar)
- Flag if: nav items have icons but no visible text labels
- Exception: tooltips on hover are acceptable for secondary actions but not primary navigation

**Code detection:**
```
Icon-only nav items:
  → <nav> or role="navigation" items with icon child but no visible text → 🟡
  → aria-label present but no visible label → 🟡 (accessible but not heuristically ideal)

Patterns to flag:
  <nav>
    <a href="/home"><HomeIcon /></a>
    <a href="/settings"><SettingsIcon /></a>
  </nav>
  // Icons only, no labels → 🟡

Correct pattern:
  <a href="/home">
    <HomeIcon aria-hidden="true" />
    <span>Home</span>
  </a>
```

**Severity:** 🟡 Warning (primary nav) · 🟢 Tip (secondary/toolbar actions)

**Fix:** Primary navigation items must have visible text labels alongside icons. Icon-only nav forces users to memorise icon meanings, which is a recall burden. Tooltips are acceptable for toolbars and secondary actions.

**Context note:** Bottom navigation bars on mobile commonly use icon-only or icon+label layouts — flag icon-only on mobile bottom nav as 🟢 Tip (space constrained) not 🟡. Desktop sidebars and top nav always need labels.

---

### H6b — Floating labels that disappear on focus

**Figma detection:**
- Identify input fields with floating label behaviour
- Flag if: the label moves inside the input and disappears when the user starts typing (label used as placeholder)
- Flag if: the value is entered and the label is no longer visible (user cannot see what field they filled)

**Code detection:**
```
Floating label patterns that remove the label:
  → input:focus + label { display: none } → 🔴
  → placeholder used as label with no persistent label → 🔴
    (already flagged in Cat 12, reinforce here)

Acceptable floating label:
  → Label moves to top of field on focus, remains visible throughout → ✅
  → Label + separate placeholder hint below field → ✅

Problematic pattern:
  → Label disappears entirely when user types, showing only the value → 🟡
    (user can't verify which field they're editing)
```

**Severity:** 🔴 Critical (label disappears) · 🟡 Warning (label obscured during input)

**Fix:** Labels must remain persistently visible — above the input, not inside it. If floating labels are used, ensure they persist at the top of the field during and after input, never disappear.

**Context note:** The "material design floating label" pattern (label moves from inside to above the field on focus) is acceptable if the label remains fully visible throughout. The issue is specifically when the label disappears at any point.

---

### H6c — No visible selected state in dropdowns or filters

**Figma detection:**
- Identify dropdown, select, filter, and segmented control components
- Flag if: selected/active option has no clear visual differentiation from unselected options
- Flag if: after closing a dropdown, the selected value is not shown in the trigger button

**Code detection:**
```
Dropdown trigger after selection:
  → Trigger button shows placeholder text even after selection → 🔴
    e.g. <button>Select a category</button> after user selected "Design" → 🔴

Selected state in open dropdown:
  → Selected option has no checkmark, highlight, or bold treatment → 🟡
  → aria-selected="true" present but no visual distinction → 🟡

Filter active state:
  → Active filter tag/pill has no visual distinction from inactive → 🟡
  → Active filter count badge missing → 🟢 Tip
```

**Severity:** 🔴 Critical (trigger doesn't show selection) · 🟡 Warning (no visual distinction in list)

**Fix:** Dropdown triggers must always show the currently selected value. Open dropdown lists must clearly mark the selected option with a checkmark, highlight, or bold. Active filters must be visually distinct from inactive ones.

---

## H7 — Flexibility and Efficiency of Use

**Definition:** Accelerators — unseen by the novice user — may often speed up the interaction for the expert user such that the system can cater to both inexperienced and experienced users.

**Why it matters:** As users gain experience, they develop expectations of being able to do things faster. An interface that can only be used slowly frustrates power users and reduces productivity.

### H7a — No keyboard shortcut hints for power users

**Figma detection:**
- Look for tooltip components in the design
- Flag if: complex or frequently-used actions (save, new item, search, undo) have no keyboard shortcut hint in their tooltip or adjacent label
- Flag if: tooltip components exist but show no shortcut hints

**Code detection:**
```
Keyboard shortcut accessibility:
  → accesskey attributes used → ✅ (check for conflicts with browser shortcuts)
  → Keyboard event listeners for shortcuts (Cmd+S, Ctrl+K, etc.) → ✅

Shortcut hint display:
  → Shortcut exists in code but tooltip doesn't show it → 🟢 Tip
  → No keyboard shortcuts at all on a complex productivity tool → 🟡

Look for:
  → document.addEventListener('keydown', ...) with shortcut logic → if no hint shown → 🟢 Tip
  → useHotkeys / react-hotkeys / mousetrap library imported → check if hints are shown
```

**Severity:** 🟢 Tip (general) · 🟡 Warning (on tools where power users are the primary audience)

**Fix:** Add keyboard shortcut hints to tooltips for frequently-used actions. Format: "Save (⌘S)" or "New item (⌘N)". This is low effort and high value for productivity tools.

**Context note:** Consumer apps (social, e-commerce) rarely need keyboard shortcuts. This check is most relevant for productivity tools, design tools, dashboards, and admin interfaces where users spend significant time.

---

### H7b — No bulk action support where expected

**Figma detection:**
- Identify list views, table views, or grid views with multiple items
- Flag if: no "select all" checkbox, no bulk action toolbar, no multi-select pattern in a list that clearly warrants it (emails, tasks, files, orders)

**Code detection:**
```
List/table with no bulk selection:
  → Table/list component with no checkbox column → 🟡 if list implies management
  → No selectedItems state in a list that clearly supports management operations → 🟡

Context signals that warrant bulk actions:
  → Items have delete/archive/move actions → no bulk = 🟡
  → List has 10+ items → no bulk = 🟡
  → Admin interface → no bulk = 🟡

Correct pattern:
  → <input type="checkbox" onChange={handleSelectAll} /> in table header → ✅
  → selectedItems.length > 0 && <BulkActionBar /> → ✅
```

**Severity:** 🟡 Warning (clearly management-oriented lists) · 🟢 Tip (general lists)

**Fix:** Any list or table where users are expected to manage multiple items should support bulk selection. Add a checkbox column, a "Select all" header, and a contextual bulk action toolbar.

**Context note:** Display-only lists (search results, feeds, product listings) don't need bulk actions. Only management-oriented lists (inbox, file manager, task list, order management) warrant this check.

---

### H7c — No saved preferences or recently used items

**Figma detection:**
- Look for dropdowns, selectors, or pickers that users use repeatedly
- Flag if: no "Recently used", "Pinned", or "Favourites" section in search, filters, or pickers that clearly warrant it (emoji pickers, colour pickers, template selectors)

**Code detection:**
```
Persistence patterns:
  → Frequently used form fields with no autocomplete/remembered values → 🟢 Tip
  → Search component with no recent searches stored in localStorage → 🟢 Tip
  → Settings changed by user with no persistence across sessions → 🟡

Positive signals:
  → localStorage / sessionStorage used to remember user preferences → ✅
  → Autocomplete with recent/suggested values → ✅
  → "Remember me" / "Save as default" option on settings → ✅
```

**Severity:** 🟢 Tip (general) · 🟡 Warning (productivity tools where re-entering settings is frequent)

**Fix:** Persist user preferences across sessions using localStorage or a backend store. Add "Recently used" sections to frequently-accessed pickers. Autocomplete previous values in repeat-use form fields.

---

## H10 — Help and Documentation

**Definition:** Even though it is better if the system can be used without documentation, it may be necessary to provide help and documentation. Any such information should be easy to search, focused on the user's task, list concrete steps to be carried out, and not be too large.

**Why it matters:** Complex interfaces inevitably have learning curves. When help is absent or hard to find, users abandon tasks or develop incorrect mental models that cause repeated errors.

### H10a — Complex inputs with no helper text

**Figma detection:**
- Identify complex or non-obvious form fields:
  - Password fields (no strength indicator or requirements shown)
  - Fields with specific format requirements (phone, date, credit card, VAT number)
  - Fields with character limits
  - Upload fields (no file type/size guidance)
- Flag if: no helper text, hint, or tooltip explains what's expected

**Code detection:**
```
Missing helper text patterns:
  → <input type="password"> with no password requirements shown → 🟡
  → <input type="tel"> with no format hint → 🟢 Tip
  → <input maxLength={280}> with no character counter → 🟡
  → <input type="file"> with no accepted types shown → 🟡

Correct patterns:
  → <p id="password-hint">8+ chars, 1 number, 1 symbol</p>
    <input aria-describedby="password-hint" type="password"> → ✅
  → Character counter: {value.length}/280 → ✅
  → Dropzone: "Accepted: JPG, PNG, PDF. Max 10MB." → ✅
```

**Severity:** 🟡 Warning (password, character-limited, formatted fields) · 🟢 Tip (simple fields)

**Fix:** Every field with a specific format requirement, character limit, or file constraint must show that information before the user submits — not only in an error message after failure.

**Context note:** Simple text fields (first name, email) don't need helper text unless there's a specific constraint. Only flag fields where the requirements are non-obvious.

---

### H10b — Error messages with no guidance on how to fix

*Cat 12 covers error message tone. This check covers error messages that fail to guide recovery.*

**Figma detection:**
- Read all error message text nodes
- Flag if: error message states what went wrong but gives no actionable next step
  - "Invalid email" alone → 🟡 (states the problem, no guidance)
  - "Please enter a valid email address (e.g. name@example.com)" → ✅

**Code detection:**
```
Error message strings:
  → "Invalid input" → 🔴 (no information at all)
  → "Error" → 🔴 (completely unhelpful)
  → "Invalid email" → 🟡 (states problem, no fix)
  → "Please enter a valid email (e.g. name@example.com)" → ✅
  → "Password must be at least 8 characters" → ✅ (actionable)

Search for error strings shorter than 4 words → likely too vague → 🟡
```

**Severity:** 🔴 Critical (no information) · 🟡 Warning (states problem but no fix guidance)

**Fix:** Every error message must: (1) say what went wrong, (2) say how to fix it. The format: "[What's wrong] — [how to fix it]". "Invalid email" → "Please enter a valid email address (e.g. name@example.com)."

---

### H10c — No contextual help on ambiguous or complex UI sections

**Figma detection:**
- Identify complex dashboard sections, settings with non-obvious implications, or forms with technical fields
- Flag if: no tooltip, info icon, or help text is present on fields/sections where a new user would reasonably be confused

**Code detection:**
```
Tooltip/help signal detection:
  → Tooltip component or [?] icon near complex fields → ✅
  → title attribute on non-interactive elements → 🟢 Tip (not keyboard accessible)
  → aria-describedby with helper text → ✅

Missing help patterns:
  → Complex settings section (billing cycle, permissions, webhook config) with no help text → 🟡
  → Technical fields (API key, webhook URL, CRON expression) with no explanation → 🟡
```

**Severity:** 🟡 Warning (clearly complex or technical fields) · 🟢 Tip (generally non-obvious)

**Fix:** Add a tooltip or inline helper to any field or setting where a new user would reasonably not know what to enter or what the consequence of their choice is. Link to documentation for truly complex features.

**Context note:** Don't add helper text to every field — that creates noise. Reserve it for genuinely ambiguous inputs and high-stakes settings. If everything has a tooltip, nothing does.

---

### H10d — No onboarding guidance on first use

**Figma detection:**
- Look for empty state screens, first-time user flows, or blank dashboard states
- Flag if: empty state shows no guidance on what to do first
- Flag if: there is no onboarding tooltip sequence, welcome message, or getting started prompt on a complex product

**Code detection:**
```
First-use detection:
  → localStorage.getItem('hasOnboarded') check with no onboarding UI → 🟡
  → Empty state component with no CTA or next-step guidance → 🟡
  → Dashboard with no tutorial prompt on first login → 🟢 Tip

Correct patterns:
  → {!hasOnboarded && <WelcomeTour />} → ✅
  → Empty state: <p>No projects yet — <a href="/new">Create your first project</a></p> → ✅
```

**Severity:** 🟡 Warning (complex product with no onboarding) · 🟢 Tip (simple product)

**Fix:** New users landing on an empty state need direction. Every empty state must tell the user what this section is for and how to populate it. Complex products should have a lightweight first-use tour or checklist.

---

## Quick Reference Checklist

**H1 — Visibility of System Status (gaps)**
- [ ] Buttons that trigger async actions have loading states
- [ ] Form submissions confirm success with toast or confirmation screen
- [ ] Multi-step flows (3+ steps) have a visible progress indicator

**H2 — Match Between System and Real World**
- [ ] No raw error codes, stack traces, or developer terms in UI copy
- [ ] Icons match their conventional real-world meaning in this context
- [ ] Date and number formats use locale-aware formatting

**H3 — User Control and Freedom (gaps)**
- [ ] All modals have a visible close mechanism and respond to Escape
- [ ] Multi-step flows have a Back option on every step except the first
- [ ] Destructive actions have confirmation dialogs or undo options

**H6 — Recognition Rather Than Recall**
- [ ] Primary navigation items have visible text labels alongside icons
- [ ] Form labels remain visible during and after input (no disappearing labels)
- [ ] Dropdown triggers show the currently selected value

**H7 — Flexibility and Efficiency of Use**
- [ ] Keyboard shortcut hints shown in tooltips for power user actions (where relevant)
- [ ] Lists that support management operations have bulk selection
- [ ] User preferences are persisted across sessions where relevant

**H10 — Help and Documentation**
- [ ] Fields with specific format requirements show helper text before error
- [ ] Error messages state what went wrong AND how to fix it
- [ ] Complex or technical fields have contextual tooltips
- [ ] First-use / empty states give clear next-step guidance
