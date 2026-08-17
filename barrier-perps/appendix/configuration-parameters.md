# Configuration Parameters

This appendix tracks the protocol parameters described by the current specification. Values marked provisional or TBD are not launch commitments.

Every configurable value should have an onchain hard bound, a timelock, or both. A parameter change does not alter an already accepted position.

## Product and Quote Parameters

| Parameter | Description | Current specification | Status |
| --- | --- | --- | --- |
| Maximum barrier distance | Per-asset cap on each barrier's distance from the mark | 10% suggested | Awaiting business decision |
| Minimum non-chosen barrier distance | Per-asset floor derived from venue leverage at zero padding | `100 / max leverage`; BTC example 2.5% at 40x | Confirmed default |
| Stake minimum and maximum | Allowed stake range per position | TBD | Awaiting business decision |
| Quote validity window | Lifetime of an opening quote | TBD | Needs engineering input |
| Buyback quote validity window | Lifetime of an early-close quote | TBD | Needs engineering input |
| Protocol fee rate | Fee charged on top of stake at opening | 0.50% proposed | Awaiting business decision |
| Early-close fee rate | Fee charged on early-close proceeds | 1% proposed | Awaiting business decision |
| Supported assets | Enabled launch underlyings | BTC, ETH, SOL, HYPE, SPCX, NVDA proposed | Confirmed in source spec |
| Leverage-padding cap | Maximum implied leverage beyond venue limit | 0 default | Needs product input |

## Pricing Parameters

| Parameter | Description | Current specification | Status |
| --- | --- | --- | --- |
| Per-asset daily volatility `sigma` | Expected-duration input | Keeper-calibrated per asset | Needs quantitative input |
| Base spread rate | Base margin applied to expected hedge notional | 0.25% proposed | Needs business and quantitative input |
| Imbalance coefficient `lambda` | Strength of signed imbalance pricing | TBD; constant at launch | Needs quantitative input |
| Capacity fraction `c` | Sets normalized imbalance capacity | 0.5 proposed | Needs quantitative input |
| Funding reserve | Expected funding over estimated position duration | Formula-defined | Needs implementation calibration |
| Execution reserve | Expected hedge fees, slippage, and latency cost | Formula-defined | Needs implementation calibration |

## Book and Liquidity Parameters

| Parameter | Description | Current specification | Status |
| --- | --- | --- | --- |
| `maxOI` | Maximum summed active stake per isolated book | $1,000,000 | Confirmed default |
| Minimum buffer | Floor below which LP withdrawals stop | TBD | Awaiting business decision |
| Outflow allowance | Maximum share of `book_assets` transferable per window | 25% per 24 hours | Confirmed |
| `max_book_leverage` | Book hedge-leverage ceiling | 7x proposed | Needs engineering input |
| `solvency_multiplier` | Minimum assets relative to marked liabilities | 1.5 proposed | Needs quantitative input |
| Shortfall handling | Last-resort allocation when book assets cannot cover liabilities | Pro-rata socialized loss based on affected position value | Confirmed design |
| Non-positive NAV recapitalization | LP share issuance when pre-deposit NAV is zero or negative | Explicit recapitalization rule required | Needs implementation confirmation |

## Hedge Parameters

| Parameter | Description | Current specification | Status |
| --- | --- | --- | --- |
| `max_hedge_slippage` | Maximum hedge limit-order distance from mark | TBD; below base spread rate | Needs engineering input |
| `max_rebalance_tranche` | Maximum notional in one hedge order | TBD | Needs engineering input |
| `max_hedge_turnover` | Maximum hedge notional traded per window | TBD | Needs quantitative input |
| Hedge deviation band `B` | Allowed target-to-actual delta deviation | TBD | Needs engineering input |
| Persistence window `N` | Time deviation may remain outside the band | TBD | Needs engineering input |

## Settlement and Breaker Parameters

| Parameter | Description | Current specification | Status |
| --- | --- | --- | --- |
| Settlement cadence | Frequency of keeper settlement attempts | TBD | Needs engineering input |
| Mark-worker publish cadence | Frequency of stored mark readings | TBD | Needs engineering input |
| Missed-publish threshold `X` | Publish intervals allowed before staleness breaker | TBD | Needs engineering input |
| Mark-jump threshold and window | Mark shock that triggers Freeze | 40% within 60 seconds proposed | Needs engineering input |
| Buffer-drawdown limit and window | NAV-per-share decline that restricts outflows | TBD | Awaiting business decision |
| Clean-check count `M` | Consecutive clean checks required to resume | TBD | Needs engineering input |
| Breaker resume levels | Recovery thresholds below or above trip boundaries | TBD | Needs engineering input |

{% hint style="warning" %}
Parameters labeled proposed or TBD may change before launch. The deployed contract and product interface are authoritative for enabled assets, fees, limits, and active risk settings.
{% endhint %}
