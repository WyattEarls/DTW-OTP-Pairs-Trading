# Comparison Between DTW and OCP Clustering on the S&P 500

## Methodology

### Universe Construction

We constructed a survivorship-bias-free universe of S&P 500 constituents 
as of January 1, 2014, using historical index membership data
(fja05680/sp500). This universe was narrowed to 356 stocks with 
continuous, non-flatlined price history through 2023, after excluding
tickers with incomplete data or extended zero-variance price stretches 
indicating delisting. We disclose that Yahoo Finance does not serve 
historical price data for tickers delisted as of the analysis date, 
regardless of the requested date range, which constraints our universe 
to constituents whose historical data remains accessible rather than
the full 2014 roster.

### Sharpe Ratio Computation

For each stock, we computed a 52-week rolling Sharpe ratio from weekly
log returns, using a risk-free rate of 4.5 percent annualized.

### Distance Metrics

We compared two distance metrics for hierarchical clustering. Dynamic
Time Warping (DTW) computes the minimum-cost alignment between two 
time series without restriction on the direction of temporal matching.
Optimal Causal Path (OCP) imposes a causality constraint, restricting the 
alignment such that later observations in the other. For each pair, we
computed OCP distance in both directions and took the minimum, since
the causality constraint is direction-dependent.

### Clustering and Cointegration Testing

We applied Ward-linkage hierarchical clustering at k = 2, 4 and 7 for 
both distance metrics. Within each cluster, we tested all intra-cluster
pairs for cointegration using the Engle-Granger test, applying 
Benjamini-Hochberg correction to control the false discovery rate
across the many simultaneous tests. Prior to cointegration testing, we
confirmed each series was integrated of order one via Augmented 
Dickey-Fuller testing.

### Look-Ahead Bias Correction

Before correcting for this bias, clustering and cointegration testing
were run on the full 2014-2023 sample, rather than Train-period data
only. This produced 4,993 raw-significant OCP pairs and 3,718 
raw-significant DTW pairs across all k-values, of which 89 OCP 
pair-k combinations and 44 DTW pair-k combinations survived
Benjamini-Hochberg correction. After deduplicating pairs that survived
correction at more than one k-value, this yielded 46 unique OCP pairs
(13 Tier 1, 33 Tier 2) and 21 unique DTW pairs (8 Tier 1, 13 Tier 2).

The uncorrected Test-period backtest showed an average Sharpe of 
11.0622 across OCP-only pairs and 4.2682 across pairs found by both
methods, with a large gap between the mean and median (3.0129) driven
by a single pair (CHRW/JNJ) recording a Sharpe of 181.3798 on one
trade, and early sign that these results were not statistically sound.

After restricting clustering, stationarity testing and cointegration 
testing to Train-period data only, the candidate pool changed 
substantially: 65 unique DTW pairs and 40 unique OCP pairs survived
correction, reversing which method appeared more prolific. Of the 27 
final deduplicated candidate pairs, only 10 (37%) generated any trades
in the Test period. Among these active pairs, performance remained 
comparable in magnitude to the uncorrected results (DTW-only pairs:
median Sharpe 8.1816; pairs found by both method: median Sharpe 
2.5569; all active pairs positive), indicating that the correction's
primary effect was on candidate pair selectively and trading
frequency, rather than on profitability of trades that did occur.

### Hedge Ratio Estimation

We estimated a time-varying hedge ratio using a Kalman filter, modeling
the hedge ratio as a random-walk latent state. The filter was
initialized and stabilized on Train-period data, then updated
continuously through the Validation and Test periods without
re-estimation, preserving out-of-sample integrity.

### Trading

We generated trading signals by standardizing the Kalman filter's
one-step-ahead prediction error using its own innovative variance,
producing a z-score at each time step without requiring a separately 
computed rolling window. Volatility regime was classified each week
as high or low based on whether rolling spread volatility exceeded 
1.5 times its median value over the sample. Entry thresholds were 
regime-dependent, requiring a z-score beyond 2.5 standard deviations
in high-volatility weeks and 1.5 standard deviations in low-volatility
weeks, reflecting the wider expected noise band during volatile 
periods. Positions were closed when the z-score reverted to within 
0.5 standard deviations of zero, or stopped out if it moved beyond 
3.5 standard deviations against the open position. Weeks in which 
insufficient history existed to classify volatility regime were
excluded from trading. Profit and loss for a given week was computed
using the position established as of the prior week's close, applied 
to that week's change in spread, avoiding look-ahead from using the 
same week's information to both decide and evaluate a trade. 



