# postui-top

paideia-os task viewer -- a `postui` reference app proving the widget
library across a `Table` + `Gauge` + `Sparkline` + `Tabs` mix plus the
`TaskRow@0.1` semantic-pipe emit path.

## Status

**v1.0.0 -- release closer landed (2026-09-07).** postui-top now ships
the full M1..M4 wave: userspace scaffold, poll loop, table/gauge/
sparkline/tabs view stack, and TaskRow@0.1 semantic-pipe emitter.
`paideia-os/postui` v1.0.0 (tag `postui-v1.0.0`) is the linked-in
widget library; `paideia-as >= 0.34.0` is the required toolchain floor.

### Kernel-body caveat (paideia-os#2352)

The `SemanticEmit` module (`src/semantic_emit.pdx`) targets SC+
sysno 115 (`sys_semantic_send`). At v1.0.0 the userspace scaffolding
is complete, but the paideia-os kernel dispatch table does NOT yet
route sysno 115 to a handler -- every invocation returns `-ENOSYS`
(`0xFFFFFFFFFFFFFFDA`). Callers of `semantic_emit_task_rows` MUST
treat `-ENOSYS` as an expected sentinel until the kernel body lands
under paideia-os#2352. The ANSI text layer (Table/Gauge/Sparkline/
Tabs) is unaffected -- top(1)-style output renders correctly with or
without the semantic-pipe consumer online.

## Features (M1..M4 landed)

| Milestone | Feature | Source |
|---|---|---|
| M1 | `sys_taskinfo` (SC+ sysno 83) poll loop, PollState + task_rows | `src/poll.pdx` |
| M2 | `ViewTable` adapter -- postui `Table` draw over task_rows | `src/view_table.pdx` |
| M3-a | `ViewGauge` -- 2x BAR gauge (CPU + memory activity) | `src/view_gauge.pdx` |
| M3-b | `ViewSparkline` -- 64-slot ring history | `src/view_sparkline.pdx` |
| M3-c | `ViewTabs` -- 3-tab [All|User|Kernel] view switch | `src/view_tabs.pdx` |
| M4 | `SemanticEmit` -- TaskRow@0.1 record emit via sysno 115 | `src/semantic_emit.pdx` |

## Data source

`sys_taskinfo` (SC+ sysno 83, frozen at R57.M4-003 + R60.M7-002 in
paideia-os), polled on a fixed 16.7ms-per-"tick" interval driven by
`sys_clock_read_ns` (SC+ sysno 66). See `src/poll.pdx` §Tick
semantics for the ns-to-tick conversion rationale.

## Semantic-pipe output

`SemanticEmit::semantic_emit_task_rows(rows_ptr, count)` repacks the
first `min(count, 4)` task_rows into the 56-byte `TaskRow@0.1` wire
shape and dispatches one `sys_semantic_send` (sysno 115) call per
record under the `TASK_ROW_SCHEMA_HASH` placeholder
`0x7A5CD0CE00010001` (see `src/semantic_emit.pdx` §SCHEMA HASH
PLACEHOLDER for the M5 BLAKE3 crossover plan). At v1.0.0 this
returns `-ENOSYS` pending paideia-os#2352.

## Layout

    manifest.pdxproj            # paideia-as build manifest (kind = tool)
    caps.decl                   # KIND_TUI_CANVAS + KIND_TTY + KIND_MEMORY
                                # requires; TaskRow@0.1 output schema
    tools/build.sh              # per-repo build script (mirrors postui's)
    src/poll.pdx                # M1: PollState + poll_init + poll_tick
    src/view_table.pdx          # M2: ViewTable adapter
    src/view_gauge.pdx          # M3: ViewGauge (2x BAR gauge)
    src/view_sparkline.pdx      # M3: ViewSparkline (64-slot ring)
    src/view_tabs.pdx           # M3: ViewTabs (3-tab switch)
    src/semantic_emit.pdx       # M4: SemanticEmit (sysno 115 emitter)
    tests/boot_top_smoke.pdx    # M4: placeholder witness (returns 0)

## Build

    bash tools/build.sh

Compiles every source under `src/` and `tests/` into loose ELF64
objects under `build-out/`. Requires `paideia-as >= 0.34.0` (the
version-floor check refuses fast otherwise).

## Run

At v1.0.0 the downstream link + boot into a live paideia-os target is
tracked separately -- `bash tools/build.sh` produces the loose-object
set; the tarball assembly (`postui-top-v1.0.0.tar.gz`) is handled by
the release pipeline. Once linked and launched as `/bin/top`, the
program polls `sys_taskinfo` on a 16.7ms tick and renders the
top(1)-style view; press `Tab` to switch view [All|User|Kernel] once
the input-dispatch consumer lands.

## Release

Dual-signed tarball (`postui-top-v1.0.0.tar.gz`) per the
`release:` block in `manifest.pdxproj`:

- Primary: ML-DSA-65 (post-quantum), signer
  `snunez+postui-top-author@paideia-os.dev`.
- Secondary: Ed25519 (classical), root signer
  `paideia-os-release-root@paideia-os.dev`.
- Mirror target: `pkgs.paideia-os`.

`pkg install postui-top` requires BOTH signatures to verify.

## License

MIT -- see LICENSE.
