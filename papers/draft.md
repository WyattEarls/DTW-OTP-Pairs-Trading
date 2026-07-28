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

### Hedge Ratio Estimation

We estimated a time-varying hedge ratio using a Kalman filter, modeling
the hedge ratio as a random-walk latent state. The filter was
initialized and stabilized on Train-period data, then updated
continuously through the Validation and Test periods without
re-estimation, preserving out-of-sample integrity.


