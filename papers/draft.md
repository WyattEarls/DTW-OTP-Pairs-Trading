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

### Trading Rule

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

## Results

<img width="2985" height="1186" alt="ocp_dendrogam_sp500" src="https://github.com/user-attachments/assets/027d819b-2e87-4bd9-9f23-6dd48e35dfc0" />
<b>Figure 1. OCP hierarchical clustering dendrogram, S&P 500 universe, Train period only (2014-2017).</b>




OPC clustering produced clearly lopsided results, with cluster 1 containing 205 stocks and cluster 2 containing 151 stocks at k=2. This pattern held at higher k values as well. At k=4, the two largest clusters contained 129 and 123 stocks respectively, while the two smaller clusters contained 76 and 28 stocks. At k=7, the 76-stock cluster from k=4 persisted unchanged, while the remaining stocks fragmented further, including one cluster of 96 stocks.

This lopsidedness follows from the mechanics of OCP's causality constraint. A pair receives a small OCP distance only if it exhibits a genuinely stable directional relationship, since the constraint penalizes any true alignment that requires matching a later observation in one series to an earlier observation in the other. Most pairs of large-cap equities lack a consistent lead-lag relationship over time, so they receive similarly inflated distances and cluster together into one large, undifferentiated group. The smaller, minority clusters are disproportionately composed of pairs with a stable, directionally consistent relationship, which Ward linkage separates out as distinct from the broader majority.


<img width="2985" height="1186" alt="dtw_dendrogram_sp500" src="https://github.com/user-attachments/assets/ed0eaebf-216d-4c92-b431-ddcfad9107e8" />
<b>Figure 2. DTW hierarchical clustering dendrogram, S&P 500 universe, Train period only (2014-2017).</b>



DTW clustering also produced uneven cluster sizes. At k=2, cluster 1 contained 140 stocks and cluster 2 contained 216 stocks, a ratio of roughly 1.5 to 1. At k=4, the clusters contained 140, 123, 66 and 27 stocks. At k=7, cluster sizes ranged from 17 to 74 stocks. Unlike OCP, DTW imposes no causality constraint, so it groups stocks based on overall co-movement in their Sharpe ratio series regardless of which stock's movement leads the other's. The resulting imbalance appears comparable in magnitude to that observed under OCP, suggesting cluster size asymmetry in this dataset is not primarily driven by the causality constraint itself, but by the underlying structure of Sharpe ratio similarity across the S&P 500 universe more broadly.



<img width="2685" height="1036" alt="sp500_test_period_results" src="https://github.com/user-attachments/assets/80559756-22d2-44e0-b71e-3ef7557f422a" />
<b>Figure 3a. DTW vs. OCP: Cumulative PnL (active pairs during Test Period only)</b>

<b>Figure 3b. DTW vs. OCP: Average Sharpe (active pairs during Test Period only)</b>



Figure 3a shows the rebased cumulative profit and loss for the 10 candidate pairs that generated at least one trade during the Test period, with each pair's cumulative value reset to zero at the start of Test to isolate performance within the out-of-sample window. All 10 active pairs ended the period with positive cumulative PnL. The two strongest performers, BIIB/KSS and ALLE/ICE, moved in sharp, discrete jumps rather than gradual reversion, consistent with a small number of large repricing events driving most of their profit rather than continuous mean-reverting behavior. This pattern should temper confidence in the magnitude of their individual Sharpe ratios, since a strategy's risk-adjusted return is less reliably estimated from a handful of large moves than from many smaller ones.

Figure 3b reports average Sharpe ratio among active pairs only, grouped by discovery method and by tier. Pairs found exclusively by DTW show a higher average Sharpe (8.17) than pairs found by both DTW and OCP (2.99), and Tier 2 pairs show a marginally higher average Sharpe (5.09) than Tier 1 pairs (4.31). These comparisons should be read cautiously given the underlying sample sizes: only 3 pairs were DTW-only and active, against 7 DTW+OCP pairs, and the DTW-only average is disproportionately influenced by a single pair (LH/WAT, Sharpe 11.95 over 4 trades). With such small active-pair counts, these group averages are suggestive of a pattern rather than statistically conclusive evidence that one discovery method or tier outperforms another.

### Limitations

### Discussion

### Literature Review






