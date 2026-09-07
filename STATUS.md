# postui-top -- status

**Wave:** postui v1 reference apps
**Current milestone:** M4-002 landed (2026-09-07) -- **v1.0.0 release closer**
**Version:** 1.0.0

See [`paideia-os/postui`'s `docs/design.md`](https://github.com/paideia-os/postui/blob/main/docs/design.md)
§3.1 and §5.2 for the full spec and milestone/issue breakdown.

## Milestones

| Milestone | Scope | Status |
|---|---|---|
| M1 | Scaffold + `sys_taskinfo` poll loop | **landed 2026-09-07** |
| M2 | Table render + Gauge (CPU/mem aggregate) | **landed** |
| M3 | Sparkline history + Tabs view switch | **landed** |
| M4-001 | `TaskRow@0.1` semantic-pipe emission | **landed** |
| M4-002 | Release closer -- v1.0.0 manifest / README / smoke witness | **landed 2026-09-07** |

## v1.0.0 release-closer landing details (M4-002)

- `manifest.pdxproj`: `version` bumped `0.1.0-pre -> 1.0.0`;
  `tests:` block populated with `tests/boot_top_smoke.pdx`; `release:`
  block comment updated to reflect M4-002 landing (dual-signed
  ML-DSA-65 + Ed25519, tarball `postui-top-v1.0.0.tar.gz`, mirror
  `pkgs.paideia-os`). Fields themselves were forward-declared at
  M1-001 and do not change here.
- `README.md`: refreshed with v1.0.0 status statement, feature list
  spanning M1..M4, build+run instructions summary, and the
  paideia-os#2352 `-ENOSYS` caveat for the semantic-pipe consumer.
- `STATUS.md`: this file -- M1..M4 marked landed.
- `tests/boot_top_smoke.pdx` (new, `module BootTopSmoke`):
  placeholder witness. `boot_top_smoke_main() -> u64` returns 0
  unconditionally. Fixture-backed smoke deferred to M4-003 (needs
  KIND_TUI_CANVAS userspace mock + live sysno 115 body).
- Tag preparation: **DO NOT commit or tag from this landing.**
  The main-side driver invokes `bash tools/build.sh` first, then
  (on clean) commits the working tree and creates the annotated tag
  `postui-top-v1.0.0`. The tarball itself is assembled by the
  release pipeline against that tag.

## Kernel-body caveat (paideia-os#2352)

The `SemanticEmit` module (`src/semantic_emit.pdx`) targets SC+
sysno 115 (`sys_semantic_send`). At v1.0.0 the userspace
scaffolding is complete, but the paideia-os kernel dispatch table
does NOT yet route sysno 115 to a handler -- every invocation
returns `-ENOSYS` (`0xFFFFFFFFFFFFFFDA`). This is a documented
v1.0.0 behavior; the semantic-pipe consumer path unlocks once
paideia-os#2352 lands, with no postui-top-side code change (the
schema-hash placeholder `0x7A5CD0CE00010001` is the only fallback,
per `src/semantic_emit.pdx` §SCHEMA HASH PLACEHOLDER).

## Dependencies

- `paideia-os/postui` v1.0.0 (tag `postui-v1.0.0`) -- link-time
  consumer (Table + Gauge + Sparkline + Tabs widget draws).
- `paideia-as >= 0.34.0` at compile time (per `manifest.pdxproj` +
  `tools/build.sh` version floor).

## Post-v1.0.0 roadmap

- M4-003: fixture-backed `boot_top_smoke.pdx` (replaces the placeholder
  witness once the KIND_TUI_CANVAS userspace mock and the sysno 115
  kernel body are both live).
- M5-001: TaskRowBatch@0.1 schema for array-shape emission (single
  syscall per tick instead of the current N).
- M5-002: input-dispatch consumer (Tab-key view switch) via
  postui's `Terminal` input path.

## License

MIT -- see LICENSE.
