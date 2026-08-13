---
name: accessibility-audit
description: Review UI code for accessibility barriers that block real users — missing labels, keyboard traps, hidden focus, color-only meaning. Use when the user asks about a11y, accessibility, screen reader support, WCAG compliance, or keyboard navigation.
---

Audit for barriers that actually block someone, ranked by who gets locked out. Don't produce a generic WCAG checklist — read the code and find what breaks.

1. **Blockers first** — these make the interface unusable, not just awkward:
   - Interactive elements that aren't reachable or operable by keyboard: `onClick` on a `div`/`span` with no `tabindex`, no key handler, and no `role`. A mouse-only control is invisible to keyboard and screen reader users.
   - Keyboard traps: modals, dropdowns, and date pickers that take focus and never give it back, or that don't close on `Escape`.
   - Focus that goes nowhere: `outline: none` with no replacement focus style, or focus not moved into a dialog when it opens and not restored when it closes.
   - Controls with no accessible name: icon-only buttons with no text, `aria-label`, or `aria-labelledby`; inputs with no associated `<label>` (a placeholder is not a label).
   - Images carrying meaning with no `alt`, and decorative images with non-empty `alt` that adds noise.

2. **Then structural issues:** heading levels skipped or used for styling, landmarks missing, lists faked with `div`s, tables without headers, and custom widgets with ARIA roles but none of the keyboard behavior that role implies — a `role="tab"` with no arrow-key handling is worse than no role at all, because it promises behavior that isn't there.

3. **Meaning conveyed by color or shape alone** — error states that only turn red, required fields marked only by color, charts distinguishable only by hue. Flag these with the specific alternative (text, icon, pattern).

4. **Don't flag what you can't determine statically.** Contrast ratios depend on computed values, and a `div` with a click handler might have keyboard support attached elsewhere. Say what needs runtime verification rather than asserting a violation you haven't confirmed.

5. Don't recommend adding ARIA where native semantics would work — `<button>` beats `role="button"` with hand-rolled key handlers. The first rule of ARIA is not to use it when HTML already does the job.

6. Output grouped by **blocks users entirely** → **degrades experience** → **verify at runtime**, each with file:line, who it affects concretely ("keyboard users can't submit this form"), and the fix.
