---
name: check-user-experience
description: Audit a project's UI against all 30 Laws of UX (lawsofux.com) — Fitts, Hick, Jakob, Miller, Doherty, Postel, Tesler, Von Restorff, Peak-End, Serial Position, Zeigarnik, Goal-Gradient, Gestalt (proximity, similarity, common region, uniform connectedness, Prägnanz), cognitive load, choice overload, chunking, Occam's razor, Pareto, Parkinson, aesthetic-usability, mental models, flow, selective attention, working memory, paradox of the active user, cognitive bias. Reports each issue with law, location, severity and a concrete suggested fix. Use when the user says "check user experience", "/check-user-experience", "UX audit", "check UX laws", "is my UX good", or asks to review how usable an app or screen is. Works on codebases (web, React, Vue, Svelte, Angular, React Native, Flutter, SwiftUI), live URLs and screenshots. Reports only; fixes only when asked.
---

# Check User Experience

Audit an interface against the Laws of UX and produce a prioritised report of real issues with concrete fixes. Report, don't fix, unless the user asks.

## Workflow

### 1. Gather the surface
- **Codebase:** detect the stack, then find screens/routes, shared components (buttons, inputs, forms, nav, modals, lists, tables), design tokens, and loading/error states. Read the actual markup and styles, not just file names.
- **Live URL / local build:** open it in the browser, walk the main flows, take screenshots at desktop and mobile widths, measure hit targets and response times where possible.
- **Screenshot:** judge visually; mark code-only checks as "not verifiable".
- Identify the 3–5 primary user tasks (sign up, search, checkout, create X). Audit those flows hardest — Pareto applies to the audit too.

### 2. Check every law
Go through the table below. For each law, look for the listed signals. Only report concrete findings with a location; never pad the report with generic advice. If a law is satisfied, it goes in the "passing" list in one line.

### 3. Report (format below), then offer to fix the High items.

## The laws: what to look for and how to fix

### Interaction & effort
| Law | Flag when | Suggested fix |
|---|---|---|
| **Fitts's Law** — time to hit a target depends on size and distance | Tap targets < 44×44 pt (iOS) / 48×48 dp (Android) / 24×24 CSS px min (WCAG 2.5.8), ideally 44px; icon-only buttons with tiny padding; primary action far from where the eye/thumb is; destructive and primary buttons adjacent with < 8px gap | Increase padding or add `::after { inset:-8px }` hit area; put primary CTA near the content it acts on, in thumb zone on mobile; separate destructive actions |
| **Hick's Law** — decision time grows with number/complexity of choices | Nav with > 7 top-level items; menus/selects with long unsorted lists; many equal-weight CTAs on one screen | Group, progressively disclose ("More"), add search/filter to long lists, sensible defaults, one primary CTA |
| **Choice Overload** | Pricing/plan pages with > 3–4 options and no recommendation; settings dumps; many filters shown at once | Highlight a recommended option, collapse advanced options, comparison table |
| **Doherty Threshold** — productivity soars when response < 400 ms | No loading/pending state on async actions; buttons don't disable or show progress on submit; full-page spinners for small loads; no optimistic updates; blocking network calls on input | Immediate feedback < 100 ms, skeletons, optimistic UI, delayed spinners (~300 ms), debounce search |
| **Postel's Law** — be liberal in what you accept | Strict input formats (phone, dates, card numbers rejecting spaces/dashes); case-sensitive emails; no trimming; validation errors that don't say how to fix; pasting blocked | Normalise input (trim, strip separators), accept multiple formats, `inputmode`/`autocomplete` attrs, helpful error messages |
| **Tesler's Law** — complexity can't be removed, only moved | User forced to do what the system could (re-enter known data, compute values, pick timezone/country that could be inferred) | Autofill, smart defaults, inference, remember last choices |
| **Paradox of the Active User** — users don't read manuals | Features only discoverable via docs/tooltips-on-hover; long onboarding tours before value; empty states with no guidance | Inline hints at point of use, actionable empty states, learn-by-doing onboarding |
| **Flow** | Unnecessary interruptions (modals, confirmations for reversible actions, forced re-auth); no keyboard shortcuts for power paths; lost focus after actions | Undo instead of confirm, preserve focus/scroll, keyboard support, minimise context switches |

### Memory & cognition
| Law | Flag when | Suggested fix |
|---|---|---|
| **Miller's Law / Chunking** | Long unbroken forms; long strings shown raw (IDs, phone, card numbers unformatted); walls of text; nav with 10+ flat items | Section forms into groups/steps, format numbers in chunks, headings and short paragraphs |
| **Cognitive Load** | Dense screens with competing elements; jargon; inconsistent terminology for the same thing; decorative clutter | Remove non-essential elements, consistent labels, plain language |
| **Working Memory** | User must remember info from a previous screen (codes, earlier choices); placeholder-only labels that vanish while typing; comparisons across pages | Keep context visible (summaries, persistent labels, breadcrumbs), side-by-side compare |
| **Mental Model** | Controls that behave unlike their look (link styled as button doing navigation or vice versa); unusual metaphors; save model unclear (autosave vs manual) | Match the users' expectation; make system state explicit |
| **Jakob's Law** — users expect your site to work like others | Non-standard placement (logo not linking home, cart not top-right, search hidden); custom controls replacing native ones without parity; reinvented patterns | Follow platform conventions (HIG / Material / web norms); native elements first |
| **Occam's Razor** | Redundant steps, duplicate controls, features nobody's flow needs on the primary screen | Remove or move to secondary surfaces |
| **Pareto Principle** | Primary tasks buried while rarely-used features get prime space | Promote the 20% of features used 80% of the time |
| **Parkinson's Law** | Forms/tasks that take longer than needed; no autofill; no progress indicators that set expectations | Autocomplete, autofill attrs, show estimated time/steps |
| **Selective Attention / Banner Blindness** | Critical info styled like an ad or placed in banners/sidebars; important warnings blending in | Put key info inline in the task path, distinct but not ad-like |
| **Cognitive Bias** | Dark patterns: pre-checked opt-ins, confirmshaming, hidden costs, fake urgency, misleading defaults | Remove; honest defaults. Flag as High (ethics + legal risk) |

### Perception (Gestalt)
| Law | Flag when | Suggested fix |
|---|---|---|
| **Law of Proximity** | Labels closer to the wrong input; equal spacing between unrelated and related items; helper text far from its field | Tighter spacing inside groups, larger between groups (spacing scale) |
| **Law of Similarity** | Same-looking elements behaving differently (clickable and static text identical); inconsistent button styles for same action level | Consistent visual language per role |
| **Law of Common Region** | Related controls not grouped; cards without boundaries mixing content | Cards, sections, background regions for groups |
| **Law of Uniform Connectedness** | Steps/related items not visually linked | Lines, connectors, shared containers for steppers/timelines |
| **Law of Prägnanz** | Overly complex icons/illustrations, ambiguous shapes | Simplify forms; recognisable icons with labels |
| **Aesthetic-Usability Effect** | Visual inconsistency (mixed radii, random colors, misaligned grid, inconsistent type scale) that erodes trust | Tokenise spacing/type/color; align to a grid |

### Sequence, motivation & memory of experience
| Law | Flag when | Suggested fix |
|---|---|---|
| **Von Restorff Effect** | Primary CTA not visually distinct; everything highlighted so nothing stands out; reliance on color alone | One emphasised primary action per view; use shape/size/icon too (accessibility) |
| **Serial Position Effect** | Key nav items in the middle of long lists; most important info buried mid-page | Put key items first and last (e.g., Home first, Profile/Cart last in tab bar) |
| **Peak-End Rule** | Weak or missing success states (form submit ends with nothing or a plain redirect); harsh error dead-ends; abrupt flow endings | Clear, satisfying confirmation; helpful recovery on errors; polish the final step |
| **Goal-Gradient Effect** | Multi-step flows without progress indication; progress that starts at 0 | Stepper/progress bar, show steps remaining, give a head start |
| **Zeigarnik Effect** | Incomplete tasks forgotten (no drafts, lost form data on navigation, no profile-completion nudges) | Autosave drafts, "continue where you left off", completion checklists |

## Severity

- **High** — blocks or seriously slows a primary task, causes errors/data loss, accessibility failure, or a dark pattern.
- **Medium** — noticeable friction or confusion on a common path.
- **Low** — polish; secondary screens.

## Report format

```
# UX Audit — <app/screen>
Scope: <what was audited> · Method: code / live / screenshot
Score: <passing laws>/30 · High: n · Medium: n · Low: n

## Top 3 to fix first
1. ...

## Findings
### [High] Fitts's Law — Icon buttons too small
Where: src/components/Toolbar.tsx:42 (also Header.tsx:18)
Issue: 20×20px icon buttons with no padding; mis-taps on mobile.
Fix: add `p-3` (44px target) or an `::after` hit-area extension.
```(code snippet if short)```

(…one block per finding, grouped by severity, High first…)

## Passing
- Jakob's Law — standard nav and logo-home pattern.
- ...

## Not verifiable
- Doherty Threshold — needs a running app to measure response times.
```

## Rules
- Every finding needs a law, a location (`file:line`, URL + element, or screenshot region), the observed problem, and a specific fix. No location → don't report it.
- Merge duplicates: one finding per root cause, listing all locations.
- Don't restyle or change the app's personality in fixes; suggest the smallest change that solves the issue.
- Respect platform conventions (iOS HIG, Material, web) when judging Jakob's Law and target sizes.
- Overlap with accessibility (contrast, focus, labels) is fine — note it under the relevant law, but don't turn this into a full WCAG audit.
- After the report, ask whether to fix the High (or all) findings. If motion/feedback issues are found (Doherty, Peak-End, Goal-Gradient), mention `/animate-this-app` can address them.
