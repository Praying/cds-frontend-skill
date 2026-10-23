# CDS 2.0 delta (from docs/v2, client v2.37337.1)

Provenance: static analysis of the shipped app. Details and code: `docs/v2/0*.md`. Spring call sites were verified against a later build (2.42304.2); see AGENTS.md §2.

## Design contracts
- Environment immutability: context window, worktree root and MCP config freeze at session creation (protects prompt cache).
- Zero-loss input: `ChatComposerQueuedMessages` backed by IndexedDB; offline shows an amber resend banner, restores on restart.
- Sessions group by physical execution source (This Computer / Cloud / SSH / Slack), not free-form folders. Group modes: None / Status / Date / Environment.

## 1.x -> 2.0 token changes
| Token | 1.x | 2.0 compact | 2.0 comfortable |
|---|---|---|---|
| canvas light / dark | `#FAF8F5` / `#1A1915` | `#FCFCFB` / `#151515` | same |
| `--cds-radius-composer` | 20px | 12px | 14px |
| `--cds-radius` | 8px | 6px | 8px |
| `--cds-panel-radius` | 10px | 10px | 12px |
| `--cds-h-control` | 32px | 24px | 32px |
| `--cds-h-control-nested` | n/a | 18px | 22px |
| `--cds-icon` | 20px | 16px | 20px |
| `--cds-gap-*` | 4/8/12/16/24 | 6/8/12/20/32 | 8/12/16/28/40 |
Density switch: `.cds-root[data-density=comfortable]`; all sizes are `calc(Nrem * var(--cds-rem-scale,1))`.

## Motion system
- Durations: fast 60ms (press, focus ring) / snap 120 / base 200 / sheet 300 / slow 450.
- Easings: out `cubic-bezier(.165,.84,.44,1)`, snap `(.32,.72,0,1)`, overshoot `(.34,1.3,.64,1)`.
- Why CSS-only: 80-120 tokens/s streaming saturates the main thread; JS animation drops frames. Keyframes live on the compositor thread.
- Button press: `.cds-btn-squish`, press `scale(.975)` in 60ms, release spring 450ms via a `linear()` curve (+9% overshoot). Needs the duration tokens defined.
- Stagger: `animation-delay: calc(base + min(sibling-index() - 1, 8) * 30ms)` (progressive enhancement; fall back to no stagger).
- Attachment removal: View Transitions type `cds-attachments`, exit 130ms `scale(.5)`.
- Scroll edge fade: scroll-driven animation (`cds-scroll-fade-*`), not pseudo-element overlays.
- Skeleton: `data-motion=lockstep` (2s dim to 60%, dense lists) or `wave` (90ms per-block offset + 105deg sweep, large cards).
- Starter card hover: `scaleY(1.015) scaleX(1.005)` 150ms ease-out + bottom radial glow.

## Components and naming
Use `data-cds="<Name>"` namespaces (40 in v2: Button, ButtonGroup, Card, ChatComposerChin/Dock/Editor/Notices/QueuedMessages, DataTable, FileDiff, ModelSelector, ModelSelectorEffort, Panel, Skeleton, SplitDropdownButton, Surface, TurnStatusStep, UserMessage, AssistantMessage, ...). Style through the attribute, not ad-hoc class names.
- Effort selector: `low | medium | high | xhigh | max`; Max shows the warning "May use excessive tokens..."; enterprise policy can cap `maxEffort`.
- Sidebar row: 28px, mono branch badge, PR capsule (`OPEN|MERGED|CLOSED`), running halo (`isRunning`), spinner (`starting`), bridge + `permissionMode` marks, 0.5px hairline divider.
- DataTable dimmed rows: `--cds-row-dimmed-opacity:.5`, interactive controls inside restore to 1.
- Charts/diagrams (Mermaid, Chart.js, Grid.js) are bundled locally; never load from a CDN.
- Composer chin touch grow: `margin-block: calc(var(--cmp-chin-touch-grow) * -1)` where grow = `(h-control - row-h) / 2`.
- `Pin as chapter` / `mcp__ccd_session__mark_chapter` is an internal leak; do not replicate.

## Reference code
`code-examples/ModelSelectorEffort.tsx`, `ChatComposerChin.tsx`, `CdsMotionEngine.tsx` (v2 chapter 5). Caveat: the chapter-5 inline snippet uses hex fallbacks and `animate-pulse`; prefer the repo files and tokens.
