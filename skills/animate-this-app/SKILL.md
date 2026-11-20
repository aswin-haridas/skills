---
name: animate-this-app
description: Add tasteful, industry-standard animations and micro-interactions to an existing project without changing its look, voice or personality. Applies Disney's 12 animation principles adapted for UI, UX laws (Fitts, Hick, Miller, Doherty, Postel, Jakob, frequency of use) and proven CSS/motion techniques (0.97 press scale, error shake, directional stepper slides, interpolate-size accordions, springs, rubber-band drag, tooltip delay grouping, reduced-motion support). Use when the user says "animate this app", "/animate-this-app", "add micro-interactions", "add motion", "make it feel alive/polished/responsive", "add transitions", or asks to improve how an existing UI feels. Works with plain HTML/CSS, Tailwind, React, Next.js, Vue, Svelte, Angular, React Native and Flutter.
---

# Animate This App

Add motion that makes an existing app feel responsive, physical and polished, while keeping its original soul. The app should feel like a better version of itself, not like a different product.

## Golden rules

1. **Preserve the soul.** Never change colors, fonts, layout, spacing, copy or component structure to fit an animation. Motion adapts to the app, not the other way round.
2. **Motion must have a purpose.** Every animation guides attention, shows a spatial relationship, or confirms a state change. If it only decorates and costs speed, cut it.
3. **Reuse before adding.** Use the motion library already installed (Framer Motion/Motion, GSAP, react-spring, Vue `<Transition>`, Svelte transitions, Angular animations, Reanimated, Flutter implicit animations). With none installed, use plain CSS. Don't add a dependency unless springs or gestures truly need one, and ask first.
4. **Less is more.** Frequent actions get the least motion. Ten subtle touches beat one show-off effect.
5. **Never break function.** No animation may delay input, hide content, shift layout, or block a click.

## Workflow

### 1. Read the app's personality first

Before touching anything:
- Identify the stack, styling system (CSS, Tailwind, CSS-in-JS, design tokens) and any motion library in `package.json`/`pubspec.yaml`.
- Grep for existing `transition`, `animation`, `@keyframes`, `ease`, `duration`, `motion.`, `useSpring`, `AnimatePresence`, `prefers-reduced-motion`. Existing durations and easings are the house style: match them.
- Classify the personality and pick a motion profile:

| Personality | Signals | Motion profile |
|---|---|---|
| Calm / professional (dashboards, fintech, B2B, docs) | Neutral palette, dense data, sharp corners | Short (120–200 ms), ease-out, no bounce, minimal scale, opacity + small translate |
| Friendly / consumer (social, lifestyle, onboarding) | Rounded corners, brand color, illustrations | 200–300 ms, gentle springs, light overshoot, playful secondary actions |
| Playful / expressive (games, kids, creative tools) | Bold color, big type, characters | Springs with visible bounce, squash and stretch, exaggeration |
| Premium / editorial (luxury, portfolio, marketing) | Lots of whitespace, serif type, imagery | Slower 300–500 ms, long smooth ease-out, fades and parallax-lite, no bounce |

When unsure, choose the calmer profile.

### 2. Plan, then confirm

Produce a short plan of the specific places to animate, grouped by priority (see the checklist below), with the file for each. For a large app, confirm scope with the user before editing. For a small app, or when the user said "just do it", proceed.

### 3. Add the motion foundation once

Create or extend a single set of tokens in whichever place the project already keeps tokens (CSS variables, Tailwind `theme.extend`, a theme file):

```css
:root {
  --dur-instant: 100ms;  /* press, toggle */
  --dur-fast: 150ms;     /* hover, focus, tooltip */
  --dur-base: 250ms;     /* dropdown, popover, small enter/exit */
  --dur-slow: 400ms;     /* modal, page, layout change; never above 500ms */
  --ease-out: cubic-bezier(0.22, 1, 0.36, 1);    /* entering */
  --ease-in: cubic-bezier(0.55, 0, 1, 0.45);     /* exiting */
  --ease-in-out: cubic-bezier(0.65, 0, 0.35, 1); /* moving on screen */
  --ease-spring: linear(0, 0.36 9%, 0.82 22%, 1.03 34%, 1.06 42%, 1.01 58%, 0.99 74%, 1);
  interpolate-size: allow-keywords;
}

@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
    scroll-behavior: auto !important;
  }
}
```

Adjust the values to the chosen motion profile and any existing house values.

### 4. Apply the checklist

Go through it in priority order. Skip anything the app doesn't have.

### 5. Verify

- Run the app and use the changed flows (use the browser preview if available). Check that nothing jumps, flickers or delays input.
- Toggle reduced motion (emulate `prefers-reduced-motion: reduce`) and confirm content still appears and works.
- Check theme switching does not trigger a transition storm.
- Report a short list of what was animated and where, and what was deliberately left still.

## Checklist (priority order)

### P1: Feedback on direct input (highest value, lowest risk)

- **Press scale.** Buttons and tappable cards scale to 0.97 on press, about 100 ms. Never scale below 0.95.
  ```css
  .btn { transition: transform var(--dur-instant) var(--ease-out); }
  .btn:active { transform: scale(0.97); }
  ```
- **Hover and focus.** Color, background and shadow transitions at `--dur-fast`. Focus rings appear instantly and are never animated away.
- **Bigger hit areas.** Small icons get an invisible extended target (Fitts's law) without changing visuals:
  ```css
  .icon-btn { position: relative; }
  .icon-btn::after { content: ""; position: absolute; inset: -8px; }
  ```
- **Toggles, checkboxes, radios.** Knob slides with ease-in-out; the check draws or scales in from 0.97.
- **Error shake.** A wrong password or invalid input shakes as well as turning red, so it is noticed and isn't color-only. Pair it with a text message.
  ```css
  @keyframes shake { 0%,100%{transform:translateX(0)} 20%,60%{transform:translateX(-6px)} 40%,80%{transform:translateX(6px)} }
  .shake { animation: shake 300ms var(--ease-in-out); }
  ```
  Re-trigger by removing the class, forcing reflow (`el.offsetWidth`), and re-adding it.
- **Success confirmation.** Saves, copies and likes get a brief secondary action (check mark, a small pop of 1 → 1.15 → 1, a count tick). Keep it under 400 ms.

### P2: Elements entering and leaving

- **Enter from 0.97, never from 0.** Popovers, dropdowns, menus, dialogs and toasts enter with `opacity 0 → 1` plus `scale(0.97) → 1` (or a 4–8 px translate), ease-out.
- **Exit faster than enter**, with ease-in, about 75% of the enter duration.
- **Transform origin.** Popovers and menus scale from their trigger (`transform-origin` at the anchor side), which shows where they came from.
- **Use `@starting-style`** for CSS-only enter animations where supported, and `transition-behavior: allow-discrete` to animate `display` and `overlay` for dialogs and popovers.
- **Modals.** The backdrop fades; the panel rises 8–16 px and fades in. Mobile sheets slide from the bottom.
- **Toasts.** Slide in from their screen edge and stack with follow-through.

### P3: Navigation and structure

- **Stepper / wizard / carousel steps.** Slide in the direction of travel (forward enters from the right, back from the left). Never just crossfade, because direction tells users where they are.
- **Tabs.** The active indicator slides between tabs (shared layout / FLIP); content crossfades quickly.
- **Accordions.** Animate `height` to `auto` with `interpolate-size: allow-keywords` (already on `:root`) or `::details-content`. Fallback for browsers without it: the grid trick.
  ```css
  .acc-body { display: grid; grid-template-rows: 0fr; transition: grid-template-rows var(--dur-base) var(--ease-in-out); }
  .acc[open] .acc-body, .acc.open .acc-body { grid-template-rows: 1fr; }
  .acc-body > * { overflow: hidden; }
  ```
  Rotate the chevron with it.
- **Page and route transitions.** Use the View Transitions API (`document.startViewTransition`, or the framework's integration) for a short crossfade or shared-element morph. Keep it under 300 ms and skip it when reduced motion is on.
- **Lists.** New items fade and slide in; removed items collapse. Stagger only on first load, 20–40 ms per item, capped at about 8 items.

### P4: Perceived performance (Doherty threshold, under 400 ms)

- **Optimistic UI.** Update the UI immediately and reconcile later.
- **Skeletons** with a subtle shimmer instead of spinners for content-shaped loads. Use a linear spinner only for indeterminate waits.
- **Delay spinners** about 300 ms so fast responses never flash a loader.
- **Tooltip delay grouping.** The first tooltip waits about 500 ms; once one has shown, the next ones appear instantly while the pointer keeps moving (reset after about 300 ms idle).

### P5: Gestures and physics (only where the app already has drag or swipe)

- **Springs instead of fixed durations** for anything flicked or dragged, so release velocity carries through. Use the installed library's spring, or `--ease-spring` for CSS.
- **Rubber-band resistance** when dragging past an edge, then spring back:
  ```js
  const rubber = (overflow, dim, c = 0.55) => (overflow * dim * c) / (dim + c * overflow);
  ```
- **Follow through.** Dragged items settle with slight overshoot; trailing parts lag a frame or two.
- **Arcs.** Elements that travel far (add-to-cart fly, a FAB morph) follow a curve, not a straight line.

### P6: Delight (sparingly, and only if the personality allows)

- Squash and stretch on a landing or bounce (playful profile only).
- Anticipation: a small wind-up before a big action, such as a send icon dipping back before flying.
- Exaggerated feedback on celebratory moments: first completion, a streak, a purchase.
- Depth: layered shadows that grow on lift and hover (solid drawing).

## Hard technical rules

- **Animate only `transform` and `opacity`** (plus `filter` and `clip-path` with care). Never animate `top`, `left`, `width`, `height`, `margin` or `padding` for movement. Accordion `height` via `interpolate-size` is the one accepted exception.
- **Durations:** micro 100–200 ms, small UI 200–300 ms, layout or page 300–500 ms. Functional UI never exceeds 500 ms.
- **Easing:** ease-out to enter, ease-in to exit, ease-in-out for on-screen moves, springs for gestures. `linear` only for loops, spinners and progress.
- **Theme switching:** disable transitions during the switch to avoid a transition storm:
  ```js
  document.documentElement.classList.add('no-transitions');
  applyTheme();
  requestAnimationFrame(() => requestAnimationFrame(() =>
    document.documentElement.classList.remove('no-transitions')));
  ```
  ```css
  .no-transitions * { transition: none !important; }
  ```
- **Never `transition: all`.** List the properties.
- **`will-change`** only on elements about to animate, never globally.
- **Reduced motion is mandatory.** Replace movement with an opacity fade or nothing; keep state changes visible.
- **Frequency of use:** the more often an action happens (typing, tab switching, a command palette, list navigation), the less it should move. Near-instant is correct for power-user paths.
- **No layout shift.** Reserve space before content animates in.
- **Interruptible.** Hover and toggle animations must reverse cleanly mid-flight. Prefer CSS transitions or springs over fixed keyframes for these.

## UX laws to respect while animating

- **Fitts's law:** big, close targets; extend hit areas.
- **Hick's law:** don't add animated choices or distractions; motion reduces decisions, not adds to them.
- **Miller's law:** stagger and group reveals in chunks, not 30 items at once.
- **Doherty threshold:** acknowledge every input within 100 ms and finish feedback within 400 ms.
- **Postel's law:** forgiving input (gestures with generous thresholds, snapping) and tidy, predictable output motion.
- **Jakob's law:** use the motion conventions users already know from the platform (iOS sheet, Android ripple, web dropdown). Don't invent new metaphors.

## Framework notes

- **Tailwind:** add the tokens in `theme.extend` (`transitionDuration`, `transitionTimingFunction`, `keyframes`, `animation`) and use `active:scale-[0.97]`, `motion-reduce:` variants.
- **React / Next.js:** if Motion (Framer Motion) is installed, use `AnimatePresence`, `layout`, `layoutId` and `useReducedMotion`. Otherwise use CSS classes and the View Transitions API.
- **Vue:** `<Transition>` and `<TransitionGroup>` with CSS classes.
- **Svelte:** built-in `transition:`, `animate:flip` and `svelte/motion` springs.
- **Angular:** `@angular/animations` or CSS, matching what's already used.
- **React Native:** Reanimated if installed, else `Animated` with `useNativeDriver: true`, and `AccessibilityInfo.isReduceMotionEnabled`.
- **Flutter:** implicit animations (`AnimatedContainer`, `AnimatedSwitcher`), `Curves.easeOutCubic`, and `MediaQuery.disableAnimations` for reduced motion.

## What not to do

- Don't restyle, recolor or re-layout anything.
- Don't animate on every scroll, or add parallax to functional screens.
- Don't add bounce to a serious or professional app.
- Don't animate text that people are reading, or anything that moves while they're typing.
- Don't loop attention-grabbing animations (except a clearly needed loading state).
- Don't add a motion library for something CSS can do.
