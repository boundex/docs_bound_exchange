# FAQ

## What are Barrier Perps?

Barrier Perps are fixed-payout derivatives whose result depends on a defined price-barrier event. Different products can define different barrier structures and outcomes.

## What are Vanilla Barrier Perps?

Vanilla Barrier Perps are the first product in the Barrier Perps family. You select an upper and lower price barrier, then choose which one will be reached first.

## What is my maximum loss?

Your maximum loss on a position is its USDC stake. The protocol fee is paid separately when the position opens.

## Is the payout fixed?

The payout is locked when the position opens and becomes the book's recorded liability if the position wins. Indicative quotes can change before acceptance.

In an extreme insolvency where book assets cannot cover liabilities, a last-resort socialized-loss mechanism may proportionally reduce the amount ultimately paid to affected users.

## Does payout mean profit?

No. Payout is the total amount received after a win and includes the returned stake.

```
Gross profit = payout − stake
```

Fees should also be considered when calculating the net result.

## Which price determines a barrier crossing?

Vanilla Barrier Perps use the underlying asset's HyperCore mark price. The upper barrier crosses when the mark reaches or exceeds it; the lower crosses when the mark reaches or falls below it.

## Can I hold multiple positions on the same asset?

Yes, subject to stake limits, available book capacity, and active safety restrictions.

## Can I change my barriers after opening?

No. The barriers, chosen outcome, stake, and payout are fixed when the position opens.

## Can I close a position early?

You may request a buyback quote while the position is active. Accepting the quote closes the position for the displayed amount. If you do not accept, the position remains active.

## How do I receive a winning payout?

After the position settles as won, its recorded payout becomes claimable to the position owner under normal solvency conditions. Claims are permissionless, but the recipient cannot be changed by the caller. An insolvency restriction or socialized-loss adjustment may delay or reduce payment.

## What happens if a book becomes insolvent?

The solvency breaker stops new positions and initially permits only buybacks that reduce liabilities. Claims are blocked while the book attempts to recover through buybacks or additional capital.

If a shortfall remains, it is allocated across affected positions according to their share of total position value. This can reduce winning claims or early-close amounts and prevents claim order from determining who absorbs the loss.

## Does Bound open a perpetual position in my account?

No. The Barrier Perp is held through the Bound contract. Bound manages and hedges the combined protocol book on Hyperliquid.

## How does Bound price positions?

Pricing begins with the product's estimated outcome probability, then accounts for expected hedge costs, book exposure, and spread. See [Pricing Overview](<pricing/README (1).md>) and [Vanilla Barrier Perps Pricing](pricing/vanilla-barrier-perps.md).

## What happens if services are unavailable?

New quotes, hedging, settlement, early closing, or claims may be temporarily delayed. Stored mark readings can allow observed outcomes to settle after normal operation resumes.

## Are claims and settlement controlled only by Bound?

No. They are designed to be permissionless, allowing eligible calls to be submitted without depending on one exclusive operator.

## What assets are supported?

The interface shows the assets currently enabled for each Barrier Perps product. Supported assets may differ because markets have different liquidity, volatility, leverage, and gap-risk characteristics.
