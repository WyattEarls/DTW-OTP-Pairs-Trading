# Comparison Between DTW and OCP Clustering on the S&P 500

## Methodology

## Universe Construction

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
