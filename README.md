# postui-top

paideia-os task viewer — a `postui` reference app proving the library
across a `Table` + `Gauge` + `Sparkline` + `Tabs` widget mix.

## Status

**Design phase — not started.** Depends on `paideia-os/postui` M1 (skeleton)
and M2 (Table/Tabs) landing first. See the authoritative design at
[`paideia-os/postui`'s `docs/design.md`](https://github.com/paideia-os/postui/blob/main/docs/design.md)
§3.1 for widget mix, data source, semantic records, and milestone
breakdown; see `STATUS.md` here for the rollup.

## Data source

`sys_taskinfo` (syscall 83), polled on a fixed tick.

## License

MIT — see LICENSE.
