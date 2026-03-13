# Motion & Animation Reference

## Why Animation Rules Exist

Animation has a job. When it does its job it reduces cognitive load, orients the user, and confirms actions. When it doesn't, it distracts, delays, and exhausts. Every animation in a UI should pass the "why is this here?" test.

---

## The Animation Purpose Taxonomy

Every animation falls into one of these categories. If it doesn't, it's decoration — and decoration should be removed.

| Purpose | What It Does | Example |
|---|---|---|
| **Orientation** | Shows *where* something came from or is going | Panel slides in from the right = it came from a sub-layer |
| **Feedback** | Confirms an action was received | Button press → brief scale-down → spring back |
| **State change** | Communicates a change of status | Toggle switches from off to on |
| **Relationship** | Shows connection between two things | Shared element transitions (hero card → detail view) |
| **Attention** | Draws the eye to something important | New notification badge pulses once |
| **Progress** | Shows work is happening | Loading bar, skeleton shimmer |

❌ Pure decoration: "logo spins on load because it looks cool" — remove.

---

## Duration Scale

UI animation should feel instant but not jarring. These are the standard duration bands:

| Category | Duration | When to Use |
|---|---|---|
| **Micro** | 100–150ms | Hover states, color changes, button presses |
| **Transition** | 150–250ms | Element enter/exit, expanding panels, tooltips |
| **Page** | 250–400ms | Route transitions, modal open/close |
| **Emphasis** | 400–600ms | Onboarding, celebration, error shake |
| **Never** | 600ms+ | Nothing in UI. Feels broken. |

**Common mistakes:**
- Sidebar slides in at 600ms → user waits, feels sluggish
- Toast notification exits at 1200ms → user waits for it to leave
- Staggered list animation total > 500ms → last item feels forgotten

**Rule of thumb:** If you're animating data or content the user is waiting for, err shorter. If you're animating a transition the user triggered, 200ms feels crisp.

---

## Easing Reference

Easing defines the velocity curve. Using the wrong easing makes motion feel physically wrong.

| Easing | Curve | When to Use |
|---|---|---|
| `ease-out` | Fast start, slow end | **Entering elements.** Things entering the screen decelerate into place — like an object being placed. |
| `ease-in` | Slow start, fast end | **Exiting elements.** Things leaving the screen accelerate away — like an object being picked up. |
| `ease-in-out` | Slow start, slow end | **Elements that move but stay.** Drawers, panels sliding across the screen. |
| `linear` | Constant speed | **Looping animations only** (spinners, progress). Linear motion for one-off transitions feels mechanical and unnatural. |
| `spring` | Overshoot + settle | **High-energy feedback.** Button presses, pull-to-refresh, elastic effects. Use sparingly. |

**CSS values for reference:**
```css
ease-out:     cubic-bezier(0, 0, 0.58, 1)
ease-in:      cubic-bezier(0.42, 0, 1, 1)
ease-in-out:  cubic-bezier(0.42, 0, 0.58, 1)
spring:       Use spring() in Figma or CSS @spring in motion libraries
```

**Common mistake**: Using `ease-in` for entering elements — it starts slow, making the UI feel unresponsive.

---

## Figma Smart Animate Naming Conventions

Figma's Smart Animate transitions work by matching layer names across frames. For it to work correctly:

### Naming Rules
```
ComponentName/Variant/State

Button/Primary/Default   →   Button/Primary/Hover    ✅ Smart Animate works
button-default           →   button-hover             ✅ Works (if exact names match)
Rectangle 12             →   Rectangle 12             ❌ Accidental match possible
```

**Smart Animate checklist:**
- [ ] Layer that should animate has the **exact same name** in both source and destination frame
- [ ] Layer **types match** — text animates to text, frame to frame
- [ ] Layers are **not inside renamed groups** between variants
- [ ] Use `Move in`, `Move out` with direction for panel/sheet transitions
- [ ] Use `Dissolve` for opacity-only changes (tooltips, overlays)
- [ ] Use `Smart Animate` for property-based transitions (size, position, color)

### What to Audit in Figma
When reviewing a Figma prototype with animations:
- Check variant layer names match exactly if Smart Animate is expected
- Check that Delay is 0ms unless a stagger is intentional and < 50ms per item
- Check that prototype connections use the right animation type for the context
- Flag any animation with duration > 400ms as 🟡 Warning, > 600ms as 🔴 Critical

---

## Reduced Motion: Non-Negotiable

The `prefers-reduced-motion` media query is not optional. Some users have vestibular disorders where moving UI can cause nausea, dizziness, or seizures.

### Required behaviour:
```css
@media (prefers-reduced-motion: reduce) {
  /* Remove or reduce all animations */
  * {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
  }
}
```

### In Figma:
There is no built-in reduced motion support in Figma prototypes. Flag this as a 🟡 Warning on any Figma audit — note that developers must implement it in code. If the file has a `Motion: Reduced` page or variant, mark it ✅.

### What still works at reduced motion:
- **Opacity changes**: Fading is generally acceptable (not physical movement)
- **Color transitions**: Fine
- **Scroll-based changes**: Use `scroll-behavior: auto` instead of `smooth`
- **State changes with no movement**: Toggle from off→on with color change, no slide

### What must be removed at reduced motion:
- Parallax effects
- Scroll-triggered entrance animations
- Auto-playing video or GIF backgrounds
- Bouncing/pulsing attention animations
- Staggered list animations

---

## Autoplay & Loop Rules

| Type | Rule |
|---|---|
| Background video | Pause button required. No audio autoplay. |
| Looping animation | Stop or pause after 3 loops. Never infinite without user control. |
| Skeleton screens | OK to loop infinitely — they communicate "loading", stop when content arrives |
| Loading spinners | OK to loop — clear purpose, user understands it will stop |
| Celebration confetti | Max 3s, then stop. Never infinite. |
| Marquee / ticker | Only if user can pause it (WCAG 2.2.2) |

**WCAG 2.2.2 (Pause, Stop, Hide):** Any moving, blinking, or scrolling content that (1) starts automatically, (2) lasts more than 5 seconds, and (3) is presented in parallel with other content — must have a mechanism to pause, stop, or hide it.

---

## Stagger Animations

Staggered list item animations (each item enters slightly after the last) can work well if:
- Total stagger duration < 300ms
- Per-item delay: 30–50ms
- Only on first load, not every state change

```
Item 1: delay 0ms
Item 2: delay 40ms
Item 3: delay 80ms
Item 4: delay 120ms (total: 120ms + duration ≈ fine)
Item 8: delay 280ms (total: 280ms + duration ≈ borderline)
Item 15: delay 560ms (total: 560ms + duration ≈ 🔴 too slow)
```

---

## Animation Anti-Patterns

| Anti-pattern | Problem | Fix |
|---|---|---|
| Loading spinner on < 1s operations | Creates perceived slowness | Show spinner only after 300ms delay |
| Entrance animation on every page load | Gets annoying after first visit | One-time or skip-if-visited |
| Simultaneous animations on multiple elements | Overwhelming, hard to track | Sequence or stagger with clear hierarchy |
| Hover animation with no click confirmation | User can't tell if action worked | Add a distinct pressed state |
| Long exit animation on dismissed content | User waits for content they don't want | Exit animations should be shorter than entrances |
| Scale > 105% on hover | Feels aggressive | Keep scale hover to 101–103% |

---

## Severity Guide for Cat 8

| Issue | Severity |
|---|---|
| No reduced motion support (code) | 🔴 Critical |
| Animation > 600ms | 🔴 Critical |
| Infinite autoplay loop with no pause | 🟡 Warning |
| Wrong easing (ease-in on entrance) | 🟡 Warning |
| Animation with no purpose (pure decoration) | 🟡 Warning |
| Total stagger > 400ms | 🟡 Warning |
| Animation > 300ms (shorter than 600ms) | 🟢 Tip |
| Spring easing used too liberally | 🟢 Tip |
| No Smart Animate naming convention | 🟢 Tip (Figma only) |
