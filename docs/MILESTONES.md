# omp-flow — Milestone Ledger

This fork rebases the omp-flow methodology onto the Trellis framework
(upstream `Andyduck-ops/Trellis` v0.6.6 @ `bde902c`). Framework = platform /
release vehicle; omp-flow = methodology product. See `COPYRIGHT` for the
upstream attribution that MUST be preserved.

## M1 — Claude-first omp-flow on the fork  ✅ DELIVERED

Branch `m1-claude-rebase`, 7 commits on `bde902c`:

| Commit | Row | Delivered |
|--------|-----|-----------|
| `e32c45b` | B | Full mechanical rebrand `trellis`→`omp-flow` across `src/**` (paths, call sites, env vars, markers) |
| `005f8d3` | C | Migration-manifest + release-tooling reset |
| `a4e386e` | D | Removed pull-prelude machinery; emptied platform registries |
| `1cb46c6` | E | Claude configurator deploy wiring on dir-walk symmetry + M1 platform gate (non-claude flags park with exit 1) |
| `5b5f9ad` | F | Replaced the Trellis methodology templates with the omp-flow Python control plane (13 py, workflow.md 12-block, 12 skills, 5 agents, 5 hooks, settings.json) |
| `638fb7d` | G | Test-suite disposition (keep/rewrite/delete/park) + ported the fixture-driven parity suite |
| `a01c1f0` | I | Rebrand of the M1-active deploy-time content templates (config.yaml framework-only, agents.md markers, guides) |

**Acceptance (verified):** `pnpm build` green · `pnpm --dir packages/cli exec
vitest run test/omp-flow` = 14/14 parity green · row H final integration gate
PASS (independent reviewer adversarially reproduced AC3/AC4/AC6/AC10/AC12/AC13
+ F5 on the deploy surface; `init --claude` deploys a byte-identical toolchain,
`update` reports 0 drift, doctor ok).

**Baseline note (correction):** M1's row G reduced cli failures 46→7 but did not
reach 0; the remaining residuals were git-subprocess parallel-load timeout flakes
plus 2 cross-platform test bugs (now fixed by M2 row F-001). The honest
Windows-host baseline is: the seq lock flake + git-subprocess parallel-load
timeout flakes (all pass in isolation).

## M2 — deferred findings cleanup  ✅ DELIVERED

M2 delivered on branch `m1-claude-rebase`, 6 rows, commits `0be6e89`..`ad9e5e6`.
Each M2 row resolves (or formally accepts) one M1-deferred finding.

| Commit | Row | Finding | Resolution |
|--------|-----|---------|------------|
| `0be6e89` | A-001 | #2 statusline | RESOLVED — rebrand + control-plane API drift fix + opt-in deploy restored |
| `9ba1d56` | B-001 | #4 template-fetcher | RESOLVED — hardcoded `mindfold-ai/marketplace` default excised |
| `0cc259c` | C-001 | #3 AC7 fixture | RESOLVED — expected-manifest fixture + test locks the deploy surface |
| `5121a88` | D-001 | #5 cosmetic | RESOLVED — `mkdtemp` prefix renamed to `omp-flow-core-test-` |
| `ad9e5e6` | F-001 | NEW cross-platform | RESOLVED — POSIX-hardcoded tests made platform-aware |
| — | E-A001B001C001D001--001 | #1 seq flake | ACCEPTED + documented (unchanged; no code masking) |

- **Finding #2 statusline (A-001, `0be6e89`)** — RESOLVED. Rebranded
  `statusline.py` trellis→omp-flow AND fixed the control-plane API drift (was
  calling the old `resolve_active_task(..., platform=)` / `active.task_path` API
  outside any guard → would crash on an active task once rebranded; now uses
  `resolve_active_task(repo, payload)` + `active.task_id`, wrapped fail-open).
  Restored the `--with-statusline`-gated deploy with `settings.statusLine`
  byte-parity to `preserveExistingClaudeStatusLine`; statusline stays opt-in
  (excluded from `getClaudeHooks`, not `update`-tracked). Executing smoke test
  proves no crash.

- **Finding #4 template-fetcher (B-001, `9ba1d56`)** — RESOLVED. Excised the
  hardcoded `mindfold-ai/marketplace` default (`TEMPLATE_INDEX_URL` deleted);
  `fetchTemplateIndex` no-ops to `[]` when no source; non-native workflow id with
  no source silently falls back to native; explicit
  `--registry`/`--workflow-source` unchanged.

- **Finding #3 AC7 fixture (C-001, `0cc259c`)** — RESOLVED. Added
  `packages/cli/test/omp-flow/fixtures/expected-manifest.json` (seeded from a real
  `init --claude` run) + `manifest.test.ts` locking the default deploy surface
  (statusline excluded, opt-in).

- **Finding #5 cosmetic (D-001, `5121a88`)** — RESOLVED. Renamed the
  `trellis-core-test-` mkdtemp prefix to `omp-flow-core-test-`.

- **NEW — cross-platform test portability (F-001, `ad9e5e6`)** — RESOLVED
  (discovered during M2 integration verify). Two inherited tests were
  POSIX-hardcoded though the production code was already cross-platform-correct:
  `upgrade.test.ts` expected `npm`/`which omp-flow` but win32 emits
  `cmd.exe …`/`where omp-flow`; `mem-helpers.test.ts` expected a raw POSIX cwd
  though `mem.ts` uses `path.resolve`. Fixed the TESTS to derive expectations from
  `process.platform` / `path.resolve` (test-only; production untouched).
  **Durable lesson: cross-platform Node test assertions must derive expected
  paths/commands from `process.platform` / `path.resolve` / `path.join`, never
  hardcode one OS's form.**

- **Finding #1 seq flake (E-A001B001C001D001--001)** — ACCEPTED + documented
  (unchanged). Inherited Windows lock-contention flake in `seq.test.ts >
  "strictly monotonic seqs under concurrent appends"`; `lock.ts` byte-identical to
  `bde902c`. Not masked. A future upstream sync could adopt backoff+jitter (à la
  `proper-lockfile`); out of scope here.

**M2 acceptance (verified by the E integration row + independent review):**
`pnpm build` green; full suite 1052 tests, 0 failures attributable to any
M2-edited file; the only failures are inherited git-subprocess/parallel-load
timeout flakes (`template-fetcher.test.ts`,
`init-uninstall-overdelete.integration.test.ts` — both pass in isolation) + the
seq lock flake. AC-FROZEN intact (LICENSE unchanged; `Copyright (C) 2026 Mindfold
LLC` preserved; linear history; no upstream push).

## M3 — Codex harness adapter  ✅ DELIVERED

M3 delivered on branch `m1-claude-rebase`, 6 rows, commits `1174a0d`..`ca99ce3`.
It brings the Codex harness to full 5-agent parity — entirely TS-side
unpark/rebrand/wire with **ZERO control-plane (Python) change**.

| Commit | Row | Delivered |
|--------|-----|-----------|
| `1174a0d` | A-001 | Restore the pull-based prelude injector in `shared.ts` (rebranded `buildPullBasedPrelude` / `injectPullBasedPreludeToml` / `applyPullBasedPreludeToml` + `detectAgentRole` mapping the 5 agent names, qbd→null); pure addition, Claude path untouched |
| `a81d6f4` | B-001 | Rebrand + expand codex agent tomls 3→5 (`omp-flow-{research,architect,implement,check,qbd}`), prelude-free bodies, `multi_agent`/`multi_agent_v2` disabled (Codex #240/#241 deadlock fix preserved) |
| `7b358ac` | C-001 | Rebrand codex templates (`.trellis`→`.omp-flow` across config.toml/hooks.json/hooks/skills) + fix `templates/codex/index.ts:85` `codex-skills`→`skills` dir bug so `getAllCodexSkills()` returns 13; pin Codex 0.129+ (`features.hooks`) |
| `1e1cd89` | D-A001B001--001 | Wire configurator: `applyPullBasedPreludeToml()` at both init (`codex.ts`) and collect/update (`index.ts`) call sites for byte-identical 0-drift; hard-wire sub-agent dispatch (no `dispatch_mode` knob); un-gate `init --codex` (`shippedPlatforms`/`shippedCliFlags` add `"codex"`); freeze-only pull documented |
| `04a5227` | E-A001B001C001D001--001 | Un-park + re-derive codex tests (`test/templates/codex.test.ts`) + a fixture-only pull-handshake suite (`test/omp-flow-codex/`) proving the deployed prelude's `omp_flow.py context` returns freeze-checked context on a frozen row and DENIES on wrong-status/unfrozen rows |
| `ca99ce3` | F-...--002 (amend-003) | Restore the codex `inject-workflow-state.py` deploy: ship it as a codex template hook (deploys via `getAllHooks` like `session-start.py`, 0-drift), flip `SHARED_HOOKS_BY_PLATFORM.codex`→`[]`; closes a dangling `hooks.json` ref that left the codex router hook non-functional |

The F-...--001 integration verify is verification-only (no commit).

- **Codex = PULL / class-2 platform (A-001, B-001, D-001).** Codex has no
  `PreToolUse(Agent)`/`CollabAgentSpawn`, so sub-agents PULL their role/row-bound
  context via a prelude embedded in their `.toml` `developer_instructions` →
  `omp_flow.py context --role <r> --task <t> [--row <row>]`. Executor≠reviewer
  independence is preserved **structurally by sub-agent dispatch** (inline mode
  rejected — main session doing both implement AND check breaks independence).
  Both deep pre-M3 uncertainties collapsed to no-new-Python: (a) the 4 pull roles
  (research/architect/executor/reviewer) reuse the EXISTING `omp_flow.py context`
  entrypoint, which runs the SAME `verify_row_frozen` the Claude PUSH path uses →
  identical freeze/status guarantees; (b) QbD reuses the gate prepare output +
  gate inspect/decide — no new `codex-qbd-report` kind was needed (the brainstorm
  over-scoped it).

- **Two accepted, human-ratified guarantee reductions vs Claude (NOT defects).**
  (D2) the codex context pull is **FREEZE-ONLY** — it inherits
  `verify_row_frozen`/status but not the push-path session/active-task
  cross-check (codex has no session id; Main supplies binding by passing explicit
  `task`+`row` ids). (D4) QbD write protection is **CONSENT-BASED** — codex has no
  `PreToolUse(Write)` equivalent of `claude-protect-write`, but gate integrity is
  rooted at inspect/decide time (`inspect_gate` recomputes the evidence digest;
  decide requires a human PASS), so a rogue auditor write cannot manufacture a
  PASS. Claude's hook write-protection is defense-in-depth, not the integrity
  source.

- **amend-003 root cause (durable, repo-wide) (F-...--002, `ca99ce3`).** M1
  deleted the `templates/shared-hooks/*.py` sources, so `getSharedHookScripts()`
  returns `[]` repo-wide → `writeSharedHooks(...)` is a no-op for EVERY platform,
  while `SHARED_HOOKS_BY_PLATFORM` still declares hooks. Codex was just the
  platform row E surfaced (its only registered hook dangled). The fix shipped
  codex's `inject-workflow-state.py` as a codex-owned template hook and flipped
  codex's allowlist to `[]`.

**M3 acceptance (verified by the E test row + F integration verify + independent
review):** `pnpm build` green; the re-derived `codex.test.ts` + fixture-only
`test/omp-flow-codex/` pull-handshake suite green (deployed prelude's
`omp_flow.py context` returns freeze-checked context on a frozen row, DENIES on
wrong-status/unfrozen rows); `init --codex` deploys the 5-agent parity set +
13 skills + the router hook chain with 0 drift on `update`. ZERO control-plane
(Python) change — TS-only unpark/rebrand/wire. The Claude PUSH path is untouched.

**Baseline note:** M3 delivers only the **codex** platform to parity. `opencode`
and the remaining adapters stay parked. Two M3 findings were dispositioned
DEFERRED by the user (see Beyond M2). The inherited seq lock flake +
git-subprocess parallel-load timeout flakes (M1/M2 baseline) are unchanged.

## Beyond M2

- Harness adapters: **Codex is now DELIVERED (M3)** at full 5-agent parity.
  `opencode` + the remaining platforms
  (gemini/qoder/copilot/cursor/kiro/droid/codebuddy/trae) stay parked with
  `parked` + milestone skip reasons in the test suite.
- **Two DEFERRED M3 findings** (dispositioned by the user, tracked for a later
  milestone):
  1. **Codex skill-prose dead-refs** — 7 `SKILL.md` files + 2 lines in
     `codex/hooks/session-start.py` still mention Trellis-era helper scripts
     `get_context.py` / `add_session.py` (no omp-flow equivalent; the omp-flow
     entrypoint is the single `omp_flow.py context`). Prose-only, self-correcting
     fallback, no exec path — deferred to a skill-prose rebrand sweep.
  2. **Latent shared-hook gap on the OTHER parked platforms** — the same
     `getSharedHookScripts() == []` root cause (M3 amend-003 lesson) means
     gemini/qoder/copilot/cursor/kiro/droid/codebuddy/trae still declare
     `SHARED_HOOKS_BY_PLATFORM` entries that would not deploy; M3 fixed it only for
     the shipped codex platform. Must be addressed when any of those platforms is
     brought up. Non-blocking cleanliness item: `configurators/index.ts:247`
     carries a now-stale comment above a dead no-op
     `collectSharedHooks(".codex/hooks","codex")` call.
- OMP extension packaging.
- npm publish (the npm name `omp-flow` has a pre-existing `0.1.5` on the registry;
  publish remains a non-goal to date).
- Potential upstream sync adopting `lock.ts` backoff+jitter (à la
  `proper-lockfile`) to retire the inherited seq lock-contention flake.
