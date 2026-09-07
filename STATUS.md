# postui-top -- status

**Wave:** postui v1 reference apps
**Current milestone:** M1-001 landed (2026-09-07)
**Version:** 0.1.0-pre

See [`paideia-os/postui`'s `docs/design.md`](https://github.com/paideia-os/postui/blob/main/docs/design.md)
§3.1 and §5.2 for the full spec and milestone/issue breakdown.

## Milestones

| Milestone | Scope | Status |
|---|---|---|
| M1-001 | Scaffold + `sys_taskinfo` poll loop | **landed 2026-09-07** |
| M1 rest | (remaining M1 substrate) | open, not started |
| M2 | Table render + Gauge (CPU/mem aggregate) | open, not started |
| M3 | Sparkline history + Tabs view switch | open, not started |
| M4 | `TaskRow@0.1` semantic-pipe emission + release | open, not started |

## M1-001 landing details

- `manifest.pdxproj` (new): satellite-app manifest, `kind = tool`,
  `sources: [src/poll.pdx]`. Entry point (`Main::main`) is a forward
  declaration -- lands at M1-005.
- `caps.decl` (new): `KIND_TUI_CANVAS(mint, present)` +
  `KIND_TTY(write, read)` + `KIND_MEMORY(mint, revoke)`; declares
  `TaskRow@0.1` output schema; enumerates SC+ sysnos 66 + 83 in an
  informational `syscalls:` block.
- `tools/build.sh` (new, `chmod +x`): mirrors `postui/tools/build.sh`
  (paideia-as resolution chain, per-src compile to `build-out/`).
  `paideia-as >= 0.34.0` required.
- `src/poll.pdx` (new): `module Poll`. Exports `POLL_STATE_BYTES = 256`
  (task_rows_ptr / task_row_count / task_row_cap /
  poll_interval_ticks / last_poll_tick / 216B reserved),
  `POLL_ROW_BYTES = 48` (pid / state / ticks_cpu / mem_kb / name_ptr /
  name_len), `poll_init(state, rows, cap, interval)`, `poll_tick(state)
  -> 0|1`. Refresh path transcodes the kernel's 64-byte record into
  the postui-top 48-byte task_row form. Error band
  `0xFFFFEC00..0xFFFFEC0F` (POSTUI_TOP_ERR_BAND) reserved; sentinels
  at +0..+3 declared but not raised at M1-001 (defensive gates deferred
  to M1-002).
- `README.md` + `STATUS.md`: updated.

**Build status:** not run (softarch does not invoke `tools/build.sh`
per paideia-os autonomous-loop discipline; main runs the build and
re-invokes the softarch on failure).

## Dependencies

- `paideia-os/postui` v1.0.0 (tag `postui-v1.0.0`) -- link-time
  consumer at M2 (Table + Gauge widget draws).
- `paideia-os/postui` M5 (semantic-pipe conformance) before this
  repo's M4.
- `paideia-as >= 0.34.0` at compile time (per `manifest.pdxproj` +
  `tools/build.sh` version floor).

## License

MIT -- see LICENSE.
