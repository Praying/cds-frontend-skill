---
name: cds-frontend
description: Design, implement, or refactor frontend UI in the Claude Design System (CDS) style, meaning Claude Desktop's visual language, tokens, motion physics and interaction guards. Use when building or restyling chat/composer/sidebar/agent-workstation UI, picking tokens, radii, density or motion, or auditing a UI against CDS. Triggers - "Claude style UI", "CDS", "composer", "spring/motion preset", "refactor to design tokens".
---

# CDS Frontend (design / implement / refactor)

Source of truth is the upstream repo https://github.com/Caho1/open-claude-ui (MIT): `AGENTS.md` (tokens, presets, components), `code-examples/` and `docs/v2/` (Claude Desktop 2.0, v2.37337.1). Paths below are relative to that repo; clone or open it alongside your project, or read them on GitHub. This skill is the workflow; it does not duplicate the values. Read `references/v2-delta.md` when the task touches v2 behaviour (density, sidebar, effort, compositor motion, offline queue).

## 0. Pick the baseline first
- **CDS 1.x** (default in `code-examples/`): warm clay accent, 20px composer, 8px radius, Framer Motion springs.
- **CDS 2.0** (docs/v2): cold `#FCFCFB` canvas, 12/14px composer, 6/8px radius, 24/32px controls, CSS-compositor motion.
Ask or infer which one; do not mix them within one surface. Many v2 tokens (`--cds-h-control`, `data-density`) are documented in docs but not yet defined in `code-examples/cds-theme.css`: define them before use.

## 1. Workflow
1. **Classify** the task: new component / restyle / refactor / audit.
2. **Reuse before writing**: check `code-examples/` (30 modules, index in AGENTS.md §3) and `src/index.ts`. Compose or extend; do not re-derive.
3. **Tokens**: every color, radius, duration and easing comes from a `--cds-*` var or Tailwind alias. No raw hex, no ad-hoc `ms`.
4. **Motion**: choose by the table below.
5. **Edge guards** (section 3) for any input, scroll or iframe code.
6. **Verify** (section 4). Report failures verbatim.

## 2. Decision rules
**Motion choice**
| Situation | Use |
|---|---|
| Hover/press/fade/skeleton/entrance, anything that runs during streaming | Pure CSS + `--cds-dur-*` + `--cds-ease-*` (compositor-only: `transform`, `opacity`, `filter`) |
| Drag release, width/height that depends on velocity or layout | Framer Motion with one of `SPRING_BOUNCY / SNAPPY / GENTLE / HEAVY_SNAP` |
| Reduced motion | Snap, never remove state change. Honor `prefers-reduced-motion` |
Never `transition: all`, never `linear`, never hard-coded spring numbers. A `transition` shorthand using `var(--cds-dur-*)` silently dies if the token is undefined, so define all five (60ms/120/200/300/450).

**Surface and borders**: layer with `surface-0..3` + hairline `border-subtle` (0.5px in v2). One stroke only: controls inside a bordered container get `border-none`. Derive shadows with `color-mix()` from the surface, not fixed alpha black. Never pure `#000`/`#fff` (code block `#0d0d0c` excepted).

**Density**: size controls from `--cds-h-control` / `--cds-radius` / `--cds-icon`, never fixed px, so Compact and Comfortable both work. Compact rows that fall under 44px hit-area use the negative-margin touch grow (`--cmp-chin-touch-grow`).

**Numbers**: any timer, counter, token gauge, line number gets `tabular-nums`.

**Information design (v2 stance)**: a workstation, not a chat toy. Prefer dense status rows (28px, branch, PR, run heartbeat) over roomy cards; surface cost/risk explicitly (effort tiers with a Max warning, usage gauge); treat user input as precious.

## 3. Non-negotiable guards
- IME: `if (e.nativeEvent.isComposing || e.keyCode === 229) return;` before any Enter-to-send.
- Streaming scroll: do not auto-pin if `Date.now() - lastUserScroll < 150`; pin only within 40px of bottom.
- Iframes for model-generated HTML: sandbox without `allow-same-origin`, origin-checked postMessage, gated by `navigator.userActivation`.
- Zero-loss input: failed/offline sends persist (IndexedDB queue) and restore on reload.
- Environment immutability: context window / worktree / MCP config are frozen once a session is created; don't build UI that edits them mid-session.
- No binary assets (`.ttf/.otf/.mp3/.wav`); system font stacks and Web Audio synthesis only.
- Both light (`:root`) and dark (`.dark`) must be checked. No cold purple/neon gradients (1.x).
- Keyboard: lists/grids use roving focus (`useClaudeRovingFocus`); dialogs trap focus.

## 4. Refactor / audit checklist
Grep the target for: hex literals, `rounded-[…px]`, `transition-all`, `ease-linear`, `duration-[…]`, `stiffness:` outside presets, `onKeyDown` with Enter lacking IME guard, `scrollTo`/`scrollTop =` in streaming code, `allow-same-origin`, 1px borders nested in bordered parents. Replace each with the token/preset/guard. Keep behaviour; change only presentation unless asked.

Then run, all must exit 0:
```
npm run typecheck && npm run check:tokens && npm run build
```
If you added a `--cds-*` reference to AGENTS.md, `check:tokens` requires it to exist in `cds-theme.css`.

## 5. Output contract
State which baseline (1.x/2.0), which existing modules were reused, which tokens/presets were chosen and why, and the verification result. Mark anything not verified (e.g. visual dark-mode check) as unverified.
