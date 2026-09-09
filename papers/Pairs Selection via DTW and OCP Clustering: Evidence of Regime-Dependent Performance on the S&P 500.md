# Pairs Selection via DTW and OCP Clustering: Evidence of Regime-Dependent Performance on the S&P 500

## Abstract

## Introduction

## Literature Review

## Data

## Methodology

<img width="2986" height="1185" alt="dtw_dendrogram_sp500_20y" src="https://github.com/user-attachments/assets/0d292eb2-c6cd-4bbd-bc35-116692091802" />
<img width="2986" height="1185" alt="ocp_dendrogram_sp500_20y" src="https://github.com/user-attachments/assets/8542589c-0070-4c9f-8788-8cdaa355fce2" />

*Figure 1: Hierarchical clustering dendrogram for DTW and OCP distance metrics. Train period (2005-2010)*

Figure 1 presents the hierarchical clustering dendrograms produced by the DTW and OCP distance metrics on Train-period data (2005-2010). Both methods identify a k=2 partition as the statistically preferred clustering, converging across all three cluster validation indices tested (silhouette-score, Calinski-Harabasz index, and Davies-Boudlin index). At k=2, DTW partitioned the 267-stock universe into groups of [X] and [Y] stocks, while OCP produced a split of 76 and 191 stocks. Pairwise agreement between the two methods' cluster assignments, measured via Adjusted Rand Index, is addressed in Figure 2.

<img width="990" height="735" alt="pairwise_ari_heatmap_sp500_20y" src="https://github.com/user-attachments/assets/a3952b5c-be36-4a05-abbc-dbf2a1bf1075" />

*Figure 2: Pairwise ARI Heatmap* 

<img width="2085" height="1486" alt="example_pair_LLY_RF_sp500_20y" src="https://github.com/user-attachments/assets/12bff0e5-fdd3-41a6-bb8e-9e54d90a0c71" />

*Figure 3: Example pair (LLY-RF pair candidate)*





## Results

<img width="1335" height="885" alt="sharpe_comparison_test_sp500_20y" src="https://github.com/user-attachments/assets/1b99dddb-9c5a-4a5f-9647-6e698c940223" />

<img width="1785" height="1036" alt="cumulative_pnl_test_sp500_20y" src="https://github.com/user-attachments/assets/92509193-fdb2-4215-bb12-bed7c25c4c6b" />

<img width="1335" height="886" alt="transaction_cost_sensitivity_sp500_20y" src="https://github.com/user-attachments/assets/f954566a-fca4-45b4-878e-2bc53f07a969" />

<img width="1484" height="1036" alt="regime_sharpe_comparison_sp500_20y" src="https://github.com/user-attachments/assets/d383777a-5406-4107-904d-21a567425cea" />

## Robustness Check

## Discussion

### Limitations

## Conclusion
