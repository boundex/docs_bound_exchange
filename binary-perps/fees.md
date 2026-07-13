# Fees

Binary Perps are executed using perpetual futures on Hyperliquid and are therefore subject to the same trading costs as any other perpetual position.

In addition, each order includes a **Builder Fee**, which compensates Bound for constructing and facilitating the Binary Perp.

***

### Trading Fees

Every Binary Perp is executed on Hyperliquid.

As a result, the standard Hyperliquid trading fees apply to:

* Opening a position
* Closing a position
* Early cash-outs

Trading fees are determined by your Hyperliquid fee tier.

For the latest fee schedule, please refer to the Hyperliquid documentation.

***

### Funding

Funding payments apply while a Binary Perp remains open.

Depending on market conditions and your position direction, funding may either increase or decrease your final return.

Funding is collected and distributed by Hyperliquid. Bound does not receive any funding payments.

***

### Builder Fee

Every Binary Perp order includes a **Builder Fee**.

The Builder Fee is a native Hyperliquid mechanism that allows applications such as Bound to receive a small fee when orders are executed through their platform.

The fee is attached to every order submitted by Bound, including:

* Opening a position
* Closing a position
* Early cash-outs

**Current Builder Fee:** **0.05%**

***

### Total Cost

The total cost of a Binary Perp may include:

* Hyperliquid trading fees
* Hyperliquid funding payments (if applicable)
* Bound Builder Fee

These costs are reflected in the final settlement of the position.

***

### Frequently Asked Questions

#### What is the Builder Fee?

The Builder Fee is Hyperliquid's native mechanism for compensating third-party applications that facilitate trading. It is automatically included on orders submitted through Bound.

#### Does Bound charge any additional fees?

No. Bound only receives the Builder Fee attached to orders through Hyperliquid's Builder Fee mechanism.

#### Does a cash-out incur fees?

Yes. A cash-out closes the underlying perpetual position and is subject to the same applicable trading fees and Builder Fee as any other position close.
