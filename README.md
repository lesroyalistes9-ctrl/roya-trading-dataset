# Crypto Prop Firm Registry — Roya Trading (CC-BY 4.0)

A dated, machine-readable registry of crypto / on-chain proprietary trading firms, maintained by [Roya Trading](https://roya-trading.com/), plus the transaction-level list of every prop-firm payout Roya Trading received on its own accounts and reconciled on a public blockchain.

Snapshot in this repository: generated **2026-11-01** (registry format v1.5) from the same data modules the site renders (see `data/READ_AT.txt`). The live endpoints are regenerated at every deploy of roya-trading.com; when they differ from the files here, the endpoints win.

| File | What it is | Live source |
| --- | --- | --- |
| `data/registry.json` | Full registry: 11 scored firms (with `rank` and six sub-scores), 8 tracked-but-not-scored entities, 14 DNS/HTTP status records, append-only changelog (10 entries), the scoring grid, headline counters | https://roya-trading.com/api/registry.json |
| `data/registry.csv` | Flat mirror of the 11 scored firms (18 columns) | https://roya-trading.com/api/registry.csv |
| `data/registry.schema.json` | JSON Schema (draft-07) of `registry.json` | https://roya-trading.com/api/registry.schema.json |
| `data/payouts.json` | 102 on-chain payout transfers received by Roya Trading's own accounts, each with its tx hash, plus the off-chain (Rise) receipts they settle and 24 dated aggregate entries | https://roya-trading.com/api/payouts.json |
| `data/payouts.csv` | Flat mirror of the 102 transfers (12 columns) | https://roya-trading.com/api/payouts.csv |

Human-readable pages behind the data: [status registry / graveyard](https://roya-trading.com/prop-firm-graveyard/), [detection methodology](https://roya-trading.com/prop-firm-graveyard/methodology/), [changelog](https://roya-trading.com/prop-firm-graveyard/changelog/), [scoring methodology](https://roya-trading.com/how-we-test-prop-firms/), [verified payouts](https://roya-trading.com/payouts/), [llms.txt](https://roya-trading.com/llms.txt).

## Headline figures in this snapshot (each with its read date)

- Registry version 1.5, `generatedAt` 2026-11-01, Trust Score grid last reviewed 2026-10-29 (six criteria, Roya's note the one subjective criterion), statuses last verified 2026-11-01.
- 11 scored firms, in rank order (Propr.xyz 85.8, Hypernova 83.4, Bungee Funded Perps 78.6, MyFundedPerps 75.7, Vest Markets 73.0, Cashed 71.8, Solana Funded 64.2, Carrot Funding 64.1, Breakout 62.0, HyperPNL 56.3, GT Funded 13.3 — Trust Score /100, `reviewReadIso` 2026-09-05 to 2026-11-01 per firm). 6 of the 11 were bought and traded by Roya Trading (`boughtByUs: true`).
- 8 entities tracked without a score (DojiFunded, PropMarket, PolyFunded, Vanta Trading, Hyperstack, DecentralProp, FUNDED by Foxify, FundedPoly), each with a `whyNotScored` and dated facts (last checked 2026-09-15).
- Counter: 1 of 14 tracked firms gone dark since 2026-06 (`asOf` 2026-11-01).
- Verified payouts (`asOf` 2026-11-01): **104 payouts received** from 4 firms, **26,941.72 USDC received on-chain** in 102 reconciled transfers — 81 Propr.xyz payouts on Ethereum (16,503.52 USDC, 2026-05-20 to 2026-10-30), 11 Hypernova payouts on Arbitrum (3,660.86 USDC, 2026-08-28 to 2026-09-01), 1 Vest Markets payout on Arbitrum (11.26 USDC, 2026-10-11), 11 MyFundedPerps payouts credited on Rise (6,775.08 USD) and withdrawn on-chain as 6,766.08 USDC on Arbitrum in 9 transfers. Per-transaction amounts in `payouts.json` are exact (6 decimals); totals are rounded to the cent.
- Hypernova payout reserve: $803,744.37 read on Arbitrum at block 520188496 on 2026-11-01.

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
| `grid` | object | `revisedIso`, `criteria[]` (`key`, `label`, `weight`, `scale`, `subjective`), `formula`, `subjectiveNote`, `deskResearchCap` — recompute any `trustScore` from `subScores` |
| `counter` | object | `goneDarkSince`, `goneDark`, `tracked`, `asOf`, `permalink` — headline number with a permanent anchor |
| `verifiedPayouts` | object | `asOf`, `reconciledTotal` (on-chain transfers), `receivedTotal` (payouts, any rail), `receivedUsdcOnchain` (v1.5, to the cent), `payingFirms`, `propr`/`hypernova`/`vestMarkets` `{count,usdc,chain,firstIso,lastIso[,splitPct]}`, `myfundedperps{…}`, `methodology`, `dataset`, `datasetCsv` |
| `hypernovaReserve` | object | `usd`, `iso`, `chain`, `block`, `tracker` — latest dated on-chain reading of Hypernova's payout reserve |
| `firms[]` | array | Every scored firm (see below) |
| `trackedNotScored[]` | array | `name`, `slug`, `url`, `officialUrl`, `status`, `statusLabel`, `lastCheckedIso`, `whyNotScored`, `facts[]` |
| `statusRecords[]` | array | `name`, `domain`, `status`, `dns`, `http`, `note`, `checked` — DNS/HTTP status per tracked firm |
| `changelog[]` | array | `dateIso`, `title`, `body`, `proof[]` — append-only entries |

`firms[]` items (in rank order): `canonicalName`, `slug`, `aliases[]`, `officialUrl`, `rails`, `status`, `trustScore` (number /100), `rank` (integer, v1.5), `subScores` (object, v1.4), `onChainTier` (integer or null), `traded` (boolean), `identifier` (object `{label, address, chain, explorer}` or null — a firm's *published* contract/payout address, never a private wallet), `review` (uri), `reviewReadIso` (date), `priceFrom` (string), `payoutsVerifiedByUs` (integer, on-chain transfers), `payoutsReceivedByUs` (integer, any rail), `boughtByUs` (boolean), `cap` (nullable).

`registry.csv` columns: `canonicalName, slug, status, rails, officialUrl, trustScore, onChainTier, traded, review, priceFrom, reviewReadIso, sub_rules, sub_payouts, sub_survival, sub_freedom, sub_value, sub_roya, rank` (`sub_*` = the six sub-scores; `trustScore` printed with one decimal).

## Fields of `payouts.json` / `payouts.csv`

Top level of `payouts.json`: `version`, `name`, `license`, `attribution`, `generatedAt`, `readIso`, `method`, `source`, `counts{…}`, `totals{…}` (aggregates, to the cent), `transactions[]` (102, exact amounts), `offChainReceipts[]` (11 Rise credits, each pointing to the transfer that settled it), `entries[]` (24 dated aggregate claims with their own `method` and `anchor`).

`transactions[]` / CSV columns: `firm, slug, dateIso, blockTimeUtc, requestedUsd, receivedUsdc, splitPct, chain, txHash, explorerUrl, method, readIso` (JSON adds `account`, a label of the receiving account, never a wallet address).

`method` is `onchain-match` for every transaction: the receipt was fetched from a public RPC (status OK) and a USDC `Transfer` log whose amount equals the dashboard's received figure to the cent was found. The hash is the proof; the RPC is the check.

## How figures are verified

- **Statuses**: every tracked domain gets a DNS resolution, an HTTP status and a manual read of the live page, each check dated. One failed check marks a firm *at risk*, never dead; reclassification requires the signal to persist across two dated checks at least 7 days apart (published rules: https://roya-trading.com/prop-firm-graveyard/methodology/). A verdict names the exact domain checked. No verdict is ever edited: corrections are appended to the changelog (two public corrections so far, 2026-08-15 and 2026-09-15).
- **Payouts**: only transfers to Roya Trading's own accounts, each matched on-chain to the cent; counts are set by a human after reconciliation, never by a script.
- **Hypernova reserve**: USDC balance of the published vault + reserve wallet on Arbitrum, read daily at a stated block.
- **Trust Scores**: public grid (https://roya-trading.com/how-we-test-prop-firms/), sub-scores recomputable; scores move only on a dated grid revision, by hand.
- Affiliate disclosure: some of the listed firms pay Roya Trading an affiliate commission on sign-ups through links published on its site (current list and terms: https://roya-trading.com/about/). Scores and statuses are not for sale; this dataset contains no affiliate links.

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

> Roya Trading. (2026). *Crypto Prop Firm Registry and Verified Payouts (dataset, version 1.5, snapshot 2026-11-01)* [Data set]. https://roya-trading.com/api/registry.json

BibTeX:

```bibtex
@misc{royatrading2026registry,
  author       = {{Roya Trading}},
  title        = {Crypto Prop Firm Registry and Verified Payouts (dataset)},
  year         = {2026},
  version      = {1.5},
  note         = {Snapshot of 2026-11-01; regenerated at every deploy. CC-BY 4.0.},
  doi = {10.5281/zenodo.22971097},
  howpublished = {\url{https://roya-trading.com/api/registry.json}},
  url          = {https://roya-trading.com/api/registry.json}
}
```

Zenodo concept DOI (resolves to the latest version): https://doi.org/10.5281/zenodo.22971097 — v1.5 (snapshot 2026-11-01): https://doi.org/10.5281/zenodo.23863863; v1.3 (snapshot 2026-09-25): https://doi.org/10.5281/zenodo.22971098. Hugging Face mirror: https://huggingface.co/datasets/roya0327/crypto-prop-firm-registry

## Changelog

The registry changelog is append-only and lives in `registry.json` → `changelog[]` and at https://roya-trading.com/prop-firm-graveyard/changelog/ (RSS available from that page). Format changes of the file itself are listed in the site's field-by-field specification (`docs/registry-json.md`): v1.3 (2026-09-25) payouts dataset; v1.4 (2026-10-16) scoring grid + sub-scores, six criteria; v1.5 (2026-11-01) per-firm `rank`, `receivedUsdcOnchain`, totals to the cent. This snapshot is v1.5.

## What this dataset is not

- Not investment advice and not a recommendation to buy any evaluation.
- Not a claim about firms that are not in it: an absent firm is simply not tracked.
- Statuses are dated readings of public surfaces; a firm that is "active" today can be "at risk" tomorrow, which is exactly why every row carries a date.
