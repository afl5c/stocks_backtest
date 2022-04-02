# stocks_backtest

Backtest stock strategies using historical data from the [NASDAQ-100][nasdaq] from 2016 to 2022.

[nasdaq]: https://en.wikipedia.org/wiki/Nasdaq-100

# How to run

## Download Axiom

You will need [Axiom][axiom], my programming language.
Axiom is a modern and simple programming language which lets you do more with less code.

[axiom]: https://aaronliu.cc/axiom

## Download Dataset

You will need to download the historical data from here:

https://aaronliu.s3.amazonaws.com/history.db (73 MB)

And put it in the same folder as this code.

## Run baseline

This baseline is used to calculate the "zero-risk" returns.

```
$ axiom backtest.axm
```

## Run strategy

There are three strategies provided:

 - **cheater** &mdash; looks into the future to pick stocks
 - **linear** &mdash; uses linear regression to pick stocks
 - **momentum** &mdash; uses "momentum" to pick stocks

To run a strategy, do:

```
$ axiom backtest.axm [strategy]
```

For example, to run the "cheater" strategy, run:

```
$ axiom backtest.axm cheater
```

# Results

The results are summarized in this table:

strategy  |sharpe
----------|------
cheater   |  1.12
linear    |  0.38
momentum  |  0.42

Basically, a momentum strategy works best. (Not counting cheater, which is unrealistic.)
However, in contrast to the vanilla strategy described on [Wikipedia][momentum],
the strategy used here looks for downward momentum, not upward momentum.
(This backtest shows that downward momentum works better than upward momentum; see code for details.)

[momentum]: https://en.wikipedia.org/wiki/Momentum_investing

# How to create your own strategies.

 - Create a file called `strategy_[name].axm`.
 - Implement a function with the signature `:strategy(date,symbols)` which returns "allocations".
 - Allocations are just an object that maps symbol to weight, where weight is an arbitrary number.
 - The after each step, the simulation will automatically rebalance the portfolio to match allocations.

For example, this strategy will devote the entire portfolio to one random stock every step:

```axiom
:strategy(date,symbols)
	index = randint(0,symbols.len()-1) #pick random index
	allocations = {}
	allocations[symbols[index]] = 1
	return allocations
```

The strategy file can `import('simulation.axm')` and use simulation.get_price() or simulation.get_prices()
in order to get price information.
See code in `strategy_linear.axm` or `strategy_momentum.axm` for examples.

Finally, you back backtest your strategy just like other strategies.
If your your file is called  `strategy_whatever.axm`, you would run:

```
$ axiom backtest.axm whatever
```
