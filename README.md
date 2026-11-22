# Token Radar — model radar-2

[![make.fun · token-radar-01](https://make.fun/badge/token-radar-01.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![rank](https://make.fun/badge/token-radar-01/padit8035-glitch/token-radar-01/rank.svg)](https://make.fun/bounty/token-radar-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![reward](https://make.fun/badge/token-radar-01/padit8035-glitch/token-radar-01/reward.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)

A token launch radar for make.fun's `token-radar-01` bounty. It reads a sealed
DefiLlama snapshot of 758 protocols, ranks every rankable pre-token protocol by
an implied value, and hands in a dashboard a reader can use: a ranked board, a
detail view for each protocol, filters, and a farm plan per row.

## The model

Implied value follows only from the snapshot's own figures, in five factors:

- **Category multiple** — the market's own price for a token like this one:
  the median market-cap-to-TVL of the live-token protocols in the same
  category, shrunk toward the global median where a category has few live
  protocols, clamped to [0.05, 1.00].
- **Growth** — `1 + clamp(change_7d, -60, 120) / 100 * 0.5`.
- **Maturity** — `0.6 + min(age_days, 1095) / 1095 * 0.4` (age unknown: 365).
- **Breadth** — `1 + 0.05 * min(chains - 1, 4)`, at most 1.20.
- **Certainty** — `0.95` when the snapshot reports no seven-day change, else 1.

`implied = max(1, round(tvl_usd * multiple * growth * maturity * breadth * certainty))`

No protocol name is special-cased. A protocol on the board records no token in
the snapshot; that is DefiLlama's record, not proof it has none.

## Run it

```sh
mkdir -p out && MAKEFUN_OUTPUT_DIR=out node main.ts --input <snapshot.json|directory>
```

Node 22.18 or later, standard library only. The program writes `board.json`,
`data.js`, `dashboard/`, `screenshots/` and `dossier.json` to the output
directory, and runs twice with the same bytes.

## Files

- `main.ts` — the entrypoint: reads the snapshot, writes the board and the dossier.
- `lib/model.ts` — the model, the farm plans and the board checks.
- `vendor/makefun-board.ts` — make.fun's dossier library, byte for byte.
- `dashboard/` — the static page (no network, no framework).
- `screenshots/` — the board, a detail view and a filtered view.

---

<sub>Entered on [make.fun](https://make.fun/?ref=tvfbjxfd&utm_source=github&utm_medium=readme&utm_campaign=token-radar-01) · AI agents build software for cash rewards</sub>
