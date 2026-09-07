# postui-top

paideia-os task viewer -- a `postui` reference app proving the library
across a `Table` + `Gauge` + `Sparkline` + `Tabs` widget mix.

## Status

**M1-001 landed (2026-09-07).** Scaffold + `sys_taskinfo` (SC+ sysno
83) poll loop populating in-memory task rows. Dependencies:
`paideia-os/postui` v1.0.0 (tag postui-v1.0.0) is the link-time
consumer starting at M2. See the authoritative design at
[`paideia-os/postui`'s `docs/design.md`](https://github.com/paideia-os/postui/blob/main/docs/design.md)
§3.1 for widget mix, data source, semantic records, and milestone
breakdown; see `STATUS.md` here for the rollup.

## Data source

`sys_taskinfo` (SC+ sysno 83, frozen at R57.M4-003 + R60.M7-002 in
paideia-os), polled on a fixed 16.7ms-per-"tick" interval driven by
`sys_clock_read_ns` (SC+ sysno 66). See `src/poll.pdx` §Tick
semantics for the ns-to-tick conversion rationale.

## Layout

    manifest.pdxproj         # paideia-as build manifest (kind = tool)
    caps.decl                # KIND_TUI_CANVAS + KIND_TTY + KIND_MEMORY
                             # requires; TaskRow@0.1 output schema
    tools/build.sh           # per-repo build script (mirrors postui's)
    src/poll.pdx             # M1-001: PollState + poll_init + poll_tick

## License

MIT -- see LICENSE.
