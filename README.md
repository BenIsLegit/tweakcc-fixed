# tweakcc-fixed

A **temporary fork of [tweakcc](https://github.com/Piebald-AI/tweakcc)** that bundles together several pending upstream fixes, with a few additional fixes on top, so `tweakcc` works cleanly against recent Claude Code releases (through **CC 2.1.113**) while upstream catches up.

> [!NOTE]
> This fork exists only to unblock users while the fixes below are reviewed and merged upstream. Once the relevant PRs land in [Piebald-AI/tweakcc](https://github.com/Piebald-AI/tweakcc), switch back to upstream.

For full documentation of what tweakcc is and how to use it, see the [**upstream README**](https://github.com/Piebald-AI/tweakcc#readme).

## Base

- **tweakcc version:** `4.0.11`
- **Based on upstream commit:** [`2e1d03e`](https://github.com/Piebald-AI/tweakcc/commit/2e1d03e) — *Prompts for 2.1.113* ([#692](https://github.com/Piebald-AI/tweakcc/pull/692))
- **Target Claude Code versions:** up to and including **2.1.113**

## Cherry-picked upstream PRs

All four PRs below are open (unmerged) upstream at the time of forking. Their fixes are required for tweakcc to function on current CC builds.

| PR | Author | What it fixes |
|---|---|---|
| [#601](https://github.com/Piebald-AI/tweakcc/pull/601) | [@signadou](https://github.com/signadou) | Handle `WASMagic` import errors gracefully during native installation detection |
| [#646](https://github.com/Piebald-AI/tweakcc/pull/646) | [@sla-te](https://github.com/sla-te) | Support the React Compiler output and async refactoring introduced in CC 2.1.85–2.1.88 (affects most patches) |
| [#655](https://github.com/Piebald-AI/tweakcc/pull/655) | [@LeonFedotov](https://github.com/LeonFedotov) | Fall back to npm source when Bun bytecode extraction produces non-patchable JS, and thread a `clearBytecode` flag through the content API so repack clears stale compiled code |
| [#664](https://github.com/Piebald-AI/tweakcc/pull/664) | [@mike1858](https://github.com/mike1858) | Resolves [issue #660](https://github.com/Piebald-AI/tweakcc/issues/660) — two patches broke `cli.js` due to literal `\"` sequences in prompt content |

## Additional fixes on top

These are not in any upstream PR and are unique to this fork.

- **`bcce70a` — Scope PR #664's backslash-doubling to quote contexts.**
  PR #664's pre-processing ran before every delimiter branch, including backticks. In template-literal content, `escapeDepthZeroBackticks` is already parity-aware, so blindly pre-doubling turned `` `\`find\`` `` into a prematurely-terminated template literal and caused `cli.js` to fail to load with *"Expected CommonJS module to have a function wrapper"*. This fix limits the doubling to `"` and `'` contexts only.

- **`207b57c` — Align PR #664's backtick tests with the scoped-doubling fix.**
  The two backtick-context tests encoded a *literal-text* interpretation of prompt content, which doesn't match tweakcc's storage format (markdown files hold the JS-source-escaped form extracted from `cli.js` template literals). Tests now assert the scoped behavior.

- **`3c08e0c` — Adapt patch regexes to CC 2.1.113's minified shapes.**
  Updates matchers in `opusplan1m`, `patchesAppliedIndication`, `thinkerFormat`, and `verboseProperty` so each patch still locates its target in the 2.1.113 build output.

- **`c87898c` — Don't overwrite `verbose:X` inside destructuring patterns.**
  The `verboseProperty` patch was greedy enough to match `{ verbose: localName }` in destructure targets and replace it with `verbose: true`, which is a syntax error. The fix scopes the replacement to property-value positions only.

## Installation

Install directly from this fork's Git URL until the fixes land upstream:

```bash
# npm
npm install -g git+https://github.com/BenIsLegit/tweakcc-fixed.git

# pnpm
pnpm add -g git+https://github.com/BenIsLegit/tweakcc-fixed.git
```

Then use it exactly like upstream tweakcc:

```bash
tweakcc            # interactive UI
tweakcc --apply    # apply customizations from ~/.tweakcc/config.json
```

Everything documented in the [upstream README](https://github.com/Piebald-AI/tweakcc#readme) — commands, API, config format, patch list — applies here unchanged.

## License

[MIT](./LICENSE), inherited from upstream. Copyright © 2026 [Piebald LLC](https://piebald.ai). This fork adds only the fixes described above; all core tweakcc code remains under the upstream copyright and license.
