---
name: frontend-ui
description: Use this skill when designing UI components, building chat or streaming interfaces, making accessibility decisions, or working with design systems.
---

# Frontend UI — Component Design and Chat/Streaming Patterns

## When to activate

Load this skill when:
- Designing or reviewing React/Vue/Svelte component architecture
- Building a chat interface or message threading UI
- Implementing streaming response display (e.g., token-by-token output from an LLM)
- Making accessibility decisions (keyboard nav, ARIA, focus management)
- Working with a design system or component library
- Deciding whether state should be local, lifted, or in a store

## Key questions to ask before implementing

1. Is this state local (component only) or shared (multiple consumers)?
2. Does the UI need to update in real time / stream data?
3. What is the error recovery path if the request fails or the stream drops?
4. Does this component need to be keyboard-navigable and screen-reader accessible?
5. Is this controlled or uncontrolled? (Who owns the state?)

## Component architecture patterns

- **Composition over inheritance** — build small, focused primitives and compose them.
- **Controlled vs uncontrolled** — prefer controlled components (state owned by parent) for forms and interactive elements; use uncontrolled only when integrating with non-React code.
- **Co-locate state with the component that owns it** — lift only when two siblings genuinely share the state.
- **Single responsibility** — a component should have one reason to change.
- **Avoid prop drilling** — use context or a store when props need to cross more than 2 levels.

## Chat and streaming UI patterns

### Message threading
- Assign stable keys to messages (use message ID, not array index).
- Maintain scroll position: auto-scroll to the bottom when a new message arrives, but only if the user is already near the bottom — user scroll overrides auto-scroll.
- Virtualize long message lists (use `react-window` or equivalent for 100+ messages).

### Optimistic updates
- Show the user's message immediately on send, before the server confirms receipt.
- Assign a temporary ID; replace with the server-assigned ID on confirmation.
- On failure, mark the message with an error state and offer a retry action.

### Streaming token display
- Append tokens incrementally to the current assistant message node.
- Avoid re-rendering the entire message list on each token — update only the active message.
- Avoid layout thrashing: don't resize containers or trigger reflows on each token append.
- Use a cursor/typing indicator while the stream is active; remove it when the stream closes.

### Loading and typing indicators
- Show a typing indicator (animated dots or skeleton) immediately after the user sends.
- Remove the indicator when the first token arrives (transition to stream display).
- Handle the case where the stream opens but then stalls (timeout + error state).

### Error states and retry UX
- Display inline errors close to the failed message, not as page-level toasts.
- Offer a retry button on failed messages.
- On stream failure mid-response, preserve what was received and mark the message as incomplete.

## Accessibility checklist

- [ ] All interactive elements are keyboard-reachable (Tab order is logical).
- [ ] Buttons and links have descriptive accessible labels (not just icons).
- [ ] Live message regions use `aria-live="polite"` (or `"assertive"` for critical alerts).
- [ ] Focus is managed on modal open/close (focus trapping in modals, restore on close).
- [ ] Color contrast meets WCAG AA (4.5:1 for normal text, 3:1 for large text).
- [ ] Form inputs have associated `<label>` elements (not just placeholder text).
- [ ] Error messages are associated with their input via `aria-describedby`.
- [ ] Streaming content areas are marked as live regions so screen readers announce updates.

## State management decision tree

```
Is the state used by only one component?
  → YES: useState (local)
Is it used by siblings or cousins?
  → YES: lift to nearest common ancestor
Is it used across many unrelated components?
  → YES: context or a store (Zustand, Redux, Jotai)
Is it async / server-derived?
  → YES: react-query, SWR, or tRPC query
```
