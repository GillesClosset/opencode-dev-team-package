---
name: frontend-design
description: Use this skill when shaping a frontend's visual direction, refining aesthetics, or avoiding generic-looking product UI.
---

# Frontend Design — Visual Direction and Product Polish

## When to activate

Load this skill when:
- Defining the visual direction for a new frontend surface or major redesign
- Refining typography, spacing, color, contrast, or motion decisions
- Making a UI feel more distinctive, premium, or product-specific
- Reviewing whether an interface looks generic, noisy, or visually inconsistent
- Translating a product brief into concrete aesthetic guardrails before implementation

Do **not** load this skill for component architecture, state ownership, chat/streaming mechanics, or accessibility-first implementation details alone — use `frontend-ui` for those concerns.

## Key questions to ask before designing

1. What feeling should the interface create: calm, fast, technical, editorial, playful, premium?
2. Which 1-2 surfaces deserve the most visual emphasis?
3. What should users notice first, second, and third?
4. Which visual choices make this product recognizably *this* product rather than a default SaaS template?
5. Where should motion, contrast, or color be restrained so the interface stays credible?

## Design principles

- **Pick a clear point of view** — a neutral interface is acceptable only when it is intentional.
- **Design hierarchy before decoration** — emphasis should come from structure, spacing, type scale, and contrast before ornaments.
- **One accent system, not many** — limit strong accent colors, shadows, and highlight treatments.
- **Make the important path obvious** — primary actions and key content should win attention instantly.
- **Use restraint to create quality** — fewer visual ideas, applied consistently, usually look more mature.

## Core design workflow

### 1. Establish the visual thesis
- Write a one-line direction statement before changing UI details.
- Example: "Dark, calm, technical workspace with one electric accent and dense but readable information layout."
- Ensure the thesis matches the product's audience and task intensity.

### 2. Set the hierarchy
- Define the primary content region, supporting information, and low-priority chrome.
- Use spacing and scale to separate levels of importance.
- Keep navigational chrome quieter than task content.

### 3. Choose a disciplined type system
- Prefer 1-2 typefaces maximum.
- Use a deliberate scale for display, section, body, and metadata text.
- Avoid oversized headings that consume attention without adding clarity.
- Treat line-height and measure as readability tools, not afterthoughts.

### 4. Build a cohesive color system
- Use neutrals to carry most of the interface.
- Reserve saturated color for actions, status, or moments of emphasis.
- Ensure surfaces, borders, and text create clear depth without relying on excessive shadows.
- If using gradients or glows, keep them localized and purposeful.

### 5. Use motion sparingly
- Motion should explain changes in state, focus attention, or smooth transitions.
- Keep durations short and consistent.
- Avoid decorative animation that competes with task completion.
- Prefer subtle fades, position shifts, and scale changes over flashy effects.

## Anti-generic UI guardrails

- Avoid the default "AI product" recipe of purple-blue gradients, glass cards everywhere, and glowing borders on every panel.
- Avoid mixing too many visual metaphors in one surface: e.g. glassmorphism + heavy shadows + dense tables + playful illustrations.
- Avoid making every card, badge, and button equally prominent.
- Avoid decorative icons, labels, or color chips that do not improve comprehension.
- Avoid perfect symmetry when the content has a natural primary action or focal area.

## Composition checklist

- [ ] There is a clear focal point on first glance.
- [ ] Primary actions are visually stronger than secondary actions.
- [ ] Spacing creates meaningful grouping, not just even distribution.
- [ ] Text styles are limited and reused consistently.
- [ ] Accent color usage is sparse enough to stay meaningful.
- [ ] Surface treatments (borders, shadows, blur, gradients) follow a single visual logic.
- [ ] Empty states, loading states, and dense states still match the same design system.

## Working with `frontend-ui`

Use `frontend-design` first when the main risk is visual quality or product differentiation.
Use `frontend-ui` first when the main risk is component structure, state flow, accessibility, or interactive behavior.

In practice:
- `frontend-design` decides the aesthetic direction
- `frontend-ui` decides how the interface is structured and behaves

If a task needs both, apply this skill to define the visual guardrails, then load `frontend-ui` for implementation patterns.
