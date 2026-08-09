# Comparison Between DTW and OCP Clustering on the S&P 500

## Introduction

Dynamic Time Warping (DTW) addresses part of this limitation by permitting an elastic, non-linear alignment between two series rather than a fixed point-to-point comparison, and has been applied to detect switching co-movement, cluster related assets and quantify lead-lag structure across a range of financial contexts. DTW's alignment is symmetric, however: it establishes that two series co-move without establishing which series leads. The Optimal Causal Path (OCP) algorithm addresses this second limitation directly, restricting the warping path so that later observations in one series cannot be matched to earlier observations in the other, and estimating the resulting lead-lag structure alongside a measure of its stability (Stubinger, 2019).

Existing work studies these two methods largely in isolation from one another. Stubinger (2019) applies OCP as a direct pair-ranking criterion within short daily formation windows and benchmarks the resulting trading strategy against static distance measures, while the DTW literature generally uses DTW distance as an input to clustering without comparing it against a causality-constrained alternative. To our knowledge, no existing study evaluates DTW and OCP as competing distance metrics within an identical clustering and cointegration pipeline, which is the comparison this paper undertakes.

We construct a survivorship bias-free universe of S&P 500 constituents and use each distance metrics, in turn, as the basis for Ward-linkage hierarchical clustering at several values of k. Within each cluster, candidate pairs are identified via Engle-Granger cointegration testing with Benjamini-Hochberg correction for multiple comparisons, and surviving pairs are traded out of sample using a Kalman-filter hedge ratio and a regime-dependent z-score entry rule. This design extends our earlier comparison of the same two methods on the NASDAQ 100 universe, allowing us to test whether the relative behavior of DTW and OCP as clustering distances generalizes to a larger and more heterogeneous universe of stocks. 

Three findings stand out. First, OCP's causality constraint produces markedly lopsided clusters, since only pairs with a genuinely stable directional relationship receive a small distance under the constraint, while DTW produces clusters of comparable imbalance despite imposing no such constraint, suggesting the asymmetry reflects the underlying structure of Sharpe ratio similarity across the S&P 500 rather than causality constraint specifically. Second, the comparison is highly sensitive to look-ahead bias: an initial specification that ran clustering and cointegration testing on the full 2014 to 2023 sample overstated candidate pair counts and produced implausibly strong and uniform backtest results, while restricting the pipeline strictly to Train-period data reversed which method appeared more prolific, with DTW producing more surviving candidates than OCP once the correction was applied. Third, among the minority of candidate pairs that actually generated trades during the Test period, performance was positive across the board regardless of discovery method, though the small number of active pairs per group limits how confidently any performance gap between DTW-only and OCP-only pairs can be attributed to the distance metric itself rather than to sample size.



## Literature Review

DTW's application to financial time series has developed largely through modification of what the distance function is applied to, rather than the alignment algorithm itself. Franses & Wiemann (2020) show that a raw value-based distance conflates genuine dissimilarity with differences in price level, a limitation directly relevant to any pair drawn from a universe as heterogeneous in price as the S&P 500. Their fix, replacing point-to-point distance with a growth-rate based feature combining a local rate-of-change measure and a global deviation measure, makes the resulting distance scale-invariant rather than magnitude dependent, and their DTW Barycenter Averaging clustering recovers switching the lead-lag relationships in state-level GDP data that static correlation misses entirely. Bai et al. (2023) modify what is being warped in a different direction, applying DTW not to price levels but to Shannon entropy time series derived from a network's commute-time representations across 347 NYSE stocks, and find that the resulting kernel's dominant entropy value drops sharply ahead of major crises including Black Monday and the Lehman Brothers collapse. Together these results suggest that DTW's usefulness as a similarity measure is not fixed to raw price series, and that the choice of representation, growth rate, entropy or level, can materially change what structure the method recovers.

A second body of work applies DTW specifically to quantify lead-lag relationships, the same structural feature our OCP causality constraint is designed to isolate directly rather than infer after the fact. Ma et al. (2020) develop point and interval-level lead-lag measures, validate them across 5,000 simulation runs, and apply the framework to one-minute CSI 300 futures and cash index data, finding a consistent futures lead of approximately 0.73 minutes across more than a decade observations. Notably, DTW alone does not encode directionality: it recovers an optimal alignment but requires a separate measurement layer built on top of that alignment, whether Ma et al.'s point and interval statistics or Han et al.'s (2019) CR_DTW ratio computed from moving average crossovers encoded as directed graphs, to determine which series leads. This is the central methodological distinction between DTW and OCP in our own comparison: OCP builds the causality constraint into the distance calculation itself, while DTW requires this constraint to be imposed afterward. Billert and Conrad (2025) extend the lead-lag logic to sentiment data, using DTW's lag parameter as a filtering criterion to select the ESG aspect most predictive of a stock's price, and Potrykus (2024) applies DTW as a template-matching tool rather than a pairwise lead-lag measure, comparing current price trajectories against a historical bubble episode and treating a normalized distance below 0.25 as a directional signal, a filtering logic comparable in spirit to the entry thresholds in our own trading rule.

A third standard DTW distance for clustering, the strand most directly comparable to our own pipeline. Safizadeh (n.d.) combined Minimum Spanning Tree structure with DTW to separate structural centrality from temporal alignment, but validates the framework only on independently simulated data with no genuine lead-lag relationship embedded, which limits what can be concluded about its behavior on real markets. Vitulano (2024) clusters S&P 500 constituents by the DTW distance between their Sharpe ratio series and recovers a cyclical and a defensive cluster confirmed non-cointegrated via Engle-Granger testing, the same test we apply within rather than across clusters. Vasquez Saenz et al. (2023) show that DTW-based clustering improves downstream forecasting accuracy relative to single-stock or full-sample training, and Li et al. (2021) find DTW produced tighter, more sectorally coherent clusters than unstandardized Euclidean hierarchical clustering on Chinese ETFs, correctly separating asset classes that the unstandardized method conflates. Tsinaslanidis (2018) applies subsequence and derivative DTW cross-sectionally across 640 NYSE stocks to match current price and volume patterns against a historical archive, a design structurally analogous to searching 
a universe for candidate pairs rather than testing one predetermined pair. 

A useful point of contrast comes from outside the DTW literature entirely. Han et al. (2022) build a pairs trading framework from static clustering on firm characteristics and monthly return factors, using Euclidean and Manhattan distance rather than any time-series-aware measure and report a Sharpe of 2.69 for their strongest specification. Their framework has no mechanism for detecting lead-lag or time-shifted co-movement between candidate pairs, precisely the gap between DTW and OCP-based distance measures are built to close. Their result is also a reminder that non-temporal approach can still produce a strong risk-adjusted return, and that temporal similarity alone does not guarantee a tradeable relationship without some economic or statistical basis for why it should persist. 

Stubinger (2019) introduces the optimal causal path algorithm itself and remains the closest methodological precedent to our own comparison, applying OCP directly to minute-by-minute S&P 500 constituent data from 1998 to 2015. The three-step algorithm first estimates a constant lag by minimizing cost along a shifted diagonal, then permits the lag to vary locally by iteratively replacing any segment of the path with a cheaper alternative, and finally reports the estimated lag as the mean offset between matched indices along with its standard deviation as a fluctuation measure. This last step is the basis for Stubinger's pair selection criterion: rather than clustering candidates, the top 10 pairs are chosen directly by the lowest fluctuation around a non-zero, stable lag,  discarding any pairs whose lead-lag structure switches direction within the formation period. Benchmarked against correlation, Manhattan distance, and lagged cross-correlation variants of the same trading framework, OCP produced the strongest annualized return after transaction costs (54.98 percent, Sharpe ratio 3.57) and showed no significant loading on Fama-French risk factors, and the same framework extended to bitcoin found stock returns leading bitcoin returns by an average of 46.83 minutes. Two aspects of the design are directly relevant to our own design. First, Stubinger uses OCP fluctuation as a direct pair-ranking criterion within daily formation windows, whereas we use OCP distance as an input to Ward-linkage hierarchical clustering across a multi-year Train period, a different role for the same underlying algorithm. Second, Stubinger's requirement that a pair show a non-zero, non-switching lag during formation is conceptually the same filter that produces the lopsided cluster sizes we observe under OCP, since both approaches penalize pairs without a stable directional relationship rather than merely a weak one.

Taken together, the DTW and OCP literatures motivate the same basic argument from different directions. The DTW studies show repeatedly that allowing elastic alignment between two series recovers structure, whether switching lead-lag regimes, sectoral coherence, or improved forecasting accuracy, that static, non-temporal measures miss, but only a subset of that literature imposes any directional constraint on the alignment itself. Stubinger (2019) closes that gap by embedding the constraint directly into the distance calculation, and demonstrates on the same asset universe we study that doing so is not merely a theoretical refinement but one that changes which pairs are selected and how they perform out of sample. Our own results extend this comparison in two respects the existing literature does not address directly: we evaluate DTW and OCP as competing distances within an identical clustering and cointegration pipeline rather than as competing trading frameworks with their own selection logic, and we show that the relative prolificacy of the two methods reverses once candidate selection is restricted to Train-period only, a look-ahead sensitivity neither Stubinger (2019) nor the DTW clustering literature examines. This suggests that comparisons of DTW and OCP conducted without strict period separation, ours included in its earlier uncorrected form, risk attributing to the distance metric itself a difference in candidate pool that is actually an artifact of information leakage. Liu et al. (2022) offers a useful forward-looking note here as well, showing with the related Thermal Optimal Path method that lead-lag structure in Chinese index and futures jumps shifted materially around the COVID-19 outbreak, a reminder that OCP's single-lag-per-window estimate may itself understate how much lead-lag relationships move across regimes, consistent with our own Limitations discussion of TOP as a natural extension of this framework.

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



## Robustness Check

To evaluate the sensitivity of our results to methodological choices, we conducted two robustness checks: one testing sensitivity to look-ahead bias in candidate pair selection, and one testing sensitivity to how trade-count heterogeneity is handled when comparing group performance.

### Sensitivity to Look-Ahead Bias

We first ran the full pipeline (clustering, stationarity testing and cointegration testing) on the complete 2014-2023 sample, including Validation and Test period data. This produced 46 unique OCP candidate pairs (13 Tier 1, 33 Tier 2) against 21 unique DTW candidate pairs (8 Tier 1, 13 Tier 2), suggesting OCP was the more prolific method. Test period Sharpe ratios under this specification were implausibly strong and uniform, including a single-trade pair recording a Sharpe of 181.38, indicating the candidate pool had been contaminated by information from the periods used to evaluate it.

We then restricted clustering, stationarity testing and cointegration testing to Train-period data only (2014-2017), with the Kalman filter hedge ratio initialized on Train and updated continuously through Validation and Test without re-estimation. Under this corrected specification, DTW identified 65 unique candidate pairs against OCP's 40, reversing which method appeared more prolific. This reversal restored consistency with our earlier finding on the NASDAQ 100 universe, where OCP was similarly the more selective method. The qualitative conclusion regarding pair count is therefore sensitive to whether look-ahead bias is present, underscoring the importance of restricting candidate selection to Train-period data in any pairs trading framework of this kind.

### Sensitivity to Trade-Count Heterogeneity 

Of the 27 candidate pairs identified under the corrected specifications, only 10 generated any trades during the Test period. Comparing group performance using unconditional averages across all 27 pairs, which assigns a Sharpe of zero for every group, obscuring the performance pairs that did trade. Restricting the comparison to active pairs only changes the picture: all 10 active pairs recorded a positive Sharpe ratio regardless of discovery method on tier, and activity rates were comparable between DTW-only pairs (37.5 percent) and pairs identified by both methods (36.8 percent). This indicates that our qualitative conclusion, that both methods identify pairs capable of profitable trades when those pairs are active, is robust to how zero-trade pairs are handled, though the magnitude of average Sharpe among active pairs should be interpreted cautiously given the small number of active pairs per group (3 DTW Clusters to 7 DTW + OCP Clusters).



## Limitations

Further research could extend the three-period framework from the current ten-year span (2014-2023) to fifteen or twenty years. A longer Test period specifically, rather than a longer window overall, would give existing candidate pairs more opportunities to complete full entry-exit cycles, increasing the number of active pairs available for comparison. This is distinct from candidate pair count, which is determined by Train-period clustering and cointegration testing and would not necessarily change by simply lengthening the Test-period.

Relatedly, this study evaluates a single out-of-sample Test period (2021-2023). This window does not capture the full range of market conditions a pairs trading strategy might encounter, including sustained bear markets or a crisis-driven liquidity shock comparable to 2008 or early 2020. A longer window spanning multiple distinct regimes would allow performance to be evaluated separately across calm and stressed periods, rather than relying on a single three-year sample that may not generalize.

Additionally, tighter entry and exit thresholds could produce more frequent, smaller trades rather than the large discrete jumps observed in several active pairs, such as LH/WAT's Sharpe of 11.95 over only four trades. Reducing reliance on a small number of large repricing events would produce Sharpe ratios less sensitive to any single trade, though this would need to be weighed against the reduced selectivity of a stricter trading rule.

The backtest also lacks a naive-strategy benchmark. Without comparing against a simple buy-and-hold baseline, it is not possible to establish whether the added complexity of clustering-based pair selection produces returns that justify its cost relative to a much simpler strategy.

Finally, OCP corresponds to the zero-temperature limit of a Thermal Optimal Causal Path (TOP) framework, in which probability mass concentrates entirely on a single optimal path. While OCP is computationally cheaper than TOP, which requires averaging over an ensemble of near-optimal paths, this single-path commitment may make OCP more sensitive to noise in the underlying data than a temperature-averaged approach. Incorporating TOP-based clustering across a range of temperatures could test whether this tradeoff meaningfully affects which pairs are identified and how they perform.

