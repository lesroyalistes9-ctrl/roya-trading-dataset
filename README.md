# Crypto Prop Firm Registry — Roya Trading (CC-BY 4.0)

A dated, machine-readable registry of crypto / on-chain proprietary trading firms, maintained by [Roya Trading](https://roya-trading.com/), plus the transaction-level list of every prop-firm payout Roya Trading received on its own accounts and reconciled on a public blockchain.

Snapshot in this repository: fetched **2026-09-25T12:12:43Z** from the live endpoints (see `data/READ_AT.txt`). The live endpoints are regenerated at every deploy of roya-trading.com; when they differ from the files here, the endpoints win.

| File | What it is | Live source |
| --- | --- | --- |
| `data/registry.json` | Full registry: 7 scored firms, 8 tracked-but-not-scored entities, 10 DNS/HTTP status records, append-only changelog (9 entries), headline counters | https://roya-trading.com/api/registry.json |
| `data/registry.csv` | Flat mirror of the 7 scored firms (11 columns) | https://roya-trading.com/api/registry.csv |
| `data/registry.schema.json` | JSON Schema (draft-07) of `registry.json` | https://roya-trading.com/api/registry.schema.json |
| `data/payouts.json` | 54 payout transactions received by Roya Trading's own accounts (48 Propr.xyz on Ethereum, 6 Hypernova on Arbitrum), each with its tx hash, plus 10 dated aggregate entries | https://roya-trading.com/api/payouts.json |
| `data/payouts.csv` | Flat mirror of the 54 transactions (12 columns) | https://roya-trading.com/api/payouts.csv |

Human-readable pages behind the data: [status registry / graveyard](https://roya-trading.com/prop-firm-graveyard/), [detection methodology](https://roya-trading.com/prop-firm-graveyard/methodology/), [changelog](https://roya-trading.com/prop-firm-graveyard/changelog/), [scoring methodology](https://roya-trading.com/how-we-test-prop-firms/), [verified payouts](https://roya-trading.com/payouts/), [llms.txt](https://roya-trading.com/llms.txt).

## Headline figures in this snapshot (each with its read date)

- Registry version 1.3, `generatedAt` 2026-09-25, Trust Score grid last reviewed 2026-09-10, statuses last verified 2026-09-25.
- 7 scored firms (Propr.xyz 86.7, Hypernova 85.7, Carrot Funding 73.2, Solana Funded 69.8, Breakout 69.5, HyperPNL 64.5, GT Funded 18.3 — Trust Score /100, `reviewReadIso` 2026-09-05 to 2026-09-11 per firm). 4 of the 7 were bought and traded by Roya Trading (`boughtByUs: true`).
- 8 entities tracked without a score (DojiFunded, PropMarket, PolyFunded, Vanta Trading, Hyperstack, DecentralProp, FUNDED by Foxify, FundedPoly), each with a `whyNotScored` and dated facts (last checked 2026-09-15).
- Counter: 1 of 10 tracked firms gone dark since 2026-06 (`asOf` 2026-09-25).
- Verified payouts: 54 transactions reconciled on-chain (`asOf` 2026-09-19): 48 Propr.xyz payouts on Ethereum totalling 7,670.94 USDC (2026-05-20 to 2026-09-18) and 6 Hypernova payouts on Arbitrum totalling 1,932.09 USDC (2026-08-29 to 2026-09-01), all at an 80 % split.
- Hypernova payout reserve: $604,038.65 read on Arbitrum at block 508755865 on 2026-09-25.

## Fields of `registry.json` (from `registry.schema.json`)

Top level:

| Field | Type | Meaning |
| --- | --- | --- |
| `$schema` | uri | URL of the JSON Schema |
| `version` | string | Registry format version |
| `name` | string | Dataset name |
| `license` | uri | License URL — CC-BY 4.0 |
| `attribution` | string | How to attribute reuse |
| `generatedAt` | date | Build date; the file is regenerated at every deploy |
| `gridLastReviewed` | date | Date the Trust Score grid was last revised |
| `statusLastVerified` | date | Date the status registry (DNS/HTTP) was last verified |
| `source` | uri | Human-readable page of the status registry |
| `methodology` | uri | How firms are tested and scored |
| `detectionMethodology` | uri | How a firm is declared dark |
| `changelogUrl` | uri | Registry changelog page |
| `counter` | object | `goneDarkSince`, `goneDark`, `tracked`, `asOf`, `permalink` — headline number with a permanent anchor |
| `verifiedPayouts` | object | `asOf`, `reconciledTotal`, `propr{count,usdc,chain,firstIso,lastIso}`, `hypernova{…,splitPct}`, `methodology`, `dataset`, `datasetCsv` |
| `hypernovaReserve` | object | `usd`, `iso`, `chain`, `block`, `tracker` — latest dated on-chain reading of Hypernova's payout reserve |
| `firms[]` | array | Every scored firm (see below) |
| `trackedNotScored[]` | array | `name`, `slug`, `url`, `officialUrl`, `status`, `statusLabel`, `lastCheckedIso`, `whyNotScored`, `facts[]` |
| `statusRecords[]` | array | `name`, `domain`, `status`, `dns`, `http`, `note`, `checked` — DNS/HTTP status per tracked firm |
| `changelog[]` | array | `dateIso`, `title`, `body`, `proof[]` — append-only entries |

`firms[]` items: `canonicalName`, `slug`, `aliases[]`, `officialUrl`, `rails`, `status`, `trustScore` (number /100), `onChainTier` (integer or null), `traded` (boolean), `identifier` (object `{label, address, chain, explorer}` or null — a firm's *published* contract/payout address, never a private wallet), `review` (uri), `reviewReadIso` (date), `priceFrom` (string), `payoutsVerifiedByUs` (integer), `boughtByUs` (boolean), `cap` (nullable).

`registry.csv` columns: `canonicalName, slug, status, rails, officialUrl, trustScore, onChainTier, traded, review, priceFrom, reviewReadIso`.

## Fields of `payouts.json` / `payouts.csv`

Top level of `payouts.json`: `version`, `name`, `license`, `attribution`, `generatedAt`, `readIso`, `method`, `source`, `counts{reconciledTotal, propr, hypernova}`, `totals{proprReceivedUsdc, hypernovaReceivedUsdc}`, `transactions[]` (54), `entries[]` (10 dated aggregate claims with their own `method` and `anchor`).

`transactions[]` / CSV columns: `firm, slug, dateIso, blockTimeUtc, requestedUsd, receivedUsdc, splitPct, chain, txHash, explorerUrl, method, readIso` (JSON adds `account`, a label of the receiving account, never a wallet address).

`method` is `onchain-match` for every transaction: the receipt was fetched from a public RPC (status OK) and a USDC `Transfer` log whose amount equals the dashboard's received figure to the cent was found. The hash is the proof; the RPC is the check.

## How figures are verified

- **Statuses**: every tracked domain gets a DNS resolution, an HTTP status and a manual read of the live page, each check dated. One failed check marks a firm *at risk*, never dead; reclassification requires the signal to persist across two dated checks at least 7 days apart (published rules: https://roya-trading.com/prop-firm-graveyard/methodology/). A verdict names the exact domain checked. No verdict is ever edited: corrections are appended to the changelog (two public corrections so far, 2026-08-15 and 2026-09-15).
- **Payouts**: only transfers to Roya Trading's own accounts, each matched on-chain to the cent; counts are set by a human after reconciliation, never by a script.
- **Hypernova reserve**: USDC balance of the published vault + reserve wallet on Arbitrum, read daily at a stated block.
- **Trust Scores**: public grid (https://roya-trading.com/how-we-test-prop-firms/), sub-scores recomputable; scores move only on a dated grid revision, by hand.
- Affiliate disclosure: Propr.xyz, Hypernova and Carrot Funding pay Roya Trading an affiliate commission on sign-ups through its links (https://roya-trading.com/about/). Scores and statuses are not for sale; this dataset contains no affiliate links.

## Update cadence (from `docs/daily-refresh.md` of the site)

| Automatic (daily job `npm run refresh:daily`, then a build + deploy) | Never automatic (human, dated) |
| --- | --- |
| Hypernova reserve (USDC on Arbitrum, vault + wallet) | Trust Scores, grid weights, `tested`, verdicts |
| Registry DNS + HTTP and the check date | A graveyard `status` (active / at-risk / dead / relaunching…) |
| Offer presence checks | The grid review date, any prose or note |
| | `reconciledTotal` (receipts, reconciled by hand) |

A failed automated read writes nothing new: the previous value stays with its own date. The JSON/CSV endpoints are regenerated at every deploy (`generatedAt`). This GitHub copy is a snapshot; a sync recipe is in `.github/workflows/README-refresh.md`.

## License and attribution

**CC-BY 4.0** — https://creativecommons.org/licenses/by/4.0/ (full text in `LICENSE`).

Attribution line to use: **CC-BY 4.0 — cite "Roya Trading" with a link to https://roya-trading.com/** (for the payouts file: link to https://roya-trading.com/payouts/).

## Citation

APA:

> Roya Trading. (2026). *Crypto Prop Firm Registry and Verified Payouts (dataset, version 1.3, snapshot 2026-09-25)* [Data set]. https://roya-trading.com/api/registry.json

BibTeX:

```bibtex
@misc{royatrading2026registry,
  author       = {{Roya Trading}},
  title        = {Crypto Prop Firm Registry and Verified Payouts (dataset)},
  year         = {2026},
  version      = {1.3},
  note         = {Snapshot of 2026-09-25; regenerated at every deploy. CC-BY 4.0.},
  doi = {10.5281/zenodo.22971098},
  howpublished = {\url{https://roya-trading.com/api/registry.json}},
  url          = {https://roya-trading.com/api/registry.json}
}
```

Zenodo DOI (v1.3, published 2026-09-26): https://doi.org/10.5281/zenodo.22971098 — concept DOI for all versions: https://doi.org/10.5281/zenodo.22971097. Hugging Face mirror: https://huggingface.co/datasets/roya0327/crypto-prop-firm-registry

## Changelog

The registry changelog is append-only and lives in `registry.json` → `changelog[]` and at https://roya-trading.com/prop-firm-graveyard/changelog/ (RSS available from that page). Format changes of the file itself are listed in the site's field-by-field specification (`docs/registry-json.md`, v1.0 → v1.2 on 2026-09-15; the live file reports v1.3).

## What this dataset is not

- Not investment advice and not a recommendation to buy any evaluation.
- Not a claim about firms that are not in it: an absent firm is simply not tracked.
- Statuses are dated readings of public surfaces; a firm that is "active" today can be "at risk" tomorrow, which is exactly why every row carries a date.
