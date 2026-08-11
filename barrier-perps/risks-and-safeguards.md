# Risks & Safeguards

Barrier Perps have defined position-level risk, but they still depend on smart contracts, market infrastructure, liquidity, and successful hedging. Review these risks before opening a position.

## Position Risk

Your maximum position loss is the stake committed when the position opens. If the non-chosen outcome occurs, the full stake is lost.

Closing early does not guarantee recovery of the original stake. The buyback value depends on current conditions and may be substantially lower.

## Barrier Observation Risk

Vanilla Barrier Perps settle using stored HyperCore mark readings. A very brief crossing that occurs and reverses between observable readings may not be captured.

Gaps can also move the mark across a barrier between readings. The stored observation sequence, rather than an assumed continuous path, determines the onchain result.

## Market and Hedge Risk

The protocol dynamically hedges its net exposure on Hyperliquid. It can experience:

* Hedge execution slippage
* Funding costs
* Delayed rebalancing
* Rapid changes when positions resolve
* Price gaps while an underlying market is closed
* Differences between expected and realized hedge performance

The liquidity buffer absorbs this variance under normal conditions.

## Infrastructure Risk

Barrier Perps depend on HyperEVM, HyperCore market data, Hyperliquid execution, and the Bound contracts. Outages or congestion can temporarily prevent:

* New quotes or position openings
* Hedge adjustments
* Settlement
* Early-close quotes
* Claims or other transfers

Stored observations can allow previously observed outcomes to settle after service resumes.

## Smart Contract Risk

Smart contracts can contain defects or behave unexpectedly. Book isolation and open-interest caps limit the exposure concentrated in one contract instance, but they do not eliminate smart-contract risk.

## Protocol Safeguards

The protocol is designed with several safeguards:

* **Book capacity limits:** Cap the amount of open stake held by one book.
* **Book isolation:** Prevent one book's assets and liabilities from mixing with another's.
* **Liquidity buffer:** Provides capital beyond user stakes to support hedging and absorb variance.
* **Hedge price limits:** Bound how far hedge orders may execute from the mark.
* **Rebalance limits:** Divide large hedge changes and limit turnover.
* **Outflow limits:** Restrict how quickly value can leave a book.
* **Circuit breakers:** Restrict or freeze actions when leverage, solvency, price, hedge, infrastructure, or liquidity conditions breach configured thresholds.
* **Permissionless settlement and claims:** Reduce dependence on a single operator for completing position outcomes.

## Circuit-Breaker Effects

Depending on the condition, safeguards may allow only exposure-reducing actions, buybacks, settlements, claims, or liquidity deposits. Severe conditions can temporarily freeze most actions.

Restrictions are intended to protect the book, but they can delay opening, closing, settling, or claiming a position. They are not a guarantee against loss.
