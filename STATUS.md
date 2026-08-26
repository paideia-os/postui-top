# postui-top — status

**Wave:** postui v1 reference apps
**Current milestone:** none landed — not started
**Version:** unreleased

See [`paideia-os/postui`'s `docs/design.md`](https://github.com/paideia-os/postui/blob/main/docs/design.md)
§3.1 and §5.2 for the full spec and milestone/issue breakdown.

## Milestones

| Milestone | Scope | Status |
|---|---|---|
| M1 | Scaffold + `sys_taskinfo` poll loop | open, not started |
| M2 | Table render + Gauge (CPU/mem aggregate) | open, not started |
| M3 | Sparkline history + Tabs view switch | open, not started |
| M4 | `TaskRow@0.1` semantic-pipe emission + release | open, not started |

## Dependencies

- `paideia-os/postui` M1 (Frame/Terminal loop, Block) before this repo's
  M1 can render anything.
- `paideia-os/postui` M2 (Table, Tabs) before this repo's M2/M3.
- `paideia-os/postui` M5 (semantic-pipe conformance) before this repo's M4.

## License

MIT — see LICENSE.
