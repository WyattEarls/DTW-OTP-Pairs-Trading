# Pairs Selection via DTW and OCP Clustering: Evidence of Regime-Dependent Performance on the S&P 500

## Abstract

## Introduction

## Literature Review

## Data

## Methodology

<img width="2986" height="1185" alt="dtw_dendrogram_sp500_20y" src="https://github.com/user-attachments/assets/0d292eb2-c6cd-4bbd-bc35-116692091802" />
<img width="2986" height="1185" alt="ocp_dendrogram_sp500_20y" src="https://github.com/user-attachments/assets/8542589c-0070-4c9f-8788-8cdaa355fce2" />

*Figure 1: Hierarchical clustering dendrogram for DTW and OCP distance metrics. Train period (2005-2010)*

Figure 1 presents the hierarchical clustering dendrograms produced by the DTW and OCP distance metrics on Train-period data (2005-2010). Both methods identify a k=2 partition as the statistically preferred clustering, converging across all three cluster validation indices tested (silhouette-score, Calinski-Harabasz index, and Davies-Boudlin index). At k=2, DTW partitioned the 267-stock universe into groups of 67 and 200 stocks, while OCP produced a split of 76 and 191 stocks. Pairwise agreement between the two methods' cluster assignments, measured via Adjusted Rand Index, is addressed in Figure 2.

<img width="990" height="735" alt="pairwise_ari_heatmap_sp500_20y" src="https://github.com/user-attachments/assets/a3952b5c-be36-4a05-abbc-dbf2a1bf1075" />

*Figure 2: Pairwise ARI Heatmap* 

In Figure 2, OCP and TOP achieve the highest pairwise Adjusted Rand Index (ARI) of any method pair (0.740) on Train-period data, indicating substantial agreement in which stocks the two methods group together. Despite the similarities in ARI, the two methods diverge sharply, with TOP underperforming OCP in every risk-adjusted metric during the Test period, though this gap only reaches statistical significance specifically within the bear-market regime. By contrast, DTW and OCP show a lower ARI (0.618), yet their pooled Test-period Sharpe ratios differ by only 0.087 and are statistically indistinct during bear-market weeks specifically (p=0.758). This demonstrates how neither high nor low ARI scores meaningfully predict how two methods will actually perform in a real trading environment.

<img width="2085" height="1486" alt="example_pair_LLY_RF_sp500_20y" src="https://github.com/user-attachments/assets/12bff0e5-fdd3-41a6-bb8e-9e54d90a0c71" />

*Figure 3: Example pair (LLY-RF pair candidate)*

Figure 3 examines the hedge ratio, spread, and Z-score of the LLY-RF, a pair candidate independently identified as significant by both OCP and TOP, across the full three-period lifecycle. The hedge ratio drifts steadily upward over the 20-year window, from approximately 0.85 to 1.65, reflecting a genuinely time-varying relationship between the two stocks that a static hedge ratio would fail to incorporate. The spread and z-score, computed from this evolving hedge ratio, show the mean-reverting behavior the strategy is designed to exploit. A short position is triggered when the z-score rises above the entry threshold, and a long position when it falls below the corresponding negative threshold, with both thresholds marked by dotted lines. Notably, there is no instance of discontinuity that appear at either period boundary (marked by discrete lines), validating that the Kalman filter and hedge ratio evolved continuously across the Train, Validation, and Test periods rather than abruptly resetting at each transition.




## Results

<img width="1335" height="885" alt="sharpe_comparison_test_sp500_20y" src="https://github.com/user-attachments/assets/1b99dddb-9c5a-4a5f-9647-6e698c940223" />

*Figure 4: Test-period Sharpe Comparison (2018-2025)*

Figure 4 presents the pooled Test-period (2018-2025) Sharpe ratio for each strategy. DTW achieves the highest Sharpe ratio (0.783), followed by OCP (0.696), naive buy-and-hold (0.385), and TOP (0.271). While DTW and OCP both show higher point-estimate Sharpe ratios than naive buy-and-hold, block bootstrap significance testing (discussed below) found neither difference statistically significant in this pooled comparison, motivating the regime-conditional analysis presented later in this section.

<img width="1785" height="1036" alt="cumulative_pnl_test_sp500_20y" src="https://github.com/user-attachments/assets/92509193-fdb2-4215-bb12-bed7c25c4c6b" />

<img width="1335" height="886" alt="transaction_cost_sensitivity_sp500_20y" src="https://github.com/user-attachments/assets/f954566a-fca4-45b4-878e-2bc53f07a969" />

<img width="1484" height="1036" alt="regime_sharpe_comparison_sp500_20y" src="https://github.com/user-attachments/assets/d383777a-5406-4107-904d-21a567425cea" />

## Robustness Check

## Discussion

### Limitations

## Conclusion
