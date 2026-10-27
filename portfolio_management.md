# Notes on: Advanced Portfolio Management by Giuseppe A. Paleologo

Giuseppe Paleologo is a **cracked** quant researcher. As of Oct 2026, he's the Global Head of Quantitative Research at Balyasny Asset Management, a $32-37B AUM global hedge fund. Previously, he was the Head of Risk Management at Hudson River Trading (2021-2023), Head of Enterprise Risk at Millenium (2018-2020), Director of Risk & Quantitative Analytics at Citadel (2015-2017) among others. Safe to say, he knows his stuff.

This book is meant to be a practical book on how people in the actual quantitative industry build, risk-manage, and optimize portfolios. Given Paleologo's experience, these should be definitely applicable to equity long/short or multi-manageer hedge funds. Personally, I'm reading to better understand how quant professionals would properly utilize covariance matrices to decompose risk or alpha from unintended beta exposure. 


Chapter 1 & 2 are introductions, we're skipping.

## Chapter 3: Tour of Risk and Performance

**Beta**: Measured as the linear regression between a stock's returns (hourly/daily/weekly, whatever you want) and the benchmark's returns.

Choosing the benchmark is up to the user - common benchmark for US equities are SPY, or the US 10-Y yield. You want to choose a benchmark that satisfies these requirements:
1. It has to be sufficiently broad and respentative of the market;
2. The benchmark has to be sufficiently liquid, via an ETF or a future, or both;
3. Beta is estimated with respect to a risk model, which in turn may *not* be adequate for the purpose -- e.g. using a global model (ie. representing all tradable world assets) to estimate the beta of a European portfolio to the STOXX50 (50 stocks from the Eurozone) does not work.

For a given stock, we model the return as a *single-factor model* as:

$$
\begin{aligned}
r &= \alpha + \beta\times m + \epsilon, \\
r &= return \\
\alpha &= intercept \\
\beta &= beta \\
m &= market \\
\epsilon &= noise
\end{aligned}
$$

This is also called the **Capital Asset Pricing Model (CAPM)**.
In theory, if the market return is zero, the daily stock return is the alpha component. But that would be discount the idiosyncratic return, or $\epsilon$. So technically, you can simplify it as 

$$
(expected\space return) = (alpha) + (beta) \times (expected\space market\space return)
$$

In the quant industry:
* A *fundamental analyst* tries to estimate $\alpha$
* A *risk manageer* tries to estimate $\beta$, identify correct market benchmark $m$ and idio return $\epsilon$
* A *macroeconomic investor* tries to esitmate the expected value of $m$.

### How do we calculate risk?

The dollar variance/volatility of each stock is the sum of the market volatility and idiosyncratic volatility. 

$$\sigma_{\text{stock}} = \text{NMV} \times \sqrt{\beta^2 \sigma_{\text{market}}^2 + \sigma_{\text{idio}}^2}$$

This should a trivial calculation - you can do this in Excel or even use a Bloomberg function to do this (BETA \<GO>).

Knowing this, how do we mitigate the risk? The simple method is by *hedging*. A simple hedge would be taking the opposite direction in the market benchmark compared to the asset you're taking risk on. Meaning if you are trading US equities like $NVDA or $APPL, then you can short $SPY to isolate the idiosyncratic returns of these stocks. You're effectively buying how these stocks do *compared to the other top 500 stocks*.

The amount of the short is calcuated by sum of $\text{NMV}\times \beta$, so that the market beta neutralizes single stock beta.

## Chapter 4: Multi-Factor Models

Naturally, we know that alphas in market cannot be measured from one straightfowrard regression. So an extension of the single-factor model would be into a *multi-factor model*:

$$
r = \alpha + \beta^1\times f_1 + ... + \beta^m\times f_m + \epsilon
$$

These betas are now *loadings* of a stock to a factor, so these are 'characteristics' of a stock. To reduce the factor to a more implementable version, we have a *two* factor model - a market-based one and a market-cap-based one:

$$
r = \alpha + \beta^M r^M + \beta^\text{BMS} r^\text{BMS} + \epsilon
$$

where M is the Market and BMS is 'Big Minus Small'.

So over time, more factors have been discovered and used in government bonds and corporate credit. Factors and betas are found via broadly three ways:
1. **Time-Series Approach**: Macroeconomic indicators, inflation, interest rates, economic growth, employment data, business cycle indicators, economic sentiment data (e.g. consumer confidence), Detailed economic activity data (same-store sales, purchasing manager indices, etc.). You can estimate the sensitivity of these factors via a **time-series regression** of the stock return.
2. **Fundamental Approach**: Pioneered by Barr Rosenberg (Barra). Betas are primitive information/characteristics of each company.
3. **Statistical Approach**: Derived from time series of asset returns. Beta and factor returns are estimated using asset returns alone.

**Fundamental models** are used the most by fundamental managers, while **time series models** are neccessary for time series betas. However, **time-series-based models are more popular in academic literature than in real life**. Mostly because of interpretability and performance concerns.

Statistical (time-series) models have the least data requirement - only the time-series data. They also have **comparable** performance for volatility prediction.

FAQ:
1. How can two models with diff. versions of factors (e.g. z-score vs raw) be different for specific factors?
  - **TLDR: Predictive power of regressiosn don't change if you mix and match vriables**. The $R^2$ of a regression is the same if your variables are squared/doubled etc. The regression coefficients will change, but $R^2$ stays the same.

2. Unit of measure for loadings? Exposures? Portoflio Volatility?
  - **Loadings**: dimentionless
  - **Exposures**: Currency
  - **Volatility**: Currency/Percentage

3. Annualized volatility to weekly/daily?

$$
\sigma _\text{yearly}=\sqrt{52} \times \sigma _\text{weekly}
$$

$$
\sigma _\text{yearly}=\sqrt{252} \times \sigma _\text{daily}
$$

