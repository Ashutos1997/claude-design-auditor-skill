# Ethical Design & Dark Patterns Reference

Ethical design means building interfaces that respect users' autonomy, attention, and interests. Persuasion is a normal and legitimate part of good design — the line is crossed when a pattern deceives users, exploits cognitive biases against their interests, or removes their ability to make an informed, free choice.

This reference covers Category 18 of the Design Auditor skill.

---

## The Ethical Line

**Persuasion is acceptable when:**
- It accurately represents facts and outcomes
- It highlights genuine value to the user
- It makes desired actions easier without making undesired actions impossible
- The user retains full understanding and control of what they're agreeing to

**Persuasion becomes manipulation when it:**
- Deceives users about facts, prices, consequences, or availability
- Exploits cognitive biases (loss aversion, social pressure, anchoring) against the user's interests
- Makes unwanted outcomes harder to reach than wanted ones through friction, not logic
- Obscures, buries, or misrepresents what the user is consenting to
- Uses emotional pressure (guilt, fear, shame) as a primary decision driver

When in doubt, apply this test: **"Would the user feel tricked if they understood what just happened?"** If yes, it's manipulation.

---

## Detection Scope

Not all patterns are equally detectable from design files or code. Declare confidence level per finding.

| Pattern | Figma detectable? | Code detectable? | Confidence |
|---|---|---|---|
| Confirmshaming | ✅ Text content | ✅ String literals | 🟢 High |
| CTA hierarchy inversion | ✅ Size, color, weight | ✅ Class/style comparison | 🟢 High |
| Trick questions | ✅ Label text | ✅ Label + input text | 🟢 High |
| Pre-checked consent | ❌ N/A | ✅ checked attribute | 🟢 High |
| Countdown timers | ✅ Timer UI elements | ✅ JS timer / CSS | 🟢 High |
| Guilt-based copy | ✅ Text content | ✅ String literals | 🟢 High |
| Privacy zuckering | ✅ Toggle defaults | ✅ Default values | 🟢 High |
| False scarcity (hardcoded) | ✅ Static text | ✅ Static strings | 🟡 Medium |
| False social proof (hardcoded) | ✅ Static numbers | ✅ Static strings | 🟡 Medium |
| Disguised ads | ✅ Visual similarity | ⚠️ Partial | 🟡 Medium |
| Hidden costs | ⚠️ Multi-step flow only | ⚠️ Partial | 🟡 Medium |
| Roach motel | ⚠️ Multi-screen flow | ⚠️ Route count | 🟡 Medium |
| Bait and switch | ✅ CTA label vs outcome | ⚠️ Partial | 🟡 Medium |
| Obstruction | ⚠️ Step count | ⚠️ Route complexity | 🟡 Medium |
| Nagging | ⚠️ Multi-screen only | ⚠️ State logic | 🔴 Low |
| Forced continuity | ❌ Requires backend | ❌ Requires backend | ❌ Not detectable |
| Friend spam | ⚠️ Contact UI patterns | ✅ Pre-selected inputs | 🟡 Medium |
| Visual misdirection | ✅ Layout analysis | ⚠️ Partial | 🟡 Medium |

**When confidence is 🟡 Medium:** Flag as potential issue. State the detection limitation. Ask the user to verify.
**When confidence is 🔴 Low:** Do not flag unless the user specifically requests a flow-level ethics review.
**When not detectable:** Do not guess or speculate. Note in the audit that pattern cannot be assessed from static design alone.

---

## Ethics Severity Model

Ethics issues use a different severity framing from the rest of the audit. These are not design mistakes — they are intentional choices that range from potentially harmful to clearly deceptive.

| Severity | Label | Meaning | Score deduction |
|---|---|---|---|
| 🔴 | **Deceptive** | Actively misleads or coerces users. Violates user trust and in many jurisdictions consumer law. Must fix. | −15 pts |
| 🟡 | **Questionable** | Persuasive in a way that may exploit users depending on context. Warrants review and justification. | −7 pts |
| 🟢 | **Noted** | Persuasive element present. Ethical in standard use but worth documenting for transparency. | 0 pts (informational) |

Ethics score starts at 100. Floor is 0.

**Scoring bands:**
- **90–100** — Ethically sound
- **70–89** — Minor concerns, review questionable patterns
- **50–69** — Significant manipulation risk, recommend redesign before shipping
- **< 50** — Deliberately deceptive — do not ship

Display as: **Ethics Score: X/100** in the report, alongside the Accessibility Score.

---

## Pattern Taxonomy

### GROUP A: Deceptive Interface Patterns

---

#### A1. Confirmshaming

**Definition:** Decline/cancel options are written to induce guilt or shame, pressuring users to accept rather than freely choose.

**Real-world example:** A newsletter popup with "Yes, sign me up!" and "No thanks, I prefer to stay uninformed" as the decline option.

**Why it's harmful:** It weaponises self-image against the user's best interest. The user can't decline without implicitly accepting a negative label about themselves. It removes genuine free choice.

**Figma detection:**
- Read all cancel/decline/skip CTA text nodes
- Flag if decline copy: uses first-person self-deprecation ("I don't want to..."), implies the user is foolish for declining, or phrases the refusal as a loss of identity

**Code detection:**
```
Search for button/link text containing patterns like:
  "No thanks, I prefer"
  "No, I don't want"
  "I'd rather not save"
  "I hate [benefit]"
  Any decline option written in first person with negative framing

Also check: <a> and <button> elements near consent modals/popups
```

**Severity:** 🔴 Deceptive

**Fix:** Rewrite decline copy as a neutral, factual statement: "No thanks" / "Maybe later" / "Skip" / "Close". The user's choice to decline needs no editorial comment.

**Context note:** First-person CTA copy for the *accept* option ("Yes, I want better design!") is a standard and acceptable persuasion technique. Only the *decline* option is subject to this check.

---

#### A2. CTA Hierarchy Inversion

**Definition:** The visual hierarchy of action buttons is deliberately inverted so the option that benefits the company (e.g. accept, subscribe, agree) appears as the primary action and the option that benefits the user (e.g. decline, cancel, close) is visually subordinated or hidden.

**Real-world example:** A cookie consent dialog where "Accept all" is a large, coloured primary button and "Manage preferences" is small grey text below — despite both being valid user choices.

**Why it's harmful:** Users follow visual hierarchy automatically. Inverting it exploits scanning behaviour to steer choices rather than inform them.

**Figma detection:**
- On any consent, subscription, or upsell screen: identify the pair of accept/decline actions
- Flag if: accept button is significantly larger, uses brand colour or filled style, and decline is text-only, grey, or below a visual fold
- Flag if: there are 2+ dismissal steps to reach the same outcome as 1 acceptance step

**Code detection:**
```
Find paired action buttons (e.g. inside modal, cookie banner, paywall):
  Accept/Primary: class="btn-primary" or bg-[brand] or font-bold
  Decline/Secondary: class="text-sm text-gray-400" or visually minimal

Flag if:
  Primary styled button leads to subscription/consent/purchase
  AND secondary/text link leads to user's preferred non-committal outcome
  AND the size/weight/color difference is > 2 Tailwind size steps

Also check:
  Cookie banners: "Accept all" button vs "Reject all" or "Manage" — must be equivalent visual weight
  (Required by GDPR/ePrivacy — unequal styling = legal risk in EU, not just ethical risk)
```

**Severity:** 🔴 Deceptive

**Fix:** Actions of equal consequence to the user must receive equivalent visual weight. If two choices are both valid, style them at the same level (both secondary, or one primary/one outlined — not primary vs invisible text).

**Context note:** It is acceptable for a *recommended* action to be styled as primary (e.g. "Save" vs "Discard changes" — save is logically primary). The manipulation is specifically when the company-beneficial option is elevated over the user-beneficial one without logical justification.

---

#### A3. Trick Questions

**Definition:** Consent checkboxes or form fields are written using double negatives, confusing language, or inverted logic so that the "safe" user choice (opt out, decline) requires careful reading to identify.

**Real-world example:** "Uncheck this box if you do not wish to not receive marketing emails." Checking = opted in. Unchecking = opted out. No one knows.

**Why it's harmful:** Cognitive load means users often check or uncheck without reading carefully. Deliberately confusing wording exploits this to obtain consent that wasn't freely given.

**Figma detection:**
- Read all checkbox label text nodes associated with consent/marketing/data sharing
- Flag any label containing: double negatives, "unless", "except", "if you do not", inverted opt-out framing

**Code detection:**
```
Search label text associated with <input type="checkbox"> for:
  - "do not" + "not" (double negative)
  - "unless you" / "except where"
  - Any label where the positive action (checking) results in opting OUT

Pattern to flag:
  <label for="marketing">Uncheck to opt out of marketing</label>
  <label for="share">Leave unchecked to not share your data</label>

Correct pattern:
  <label for="marketing">Send me marketing emails</label>
  (checked = opted in — clear, no negatives)
```

**Severity:** 🔴 Deceptive

**Fix:** Rewrite all consent labels as positive, first-person affirmative statements. Checking = consenting, always. "Send me updates", "Share my data with partners", "Enrol in loyalty programme". No negatives.

**Context note:** "I agree to the Terms of Service" — checking = agreeing — is standard and fine. The issue is specifically consent options where the action and outcome are inverted or obscured.

---

#### A4. Disguised Ads

**Definition:** Advertisements or promoted content are styled to visually resemble organic content, editorial results, or UI elements, obscuring their commercial nature.

**Real-world example:** Search results where sponsored listings use the same card style as organic results, with only a tiny grey "Ad" label.

**Why it's harmful:** Users rely on visual distinction to apply appropriate scepticism to commercial content. Removing that distinction deceives users into treating paid placement as editorial recommendation.

**Figma detection:**
- Look for "Ad", "Sponsored", "Promoted" labels on content cards
- Flag if: the label is < 11px, uses low-contrast colour (< 3:1 against background), or is positioned in a non-prominent location (e.g. bottom-right corner, below the fold of the card)
- Flag if: sponsored content cards are visually identical to organic content cards with no secondary signal beyond text

**Code detection:**
```
Search for elements with "ad", "sponsored", "promoted" labels:
  → Label font-size < 12px → 🔴
  → Label color contrast < 3:1 against card background → 🔴
  → No visual distinction (border, background, icon) beyond text label alone → 🟡
  → aria-label or role missing on ad container → 🟡
    (screen readers need to know it's an advertisement)
```

**Severity:** 🔴 Deceptive

**Fix:** Sponsored/promoted content must be visually distinct from organic content. Use a consistent label ("Sponsored") in readable size (≥ 12px) with sufficient contrast, placed prominently (top-left of card). A background tint or border on sponsored cards provides a secondary visual signal.

**Context note:** Clearly labelled "Featured" or "Partner" content sections with distinct visual treatment are acceptable. The issue is specifically when commercial and organic content are made to look identical.

---

#### A5. Bait and Switch

**Definition:** A CTA or link promises one outcome but delivers a different, typically more committed or costly one.

**Real-world example:** A "See pricing" button that goes directly to a payment form pre-filled with the most expensive plan, skipping the pricing comparison page.

**Why it's harmful:** Users make decisions based on expected outcomes. Delivering a different outcome without warning bypasses informed consent.

**Figma detection:**
- On multi-step flows: check if CTA label accurately describes the destination
- Flag: "See plans" → payment screen (not plans page)
- Flag: "Learn more" → sign-up form (not information)
- Flag: "Free trial" → credit card required screen without prior disclosure

**Code detection:**
```
Check <a href> and <button> destinations vs their text content:
  → "Free" in label but destination URL contains /payment, /checkout, /billing → 🟡
  → "Learn more" linking to /signup or /register → 🟡
  → "Try for free" where the linked page requires credit card without this being
     disclosed before the click → 🔴

Also check for pre-selected plan tiers on pricing/signup pages:
  → Most expensive plan pre-selected by default → 🟡
```

**Severity:** 🔴 Deceptive (when credit card / payment is involved) · 🟡 Questionable (when destination is unexpected but not costly)

**Fix:** CTA labels must accurately describe the immediate next step. If a free trial requires a credit card, state it on the CTA: "Start free trial (card required)". Never pre-select the most expensive plan.

**Context note:** "Get started" as a general CTA for a signup flow is fine — it's a convention users understand. The issue is specifically when the CTA implies a lower-commitment action than what actually occurs.

---

#### A6. Hidden Costs

**Definition:** Fees, charges, or required add-ons are revealed only at the final step of a purchase flow, after the user has invested time and effort in the process.

**Real-world example:** A booking flow that shows £49 throughout, then adds a £12 "service fee" and £3 "payment processing fee" only at the checkout confirmation screen.

**Why it's harmful:** Sunk cost psychology means users are more likely to accept hidden fees at the final step than if disclosed upfront. This is deliberate exploitation of a cognitive bias.

**Figma detection (multi-frame flows):**
- On checkout/booking/subscription flows: look for price displays at each step
- Flag if: a price shown early in the flow differs from the total on the confirmation/payment screen without an explicit explanation of additional charges
- Flag if: "Taxes and fees" are listed as "+ fees" with no amount shown until the final screen

**Code detection:**
```
On checkout flows, look for:
  → Price displayed as number in early steps → different number in payment step
    without an explicit fee breakdown element in between → 🔴
  → "Processing fee", "Service fee", "Convenience fee" elements that only appear
    in the final checkout component → 🔴
  → Fee disclosure only in <small> or footnote text → 🟡
```

**Severity:** 🔴 Deceptive

**Fix:** Show the complete price — including all fees and taxes — from the first step where a price is shown, or as early as possible. If exact fees can't be calculated early, show an estimate with a clear "Final price at checkout" note.

**Context note:** Showing taxes calculated at checkout (because they depend on location) is acceptable if "taxes will be added" is disclosed upfront. The manipulation is specifically hiding fees that are always charged regardless of user input.

---

#### A7. Visual Misdirection

**Definition:** Layout, colour, animation, or visual weight is used to draw attention away from important information (terms, costs, risks) and toward conversion-positive elements.

**Real-world example:** A subscription signup where the price and billing frequency are in small light-grey text below the fold, while a large colourful button dominates the screen.

**Why it's harmful:** Visual hierarchy is a primary way users decide what to read. Deliberately inverting it for important information prevents informed consent.

**Figma detection:**
- On pricing/consent/checkout screens: identify all text that describes cost, commitment, or risk
- Flag if: such text is: < 13px, below 3:1 contrast, placed below the primary CTA, or in a location users are unlikely to read before acting
- Flag if: animation or visual weight strongly directs attention away from fee/commitment disclosure

**Code detection:**
```
On pricing/checkout/consent pages, find text containing:
  "per month", "per year", "billed annually", "renews at", "after trial",
  "terms", "cancel anytime" (verify it's actually easy to cancel — see Roach Motel)

Flag if that text has:
  → font-size < 13px → 🟡
  → color contrast < 3:1 → 🔴 (also a Cat 2 issue)
  → position: below the primary CTA in DOM order → 🟡
  → opacity < 0.7 → 🟡
```

**Severity:** 🟡 Questionable (layout) · 🔴 Deceptive (when combined with hidden costs or false urgency)

**Fix:** Any text describing cost, commitment, or risk must meet the same visual standards as the CTA it accompanies. Minimum 13px, 4.5:1 contrast, positioned before or at the same level as the primary action button.

**Context note:** Styling a primary CTA prominently is legitimate. The issue is specifically when important *constraining* information (costs, terms, risks) is visually suppressed.

---

### GROUP B: Coercive Flows

---

#### B1. Roach Motel

**Definition:** A flow is designed so that entering a subscription, service, or commitment is significantly easier than exiting it. The cancellation path requires substantially more effort than the sign-up path.

**Real-world example:** Signing up for a subscription takes 2 clicks. Cancelling requires navigating to Account → Settings → Billing → Manage Subscription → Cancel → Confirm Cancel → Receive Retention Offer → Confirm Again → Wait 5 days.

**Why it's harmful:** Asymmetric friction exploits the path of least resistance. Users who intended to cancel often give up, paying for a service they don't want. This is deceptive by design.

**Figma detection (multi-frame flows):**
- Count the steps from intent to completion for: sign up, subscribe, add payment
- Count the steps from intent to completion for: cancel, unsubscribe, close account, remove payment
- Flag if: cancellation requires significantly more steps (2×+) than sign-up
- Flag if: a "cancellation" flow leads to retention offers, plan downgrades, or pause options before the actual cancel option

**Code detection:**
```
Navigation structure check:
  → Sign-up route: /signup → /payment → done (2 steps)
  → Cancel route: /account → /settings → /billing → /manage → /cancel
    → /confirm → /retention-offer → /final-confirm (7 steps)
  → Step count ratio > 2:1 (cancel:signup) → 🔴

Also check:
  → Cancel button leads to a retention modal before actual cancellation → 🟡
    (one retention offer is acceptable; multiple forced offers before cancel = 🔴)
  → "Delete account" option buried in settings hierarchy 3+ levels deep → 🟡
```

**Severity:** 🔴 Deceptive

**Fix:** The path to exit a commitment must be no harder than the path to enter it. If sign-up is 2 steps, cancellation must be reachable in ≤ 3 steps. One genuine retention offer (e.g. "Before you go — would you like to pause instead?") is acceptable if the user can immediately proceed to cancel.

**Context note:** Asking "Are you sure?" before a destructive action is a legitimate UX safety pattern. One confirmation step on cancellation is fine. Multiple forced steps, retention loops, and buried options are not.

---

#### B2. Obstruction

**Definition:** Unnecessary steps, friction, or complexity are deliberately added to paths the company doesn't want users to take (unsubscribe, opt out, delete data, close account).

**Real-world example:** An unsubscribe link in an email leads to a page requiring the user to: log in, navigate to preferences, uncheck 12 individual email types, and click save — rather than a one-click unsubscribe.

**Why it's harmful:** Friction is a tool that works. Artificial friction on user-beneficial paths is a deliberate barrier to exercising rights.

**Figma detection:**
- On opt-out/deletion/unsubscribe flows: count form fields and steps required
- Flag if: unsubscribe requires login when the user arrived from an email
- Flag if: data deletion requires contacting support rather than a self-serve option
- Flag if: GDPR data export/deletion is not present or requires > 3 steps

**Code detection:**
```
On privacy/account deletion/unsubscribe flows:
  → Login required to unsubscribe from email → 🔴
    (one-click unsubscribe is the standard and legally required in many jurisdictions)
  → Data deletion only available via support contact form, not self-serve → 🟡
  → Account deletion requires email verification + 7-day wait + re-confirmation → 🟡
    (some delay is acceptable for security; making it confusing is not)
  → Unsubscribe confirmation page has re-subscribe CTA more prominent than confirmation → 🟡
```

**Severity:** 🔴 Deceptive (when it blocks legally required actions) · 🟡 Questionable (unnecessary friction on non-legal paths)

**Fix:** Unsubscribe must be one click from the email link. Account/data deletion must be self-serve and reachable in ≤ 3 steps. Privacy rights (GDPR, CCPA) are legal obligations, not optional UX choices.

**Context note:** A reasonable confirmation step ("Your account will be deleted in 14 days — here's how to recover it") is legitimate safety design. The issue is specifically friction that serves no user interest.

---

#### B3. Forced Action

**Definition:** Users are required to complete an unwanted action (share data, accept marketing, connect a social account) in order to access a feature or complete an unrelated task.

**Real-world example:** An app that requires connecting your Facebook account to use its core features, even though the features don't require social data.

**Why it's harmful:** Bundling unrelated consent with core functionality coerces agreement that wouldn't be freely given in isolation.

**Figma detection:**
- On onboarding/signup flows: identify any steps that collect data or permissions beyond what the core product requires
- Flag if: social login is the *only* option with no email alternative
- Flag if: marketing consent is a required field (not optional) in a signup form
- Flag if: permission requests (location, contacts, notifications) are presented before their functional context is clear

**Code detection:**
```
Signup/onboarding forms:
  → Marketing consent checkbox with required attribute → 🔴
    (<input type="checkbox" required> on a marketing opt-in)
  → Social OAuth as sole login option (no email/password alternative) → 🟡
  → Permission requests (geolocation, notifications) on first load with no
    explanation of why they're needed → 🟡

Account connection flows:
  → Third-party data connection presented as required step in core onboarding → 🟡
    (should be optional or clearly labelled as optional)
```

**Severity:** 🔴 Deceptive (when bundled with legally required consent) · 🟡 Questionable (when it restricts reasonable alternatives)

**Fix:** Marketing consent must always be optional and unchecked by default. Core product functionality must be available without requiring unrelated data sharing. Offer email/password as an alternative to social login.

**Context note:** Requiring account creation to access a personalised service is legitimate. Requiring data collection that goes beyond what the service needs is not.

---

#### B4. Nagging

**Definition:** The same prompt, request, or offer is shown repeatedly after the user has explicitly dismissed it, exploiting persistence to wear down resistance.

**Real-world example:** An app that shows a "Rate us!" popup every session despite the user tapping "Not now" each time.

**Why it's harmful:** Repeat prompts after explicit dismissal disrespect the user's stated preference and erode trust.

**Figma detection (multi-screen audits):**
- Flag if: the same modal, banner, or prompt appears across multiple screens without a visible "don't show again" or session-persistent dismiss option
- Flag if: a dismissed notification/banner has no permanent dismiss mechanism

**Code detection:**
```
Look for localStorage/sessionStorage/cookie patterns:
  → Prompt shown based on session count without a "permanently dismissed" flag → 🟡
  → showRatingPrompt() called on every app open with no check for prior dismissal → 🟡
  → Cookie consent banner that reappears after being dismissed (without clearing cookies) → 🔴

Pattern to flag:
  if (sessionCount % 3 === 0) showPromoModal() // no dismissal check → 🟡
  
Correct pattern:
  if (!localStorage.getItem('promoDismissed')) showPromoModal()
```

**Severity:** 🟡 Questionable (after 1 dismissal) · 🔴 Deceptive (after explicit "don't show again")

**Fix:** Every dismissible prompt must have a permanently effective dismiss option. After explicit dismissal, never show the same prompt again in the same session. Respect "not now" as a soft no; respect "don't show again" as a hard no.

**Context note:** A first-time prompt for reviews, push notifications, or premium features is legitimate. Persistence beyond one dismissal per session, or ignoring "don't show again", is nagging.

---

### GROUP C: Consent & Privacy Manipulation

---

#### C1. Privacy Zuckering

**Definition:** Privacy settings default to maximum data sharing, and the interface is designed to make restricting sharing confusing, time-consuming, or hard to find.

**Named after:** Mark Zuckerberg's approach to Facebook privacy settings.

**Real-world example:** A cookie consent dialog where all non-essential cookies are pre-enabled, and "Manage preferences" leads to 12 categories each requiring individual opt-out, while "Accept all" is one click.

**Why it's harmful:** Defaults have an enormous effect on user behaviour. Defaulting to maximum data sharing obtains consent that most users would not give if the choice were equally easy in both directions.

**Figma detection:**
- On privacy settings / cookie consent screens: check the default state of all data-sharing toggles
- Flag if: any non-essential data sharing toggle is ON by default
- Flag if: privacy controls are in Settings → Privacy → Advanced → Manage rather than reachable in ≤ 2 steps
- Flag if: "Accept all" and "Reject all" are not equivalent in visual weight

**Code detection:**
```
Check default state of consent inputs:
  → <input type="checkbox" checked> on any non-essential data sharing option → 🔴
  → <toggle default="true"> on analytics/marketing/advertising categories → 🔴
  → "Accept all" button present without equivalent "Reject all" button → 🔴
    (GDPR requires symmetrical consent withdrawal)

Cookie consent libraries:
  → consentRequired: false for analytics category → 🔴
  → Default consent: 'granted' for non-essential categories → 🔴
  → Google Consent Mode with analytics_storage: 'granted' as default → 🔴
```

**Severity:** 🔴 Deceptive (legally non-compliant in GDPR jurisdictions)

**Fix:** All non-essential data collection must default to OFF. "Accept all" and "Reject all" must be visually equivalent. Privacy settings must be reachable in ≤ 2 steps from any screen.

**Context note:** Strictly necessary cookies (session, security, load balancing) can be enabled by default as they are required for the service to function. Only non-essential categories (analytics, advertising, personalisation) must default to off.

---

#### C2. Pre-Checked Consent

**Definition:** Consent checkboxes for marketing communications, data sharing, or third-party sharing are pre-checked by default, requiring users to actively opt out rather than actively opt in.

**Real-world example:** A checkout form where "Yes, send me emails about products and special offers" is pre-checked alongside the required terms agreement.

**Why it's harmful:** Pre-checked consent exploits form completion inertia. Most users complete forms without reading every field. Pre-checking harvests consent that was never genuinely given. Illegal under GDPR/PECR.

**Figma detection:**
- On any form containing consent checkboxes: note which are checked by default in the design
- Flag any checkbox for marketing, data sharing, or third-party sharing that appears checked in the default/empty state

**Code detection:**
```
Direct detection:
  → <input type="checkbox" name="marketing" checked> → 🔴
  → <input type="checkbox" name="newsletter" defaultChecked={true}> (React) → 🔴
  → v-model with default true on consent checkbox (Vue) → 🔴

Bundled consent:
  → Single checkbox that covers both ToS acceptance AND marketing consent → 🔴
    ("I agree to the Terms and want to receive marketing emails" — must be separate)

Correct pattern:
  <input type="checkbox" name="marketing"> (unchecked by default)
  <input type="checkbox" name="terms" required> (ToS — required is acceptable)
```

**Severity:** 🔴 Deceptive (legally non-compliant in GDPR/PECR jurisdictions)

**Fix:** All marketing, data sharing, and third-party consent checkboxes must be unchecked by default. Terms of Service agreement can be required. Marketing consent must always be separate from ToS agreement and always optional.

**Context note:** Functional settings (e.g. "Remember my preferences") can reasonably default to checked. Only consent for data collection, marketing, and sharing with third parties must default to unchecked.

---

#### C3. Interface Interference

**Definition:** Privacy and consent settings are designed with confusing language, inconsistent interaction patterns, or misleading UI elements that make it difficult for users to achieve their intended privacy choices.

**Real-world example:** A cookie settings panel where some categories use a toggle (on = consented) and others use a checkbox (checked = opted out), with no explanation of the difference.

**Why it's harmful:** Interface inconsistency in the specific context of consent exploits cognitive load to obtain broader consent than intended.

**Figma detection:**
- On privacy/settings screens: check for inconsistent control types (mix of toggles and checkboxes) without clear labelling of what each state means
- Flag if: "on" and "off" toggle labels are absent — the user can't tell which direction is consent
- Flag if: save/apply button is not present after changes (no clear way to confirm intent)

**Code detection:**
```
Consent UI patterns:
  → Mix of checkbox and toggle inputs within the same consent category group → 🟡
  → Toggle without visible on/off or yes/no label → 🟡
  → aria-checked missing on custom toggle elements → 🟡
  → No save/apply button — changes auto-save without confirmation → 🟡
    (user may not know their change was registered)
  → Visually identical "on" and "off" toggle states (relies on position only) → 🟡
    (must pass 3:1 contrast between states)
```

**Severity:** 🟡 Questionable

**Fix:** All consent controls within the same panel must use consistent interaction patterns. Toggles must have visible "On/Off" or "Yes/No" labels. A "Save preferences" button must confirm intent. Never auto-apply privacy changes.

**Context note:** Mixing toggles and checkboxes in general settings is fine. The issue is specifically in consent and privacy contexts, where the consequences of misunderstanding are significant.

---

#### C4. Drip Pricing

**Definition:** The total price of a product or service is revealed incrementally across multiple steps, with each step adding fees, charges, or required add-ons.

**Real-world example:** A flight booking that shows £45 → adds £12 seat selection → adds £8 luggage → adds £5 payment fee → final total: £70 (56% more than initially shown).

**Why it's harmful:** Each incremental reveal exploits sunk cost psychology. By the time the final price appears, the user has invested time and expects the price they saw initially. This is a form of false advertising.

**Figma detection (multi-frame flows):**
- Track price display across booking/checkout flow frames
- Flag if: price shown in step 1 differs from price shown in final confirmation without an explicit, prominent breakdown of what was added and why

**Code detection:**
```
Price element tracking across checkout steps:
  → Price component shows different value on /checkout vs /payment
    without a fee breakdown component between them → 🔴
  → Optional add-ons (seats, luggage, insurance) defaulting to selected → 🟡
  → "Booking fee", "Service fee" appearing only on the final step → 🔴

Also flag:
  → Required add-ons presented as optional during flow then added automatically → 🔴
  → Total price element only present on the final confirmation step → 🟡
```

**Severity:** 🔴 Deceptive

**Fix:** Show the total price (including all mandatory fees) from the first price display. Optional add-ons must be clearly optional and unselected by default. A running total that updates in real time as add-ons are selected is the ethical pattern.

**Context note:** Showing taxes calculated at the final step (because they depend on user location) is acceptable if the pre-tax price is clearly labelled as such. Mandatory fees that don't depend on user input must be included in all price displays.

---

### GROUP D: False Urgency & Scarcity

---

#### D1. Countdown Timers

**Definition:** A countdown timer implies that a price, offer, or availability will expire at the deadline — when in reality the offer resets or the timer is artificial.

**Real-world example:** A "Sale ends in 02:47:22" timer on a product page where the same sale has been running for months and the timer simply resets every day.

**Why it's harmful:** Artificial urgency exploits loss aversion to rush decisions users would otherwise take more time on. False urgency is deceptive about the nature of the offer.

**Figma detection:**
- Identify countdown timer UI elements (numerical countdown, progress bar depleting, ticking clock)
- Flag: timer present on a pricing/offer screen → always flag as potential issue (confidence: medium — can't verify if timer is real from static design)
- Note: ask the user whether the timer is backed by real data

**Code detection:**
```
Detect timer implementations:
  → setInterval() or setTimeout() used to count down from a fixed number → 🟡
  → Timer initialized from localStorage or sessionStorage (resets per session) → 🔴
  → Timer initialized from a hardcoded future date that doesn't change → 🔴
  → Timer that resets on page refresh (visible in network tab patterns) → 🔴

Real (acceptable) timers:
  → Timer value fetched from API endpoint → ✅ (may be real)
  → Timer initialized from a server-side expiry timestamp → ✅ (may be real)
  
Flag as 🟡 in all cases; upgrade to 🔴 if reset/artificial pattern is confirmed.
```

**Severity:** 🟡 Questionable (unverified) · 🔴 Deceptive (when reset pattern is confirmed)

**Fix:** Only use countdown timers for genuinely time-limited offers with a real end date. If the offer doesn't expire, remove the timer. If it does expire, ensure the timer value comes from a real server-side timestamp that does not reset.

**Context note:** Flash sales with real expiry dates and stock-based countdowns ("X left") backed by real inventory data are legitimate urgency signals. The issue is specifically fabricated or perpetually-resetting timers.

---

#### D2. False Scarcity

**Definition:** Inventory, availability, or demand is presented as more limited than it actually is to pressure purchase decisions.

**Real-world example:** "Only 2 rooms left at this price!" shown on a hotel booking site when there are actually 47 rooms available.

**Why it's harmful:** Manufactured scarcity exploits loss aversion to accelerate decisions the user would otherwise make more carefully.

**Figma detection:**
- Identify scarcity indicators: "Only X left", "X remaining", "Limited availability", "Almost sold out"
- Flag: hardcoded numbers in scarcity text → always flag (medium confidence — can't verify accuracy from static design)
- Note: ask user whether numbers are dynamic or static

**Code detection:**
```
Search string literals for scarcity language:
  → "Only [number] left" as a hardcoded string → 🟡
  → "Limited availability" with no data binding → 🟡
  → "X people viewing this" with static/fake number → 🔴 (see D3)

Real (acceptable) scarcity:
  → Scarcity number bound to inventory API: {stockCount} remaining → ✅
  → Scarcity element only rendered when stock < threshold from real data → ✅

Also flag:
  → "Low stock" label that appears regardless of actual inventory → 🟡
```

**Severity:** 🟡 Questionable (hardcoded, unverifiable) · 🔴 Deceptive (confirmed inaccurate)

**Fix:** Scarcity indicators must be backed by real, real-time inventory data. If you can't connect scarcity claims to live data, remove them.

**Context note:** Genuine "last few available" messaging backed by real inventory is a legitimate and helpful signal for users who need to make timely decisions. The issue is fabricated or exaggerated scarcity.

---

#### D3. False Social Proof

**Definition:** User counts, viewer numbers, or social validation figures are fabricated or misleading, creating artificial impression of demand or popularity.

**Real-world example:** "1,247 people are looking at this right now" shown simultaneously to all users, where the number is randomly generated between 800 and 1,500.

**Why it's harmful:** Social proof is a powerful trust and urgency signal. Fabricating it is straightforwardly deceptive — it creates false beliefs about product demand.

**Figma detection:**
- Identify social proof elements: "X people viewing", "X purchased today", "X happy customers"
- Flag: large, specific, round-ish numbers in social proof text (e.g. "10,000 customers") → medium confidence — can't verify accuracy
- Flag: viewer count / activity feed UI elements → always flag for verification

**Code detection:**
```
Search for social proof patterns with static values:
  → "people viewing" or "viewing this" with hardcoded number → 🔴
  → Random number generation for social proof:
      Math.floor(Math.random() * 500) + 800 → 🔴 (extremely common pattern)
  → "X sold today" as static string → 🟡

Real (acceptable) social proof:
  → Viewer count fetched from WebSocket or real-time API → ✅
  → Purchase count from real analytics data → ✅
  → Review count from real review data → ✅

Also flag:
  → Testimonials without attribution (name, photo, company) → 🟢 Noted
  → Review counts that can't be independently verified → 🟢 Noted
```

**Severity:** 🔴 Deceptive (fabricated numbers) · 🟡 Questionable (unverifiable claims)

**Fix:** All social proof numbers must reflect real data. Remove viewer counts if they can't be backed by real-time data. Real customer counts, real review scores, and real testimonials are all legitimate.

**Context note:** "Join 50,000+ designers" on a marketing page is acceptable if the number is real and updated. The issue is specifically fabricated or algorithmically-generated social pressure.

---

### GROUP E: Emotional Manipulation

---

#### E1. Guilt-Based Copy

**Definition:** UI copy uses shame, guilt, or implied self-judgement to pressure users into accepting offers, upgrading, or continuing engagement.

**Real-world example:** An app that sends push notifications saying "You've been neglecting your goals 😔" or shows "You're falling behind your streak!"

**Why it's harmful:** Using negative emotional states (guilt, shame, anxiety) as conversion tools exploits psychological vulnerabilities, particularly harmful for users with anxiety or perfectionism.

**Figma detection:**
- Read notification, empty state, and retention copy text nodes
- Flag if: copy implies failure, neglect, or inadequacy for not using the product
- Flag if: copy uses self-referential shame framing ("you've been slipping", "you're falling behind")

**Code detection:**
```
Search string literals for guilt/shame patterns:
  → "You haven't [verbed] in X days" with implicit negative framing → 🟡
  → "Don't lose your streak" (mild — acceptable) vs
     "You're letting yourself down" (guilt — not acceptable) → 🔴
  → "Your [friends/team/competitors] are [ahead/doing better]" → 🟡
  → Notification copy containing "neglect", "fail", "behind", "disappointing" → 🔴
  → Empty state copy implying the user is at fault for having no data → 🟡
```

**Severity:** 🔴 Deceptive (explicit shame/guilt) · 🟡 Questionable (implied inadequacy)

**Fix:** Motivational copy should focus on positive outcomes, not negative self-image. "Keep the momentum going!" not "You're falling behind." "Resume where you left off" not "You've been neglecting this." Acknowledge inactivity neutrally; never shame it.

**Context note:** Streak mechanics, completion percentages, and progress indicators are legitimate engagement tools when framed positively. Loss framing ("you'll lose your streak") is a borderline pattern — acceptable in mild doses, manipulative when central to the engagement loop.

---

#### E2. Fear Appeals

**Definition:** Disproportionate or exaggerated negative consequences are implied to pressure users into action, exploiting fear of loss, harm, or failure.

**Real-world example:** A password manager showing "Your accounts are EXPOSED" in large red text, implying imminent breach, when the actual risk is ordinary.

**Why it's harmful:** Disproportionate fear appeals cause anxiety that may lead to rushed decisions. When the fear is exaggerated or manufactured, it's manipulative.

**Figma detection:**
- Read warning/alert/empty state copy for fear-based framing
- Flag if: risk language uses all-caps, red colour, or alarm icons for ordinary states
- Flag if: consequences described are significantly more severe than what would actually occur

**Code detection:**
```
Search for disproportionate warning language:
  → "EXPOSED", "COMPROMISED", "AT RISK", "VULNERABLE" for non-critical states → 🟡
  → Red alert styling on informational messages → 🟡 (also a Cat 2 issue)
  → Modal that cannot be dismissed without taking an action (forced fear conversion) → 🔴
  → "Your data could be stolen" messaging on a standard upsell prompt → 🔴
```

**Severity:** 🟡 Questionable (exaggerated risk) · 🔴 Deceptive (fabricated risk used for conversion)

**Fix:** Risk communication should be proportionate to actual risk. Use appropriate severity levels: info, warning, error. Reserve red/alarm styling for genuine high-severity states. Never exaggerate risk to drive upgrades or signups.

**Context note:** Genuine security warnings ("Your password was found in a data breach") are legitimate and important. The issue is using fear-based language for routine prompts or upsells where the risk is minor or manufactured.

---

#### E3. Toying with Emotion

**Definition:** UI design deliberately targets emotional vulnerabilities — anxiety, loneliness, FOMO, regret — as primary drivers of engagement or conversion, beyond what serves the user's interests.

**Real-world example:** A social app that withholds notification counts until the user opens the app, then reveals a large number to create a dopamine hit. Or an e-commerce site that shows "You almost missed this deal!" on items the user viewed.

**Why it's harmful:** Deliberately engineering emotional states to drive engagement treats users as mechanisms to exploit rather than people to serve. It's particularly harmful for users with anxiety, addiction vulnerability, or compulsive behaviour patterns.

**Figma detection:**
- Identify: notification badges with counts hidden until interaction, "You almost..." / "Don't miss out" copy, regret-inducing copy on cart abandonment screens
- Flag: patterns whose primary purpose is emotional state creation rather than information delivery

**Code detection:**
```
Patterns to flag:
  → Notification badge count hidden behind interaction (revealed on click/hover) → 🟡
  → "You almost had it" / "Items in your cart are selling out" language → 🟡
  → Countdown on cart abandonment email links → 🟡
  → Variable reward patterns: content that withholds gratification unpredictably → 🟡
    (infinite scroll with delayed content reveal, pull-to-refresh with spinner)
  → Push notification permission request immediately after first valuable action
    (harvesting emotional high for permission) → 🟡
```

**Severity:** 🟡 Questionable

**Fix:** Notification counts should be visible without requiring interaction. Cart and wish-list reminders can reference real urgency but should not manufacture emotional pressure. Push notification permission requests should be contextual (triggered when a feature requires it), not timed to emotional peaks.

**Context note:** Excitement about new messages or genuine urgency about real availability are legitimate emotional contexts. The issue is deliberately engineering emotional states (anxiety, FOMO, regret) as a conversion mechanism rather than as a natural response to genuine product value.

---

## Ethical Persuasion — What NOT to Flag

These are persuasive design techniques that are legitimate, ethical, and should never be penalised by the ethics audit.

| Technique | Why it's acceptable |
|---|---|
| **Prominent primary CTA** | Clear visual hierarchy helps users understand what to do. Acceptable when the primary action is logically primary. |
| **Genuine social proof** | Real review counts, real testimonials, real customer numbers. Helps users make informed decisions. |
| **Real scarcity** | Accurate "last few available" backed by real inventory. Genuinely useful information. |
| **Real urgency** | Offers with genuine expiry dates, real events with real deadlines. |
| **Progress indicators** | Progress bars, completion percentages, streaks — when framed positively. |
| **Anchoring (transparent)** | Showing a higher price before a discounted price when both are real. |
| **Loss framing (honest)** | "You'll lose your saved items if you don't sign in" — when true and stated once. |
| **Reciprocity** | Free tier, free trial, free content offered genuinely before asking for commitment. |
| **Scarcity countdown** | Countdown backed by a real server-side expiry that doesn't reset. |
| **Completion motivation** | "You're 80% done — finish setting up your profile!" — positive framing, accurate. |
| **Feature gating** | Showing premium features as locked — transparent about what requires upgrade. |
| **Onboarding nudges** | Tooltips, highlights, empty state CTAs that guide users to value. Not repeated after dismissal. |

**Rule of thumb:** If the persuasive element is honest, proportionate, and serves the user's ability to make an informed decision — it's ethical persuasion, not manipulation.

---

## Quick Reference: Ethics Audit Checklist

Use this during Cat 18 to ensure all patterns are checked:

**Group A — Interface**
- [ ] No confirmshaming on decline/cancel options
- [ ] Accept and decline actions have equivalent visual weight (especially cookie consent)
- [ ] All consent copy uses positive, non-double-negative language
- [ ] Ads/sponsored content are visually distinct from organic content
- [ ] CTA labels accurately describe the immediate next action
- [ ] All fees shown from first price display

**Group B — Flows**
- [ ] Cancellation path ≤ 2× the steps of sign-up path
- [ ] Unsubscribe works in one click from email
- [ ] No required fields collecting non-essential data
- [ ] Dismissed prompts stay dismissed

**Group C — Consent**
- [ ] All non-essential data collection defaults to OFF
- [ ] No pre-checked marketing consent
- [ ] Consent controls are consistent and clearly labelled
- [ ] Total price always shown — no drip pricing

**Group D — Urgency**
- [ ] Countdown timers backed by real server-side data
- [ ] Scarcity claims backed by real inventory
- [ ] Social proof numbers backed by real data

**Group E — Emotion**
- [ ] No shame or guilt in decline/inactivity copy
- [ ] Risk language proportionate to actual risk
- [ ] No emotional manipulation patterns (FOMO engineering, hidden notification counts)
