---
name: ux-review
description: Review a UI change from the user's side, checking loading, empty, error, success and disabled states at mobile and desktop width, plus accessibility. Use when reviewing or self-checking any change to UI users see.
---

# UX review

Check that every state of the changed UI exists, looks right, and works for keyboard and screen reader users.

## 1. States

Check each state at 390px and 1280px:

- [ ] Loading (no layout jump when content arrives)
- [ ] Empty (says why and what to do)
- [ ] Error (says what went wrong and how to recover)
- [ ] Success / filled, including long text and many items
- [ ] Disabled and in-progress controls
- [ ] No sideways scroll at 390px; tap targets at least 24x24px (44 preferred)

Use screenshots the caller gives you first. Capture missing states with `verify-ui`. No way to run the app → say which states you could not check.

## 2. Accessibility

Run the `accessibility` checklist on the changed UI. WCAG 2.2 AA failures should be fixed; a keyboard trap or an unlabelled primary control must be fixed.

## 3. Report

Per finding: the state and width, what you saw, the evidence (screenshot or element), and what would fix it. Use the caller's format and severity scale if given. List what you could not verify.
