---
title: 'Factor Mining in Quantitative Investing: A Survey'
date: 2025-07-01 10:05:34
math: true
index_img: /img/cover/quan.jpg
excerpt: This article is a survey for Quantitative factor mining, including basic concepts, methodology and factor effectiveness test
categories:
  - Artificial Intelligence
tags:
  - Finished
  - Survey
  - Quantitative Factor Mining
---

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>

# Introduction to Quantitative Factor Mining: A survey

{% note primary %}

The first part of my final assignment for **Programming Practice** in SJTU.

Author: Xiyuan Yang, Mowen Ruan, Zhijie Chen.



For more info: https://latex.sjtu.edu.cn/read/yyvmgdwgvfck#ba0270

{% endnote %}

## Abstract

Quantitative factor mining aims to identify systematic drivers of asset returns through mathematical and statistical modeling, with applications ranging from risk assessment to alpha generation. This survey provides a structured overview of the field by first introducing fundamental factors—such as **value**, **momentum**, **quality**, and **size**. We then discuss classical factor models like **CAPM** and **APT**, as well as the challenges posed by the growing "**factor zoo**", where redundant or spurious factors complicate model selection. 

The core of our analysis focuses on modern factor construction methodologies, contrasting heuristic approaches with data-driven techniques. While linear composites and economic intuition remain foundational, supervised learning methods like **gradient boosting** and **neural networks** offer flexibility in capturing non-linear patterns, albeit at the risk of overfitting. Unsupervised approaches, particularly **PCA**, provide an alternative by extracting latent statistical factors from return covariance structures, though their interpretability remains limited.

 Finally, we examine rigorous frameworks for evaluating factor effectiveness, emphasizing robustness against data-mining biases. **Information coefficient (IC)** analysis measures predictive power, while **grouped backtesting** assesses economic significance through portfolio sorts. Our findings highlight the importance of balancing statistical rigor with theoretical coherence, suggesting hybrid methodologies as a promising direction for future research in quantitative finance.

## Introduction to Quantitative Factor Mining

### Definition
**Quantitative Analysis (QA)** is the use of mathematical and statistical methods to ascertain the value of financial assets and inform investment decisions. **Quantitative factors**, often referred to as factors in quantitative finance, are characteristics or attributes of assets (like stocks) that explain their risk and return. More specifically, a factor is essentially any characteristic that can be used to explain or predict the returns and risks of a group of securities. These factors are typically identified through empirical research and statistical analysis. The core idea is that different assets respond to these underlying drivers in a systematic way.

### Common Quantitative Factors
Given that stock price fluctuations are influenced by multifaceted market dynamics and other factors, the selection of predictive factors remains highly flexible. Therefore, quantitative factors can be analyzed from both macro and micro dimensions. Generally, all factors can be categorized into **style factors** (such as value, momentum, quality, size, and low volatility) and **macroeconomic factors** (such as interest rates, inflation, and liquidity).

Here is an overview of the most frequently analyzed style factors:

| Factor             | Core Idea                                                    |
| :----------------- | :----------------------------------------------------------- |
| **Value**          | Stocks with low prices relative to their fundamental accounting values outperform growth stocks, which is known as **Value Premium** in Quantitative Analysis. Conceptually, it relies on the principle of mean reversion, where market prices will eventually reflect a company's true intrinsic value. Stocks of undervalued companies with growth potential will generate profits until reaching a stable equilibrium. |
| **Momentum**       | Over the long term, securities with higher levels of momentum typically outperform the market, the so-called **momentum premium**. The core logic of a momentum strategy is "buying winners and selling losers". This strategy heavily relies on periodic portfolio adjustments to maintain consistent exposure to recent strong performers. |
| **Quality**        | The quality factor refers to companies with stronger profitability, more robust balance sheets, and lower financial leverage tending to perform better in the market. Common metrics used to gauge quality include **return on equity (ROE)**, **balance sheet accruals ratio**, and **financial leverage ratio**. |
| **Size**           | The size factor refers to the empirically observed phenomenon that mid- and small-capitalization stocks generally outperform large-capitalization stocks. The underlying logic includes risk-based compensation for factors such as higher financial distress probability and information asymmetry — smaller firms are more often mispriced by the market. |
| **Low Volatility** | The low volatility factor refers to the empirical finding that securities with lower historical price volatility or market beta tend to generate higher risk-adjusted returns. This phenomenon, known as the **Low Volatility Anomaly**, challenges traditional financial theories (e.g., CAPM) where risk and expected return are positively related. Explanations for this anomaly include leverage constraints, which force investors to overweight high-beta stocks, and behavioral biases such as a lottery preference for volatile securities. |

---

#### Value Factor
Common metrics used to quantify the value factor include **Price-to-Earnings (P/E) ratio**, **Price-to-Book (P/B) ratio**, **Dividend Yield**, and **Price/Earnings to Growth (PEG) ratio**.

* **Price-to-Earnings (P/E) Ratio:**
    $$\text{P/E} = \frac{\text{Market Price per Share}}{\text{Earnings per Share (EPS)}}$$
    where a lower P/E suggests potential undervaluation.

* **Price-to-Book (P/B) Ratio:**
    $$\text{P/B} = \frac{\text{Market Price per Share}}{\text{Book Value per Share}}$$

* **Dividend Yield:**
    $$\text{Dividend Yield} = \frac{\text{Annual Dividends per Share}}{\text{Market Price per Share}} \times 100\%$$
    It reflects income generation relative to price.

* **Price/Earnings-to-Growth (PEG) Ratio:**
    $$\text{PEG} = \frac{\text{P/E Ratio}}{\text{Annual EPS Growth Rate}}$$
    where PEG < 1 may signal undervaluation accounting for growth.

#### Momentum Factor
The **Momentum factor** captures the tendency of assets to continue their recent performance trends. Common quantification methods include **Time-Series Momentum**, which measures the trend of an asset's own performance over time, and **Cross-sectional Momentum**, which ranks assets by their relative performance within the same period.

* **Time-Series Momentum (Absolute Momentum):**
    $$M_{\text{abs}} = \text{Sign}(r_{t-k:t}) \cdot \left| \frac{r_{t-k:t}}{\sigma_{t-k:t}} \right|$$
    where $r_{t-k:t}$ is the past k-period return and $\sigma_{t-k:t}$ is the volatility over the same period.

* **Cross-Sectional Momentum (Relative Momentum):**
    $$M_{\text{rel}} = \text{Rank}(r_{t-k:t})$$
    This ranks assets based on past k-period returns within a universe (e.g., top 10% vs. bottom 10%).

#### Quality Factor
**Quality** is quantified through profitability, earnings stability, and balance sheet strength:

* **Profitability:**
    $$\text{ROE} = \frac{\text{Net Income}}{\text{Shareholders' Equity}}, \quad \text{ROA} = \frac{\text{Net Income}}{\text{Total Assets}}$$

* **Earnings Quality:**
    $$\text{Accruals} = \frac{\text{Net Income} - \text{Operating Cash Flow}}{\text{Total Assets}}$$
    (Lower accruals indicate higher earnings quality)

#### Size Factor
The core principle of the **size factor** is to measure the total market value of a company. Hence, **market capitalization** (market cap) is the universal and most direct metric. Because market cap distribution is heavily skewed (a few firms with very large cap and many with small cap), quantitative analysts often take the natural logarithm of the raw data.
$$\text{Market Cap} = \log(\text{Current Share Price} \cdot \text{Total Number of Shares})$$

#### Low Volatility Factor
The **low volatility factor** captures the stability and riskiness of a stock's price movements. Metrics include:

* **Historical Volatility:** It is quantified as the standard deviation of the stock's historical returns (over a specific period backward).
    $$\text{Volatility} = \sigma(R_1, R_2, \dots, R_n)$$
    where the $R$'s are a series of periodic returns over the chosen time period.

* **Market Beta ($\beta$):** It quantifies the systematic risk of a stock and is calculated by regressing a stock's excess return against the market's excess return.
    $$R_{\text{stock}} - R_f = \alpha + \beta \cdot (R_{\text{market}} - R_f) + \varepsilon$$
    where $R_{\cdot}$ is the return of a placeholder asset, $R_f$ is the risk-free rate, and $\varepsilon$ is the residual.

#### Macroeconomic Factors
Unlike style factors, which affect specific firms or industries, **macroeconomic factors** are systematic variables that reflect the state of the broader market environment. They are primarily used for top-down asset allocation, factor timing, and building comprehensive risk models. Some of the most critical macroeconomic factors are listed below:

* **Economic Growth:** It measures the overall health and expansion rate of economic activity. Strong economic growth typically implies higher corporate profitability; yet it can also trigger inflation and rate hike expectations. Metrics include **GDP**, **PMI** (Purchasing Managers' Index), and **industrial production**.
* **Inflation:** High inflation erodes corporate profits and the future purchasing power of investment. It typically prompts central banks to tighten monetary policy, imposing pressure on both equity and bond valuations. Metrics include **CPI** (Consumer Price Index) and **PPI** (Producer Price Index).
* **Interest Rates and Monetary Policy:** They reflect the cost of capital and the availability of liquidity. Metrics include **Central Bank Policy Rate**, **Long-term Government Bond Yield**, and **Credit Spread**.

### Factor Models
A key aspect of analyzing stock trends is selecting common factors for factor analysis and modeling. As a result, economists have proposed numerous factor models which analyze several factors and make judgments, including the **Capital Asset Pricing Model (CAPM)** by William Sharpe and **Arbitrage Pricing Theory (APT)** by Stephen Ross.

#### Capital Asset Pricing Model
The Capital Asset Pricing Model suggests that the expected return on the capital asset $E(R_i)$ follows:
$$E\left(R_{i}\right)=R_{f}+\beta_{i}\left[E\left(R_{m}\right)-R_{f}\right]$$
$E(R_{m})-R_{f}$, which is sometimes known as the **market premium**, is the single factor in this model, thus CAPM can be seen as a simplified **Single Factor Model**. $\beta_i$ is the sensitivity of the expected excess asset returns to the expected excess market returns, or also:
$$\beta_i = \frac {\text{Cov}(R_{i},R_{m})}{\text{Var}(R_{m})} = \rho _{i,m}{\frac {\sigma _{i}}{\sigma _{m}}}$$

#### Arbitrage Pricing Theory
The limitation of CAPM lies in its consideration of only market risk, while in reality, stock returns are influenced by multiple factors (such as industry, size, value, etc.). As a result, economists have developed multifactor models, such as:

* **Fama-French three-factor model**: market factor, size factor and value factor.
* **Carhart four-factor model**: Adding the momentum factor.

**Arbitrage Pricing Theory (APT)** is a multi-factor asset pricing model based on the no-arbitrage principle. Unlike CAPM, APT does not rely on the market portfolio's efficiency but assumes asset returns are driven by multiple systematic risk factors, which allows for any number of macroeconomic or statistical factors.

APT assumes asset returns can be expressed as:
$$R_{i}=E\left(R_{i}\right)+\sum_{k=1}^{K} \beta_{i, k} F_{k}+\epsilon_{i}$$
In this equation, the actual return of asset i ($R_i$) is calculated by sensitivity (factor loading) of asset i to factor k ($\beta_{i,k}$) and unexpected changes in factor k ($F_k$).

Under no-arbitrage, expected returns follow:
$$E\left(R_{i}\right)=R_{f}+\sum_{k=1}^{K} \beta_{i, k} \lambda_{k}$$
$\lambda_{k}$ represents the risk premium for factor k.
Factor selection is quite flexible in the APT equation. Factors in APT may include both style factors and macroeconomic factors which are mentioned in section 1.2.

### Factor Zoo Phenomenon
Nowadays, with the rapid development of computational resources, hundreds of "significant factors" have been discovered through data analysis. While early models relied on economically grounded risk exposures, recent studies have identified over 300 factors—ranging from fundamentals (e.g., profitability) to unconventional metrics (e.g., weather data).

"**101 Formulaic Alphas**" is a seminal work that defines 101 quantitative trading alphas constructed from price, volume, and other market data. They represent common patterns in financial markets, with each alpha designed to capture distinct market inefficiencies or behavioral anomalies.

Here are some examples from the "101 Formulaic Alphas" work:
* **Alpha#12:** $(\text{sign}(\Delta(\text{volume}, 1)) \times (-1 \times \Delta(\text{close}, 1)))$
* **Alpha#13:** $(-1 \times \text{rank}(\text{covariance}(\text{rank}(\text{close}), \text{rank}(\text{volume}), 5)))$
* **Alpha#14:** $(((-1 \times \text{rank}(\Delta(\text{returns}, 3))) \times \text{correlation}(\text{open}, \text{volume}, 10)))$
* **Alpha#15:** $(-1 \times \text{sum}(\text{rank}(\text{correlation}(\text{rank}(\text{high}), \text{rank}(\text{volume}), 3)), 3))$
* **Alpha#16:** $(-1 \times \text{rank}(\text{covariance}(\text{rank}(\text{high}), \text{rank}(\text{volume}), 5)))$
* **Alpha#17:** $(((-1 \times \text{rank}(\text{ts\_rank}(\text{close}, 10))) \times \text{rank}(\Delta(\Delta(\text{close}, 1), 1))) \times \text{rank}(\text{ts\_rank}((\text{volume} / \text{adv20}), 5)))$
* **Alpha#18:** $(-1 \times \text{rank}((\text{stddev}(|(\text{close} - \text{open})|, 5) + (\text{close} - \text{open})) + \text{correlation}(\text{close}, \text{open}, 10))))$
* **Alpha#19:** $( ( -1 \times \text{sign}(((\text{close} - \text{delay}(\text{close}, 7)) + \Delta(\text{close}, 7)))) \times (1 + \text{rank}((1 + \text{sum}(\text{returns}, 250)))) )$
* **Alpha#20:** $ (((-1 \times \text{rank}((\text{open} - \text{delay}(\text{high}, 1)))) \times \text{rank}((\text{open} - \text{delay}(\text{close}, 1)))) \times \text{rank}((\text{open} - \text{delay}(\text{low}, 1)))) $
* **Alpha#21:**
    $$
    \begin{cases} 
    -1 \times 1 & \text{if } (\frac{\text{sum}(\text{close}, 8)}{8} + \text{stddev}(\text{close}, 8)) < \frac{\text{sum}(\text{close}, 2)}{2} \\
    1 & \text{else if } \frac{\text{sum}(\text{close}, 2)}{2} < (\frac{\text{sum}(\text{close}, 8)}{8} - \text{stddev}(\text{close}, 8)) \\
    1 & \text{else if } (1 < \frac{\text{volume}}{\text{adv20}}) \lor (\frac{\text{volume}}{\text{adv20}} = 1) \\
    -1 \times 1 & \text{otherwise}
    \end{cases}
    $$
    However, many factors suffer from **data mining bias** (in-sample overfitting), **redundancy** (high correlations between factors like ROE and ROA), or lack economic rationale (e.g., "Super Bowl Indicator"). Hence, the pursuit of parsimony represents central challenges in quantitative finance.

## Factor Construction Methodology
**Factor construction** is the process of translating financial theories and raw data into a predictive quantity (factor). A cornerstone principle of robust factor construction is the need for a concrete economic rationale or a plausible causal mechanism. This principle serves as a critical safeguard against data mining bias. Otherwise, models may identify deceptive correlations in historical data that yet lack genuine predictive power, a phenomenon known as **overfitting**. Simply discovering a pattern is insufficient; there must be a compelling reason why that pattern should exist and persist.

Adhering to this principle, the methodologies for constructing factors can be classified into several distinct paradigms. The first is a **heuristic paradigm**, where factors are built directly from established economic concepts. Such factors boast decent transparency and interpretability. In stark contrast is the **data-driven paradigm** of supervised learning, which employs algorithms to learn complex relationships directly from data by mapping predictive features to future returns. A third approach is the **unsupervised learning paradigm**, which seeks to unveil the inherent statistical structure and patterns within the data itself, without reference to a future outcome.

### Heuristic and Linear Composite Factors
Traditionally, quantitative analysts rely on their economic knowledge and intuition and linear modeling to construct factors. Those methods are completely transparent and interpretable, as they are directly motivated by and derived from economic theories.

* **Single-Variable Factors:** This category of factors is self-explanatory and disarmingly simple. The variable is picked based upon established financial theories or empirical evidence. Examples of such factors include the **Value factor**, which is often constructed by simply using the raw B/P values after appropriate preprocessing.

* **Linear Composite Factors:** Multiple single-variable factors can be combined to create a more robust factor with stronger predictive power, called a **composite factor**. Such factors are constructed as the weighted average of several indicators, where weights are determined by either economic rationale or by statistical methods. For example, the **Quality factor** can be constructed as a linear combination of ROE, Debt-to-Equity Ratio, and the volatility of earnings. Albeit more comprehensive than single variables, this method still assumes a linear relationship and picks weights manually, which is a less than disciplined approach.

### Supervised Learning Approaches
**Supervised learning models** construct factors by explicitly learning the relationship between a series of predictive features and an indicator of future asset performance (the label). The objective is to find a set of model parameters, $\theta$, that minimizes a given loss function $\mathcal{L}$:
$$\theta^*=\arg\min_\theta\frac1N\sum_{i=1}^N\mathcal{L}(y_i,f(\mathbf X_i;\theta))$$
where $\mathbf X_i, y_i$ are the vector of input features and label (future return) for asset i. The learned mapping $f$ is considered a "factor" itself: the factor value is $\hat y=f(\mathbf X)=f(\mathbf X,\theta^*)$, where $\mathbf X$ is the vector of input features.

* **Tree-Based Models: Gradient Boosting:** **Gradient Boosting Decision Trees (GBDT)** build a predictive model in an additive, stage-wise way. The model $F_M(\mathbf X)$ is an ensemble of M individual decision trees $h_m(\mathbf X)$:
    $$F_M(\mathbf X)=\sum_{m=1}^M\gamma_m h_m(\mathbf X)$$
    where $\gamma_m$ is the learning-rate or step-size. Every tree is trained to predict the negative gradient of the loss function with respect to the previous model's prediction, which empowers GBDT to capture non-linearities and high-order feature interactions.

* **Deep Learning: Neural Networks:** **MLPs (Multi-Layer Perceptron)** learn hierarchical representations of the data through interconnected layers of neurons. The calculation of the factor value involves a forward propagation process, which transforms the input features through successive layers, each learning a progressively more abstract representation of the data. The activation of the $l$-th hidden layer, $\mathbf h^{(l)}$ ($\mathbf h^{(0)}=\mathbf X$), is recursively computed with:
    $$\mathbf h^{(l)}=\sigma_l\left(\mathbf W^{(l)}\mathbf h^{(l-1)}+\mathbf b^{(l)}\right)$$
    where $\mathbf W^{(l)}$ and $\mathbf b^{(l)}$ are the weight matrix and bias vector for layer $l$, and $\sigma_l$ is the non-linear activation function for layer $l$ (e.g., ReLU, sigmoid, hyperbolic tangent). The final output is the value of the factor. The power of this approach is guaranteed by the Universal Approximation Theorem. Neural networks are powerful in handling high-dimensional, complex data without extensive manual feature engineering.

### Unsupervised Learning Approaches
**Unsupervised learning methods** do not explicitly predict labels. They instead identify latent structures and patterns within the data. They are primarily employed to construct statistical risk factors that explain the sources of common variation in asset returns. This paradigm is exemplified by the statistical method of **Principle Component Analysis (PCA)**.

**PCA** is a dimensionality reduction technique used to construct statistical factors by analyzing the comovement of asset returns. Given an N by T matrix of asset returns $\mathbf R$, where N is the number of assets and T is the number of time periods, PCA aims to find a set of orthogonal vectors (principle components) that explain the maximum amount of variance in the data. In other words, the k-th principle component is the solution of the following optimization problem:
$$\mathbf w_k=\arg\max_{\mathopen{}\left\lVert\mathbf w\right\rVert=1}\text{Var}(\mathbf R^\mathrm T\mathbf w)\quad\text{subject to}\quad\mathbf w_k^\mathrm T\mathbf w_j=0,\forall j<k$$
This is equivalent to finding the eigenvectors of the sample covariance matrix $\frac1{T-1}\mathbf R\mathbf R^\mathrm T$. In our context, the principle components $\{\mathbf w_k\}$ are the statistical factors. These factors, despite their power for risk modeling, typically lack a direct economic interpretation.

## Factor Effectiveness Testing
**Quantitative factor effectiveness** refers to the ability of a factor to predict future asset returns consistently. The factors we have identified may not be effective, so we need to conduct effectiveness tests. Here are some common methods.

### Information Coefficient Analysis
**Information Coefficient (IC)** analysis is a cornerstone technique in quantitative investing for assessing the effectiveness of predictive factors. By measuring the correlation between factor values and subsequent returns, IC provides a quantitative framework to evaluate factor quality, optimize portfolio construction, and inform trading strategies.

The Information Coefficient is defined as the correlation between factor values and future returns. Mathematically, it is expressed as:
$$\text{IC}\_t = \text{Corr}(f\_{i,t}, r_{i,t+1})$$
where $f_{i,t}$ represents the factor value for asset $i$ at time $t$, and $r_{i,t+1}$ denotes the return from time $t$ to $t+1$.

* **Pearson Information Coefficient:** The Pearson IC measures linear correlation and is computed as:
    $$\text{IC}\_t = \frac{\text{Cov}(f\_{i,t}, r_{i,t+1})}{\sigma(f_{i,t}) \cdot \sigma(r_{i,t+1})}$$
    where $\text{Cov}$ denotes covariance and $\sigma$ represents standard deviation.

* **Spearman Rank Information Coefficient (RIC):** To mitigate the impact of outliers, the Spearman RIC uses rank-transformed values:
    $$\text{RIC}\_t = \text{Corr}(\text{rank}(f\_{i,t}), \text{rank}(r_{i,t+1}))$$
    This is equivalent to the Pearson correlation applied to ranked data.

For each time period $t$, the cross-sectional IC is calculated as follows:
1.  Compute cross-sectional means:
    $$\bar{f}\_t = \frac{1}{N} \sum_{i=1}^N f\_{i,t}, \quad \bar{r}\_{t+1} = \frac{1}{N} \sum_{i=1}^N r_{i,t+1}$$
2.  Standardize factor values and returns:
    $$f'\_{i,t} = f\_{i,t} - \bar{f}\_t, \quad r'\_{i,t+1} = r\_{i,t+1} - \bar{r}\_{t+1}$$
3.  Calculate covariance and standard deviations:
    $$\text{Cov}\_t = \frac{1}{N} \sum\_{i=1}^N f'\_{i,t} \cdot r'\_{i,t+1}$$
    $$\sigma_{f,t} = \sqrt{\frac{1}{N} \sum_{i=1}^N (f'\_{i,t})^2}, \quad \sigma\_{r,t} = \sqrt{\frac{1}{N} \sum\_{i=1}^N (r'\_{i,t+1})^2}$$
4.  Compute Pearson IC:
    $$\text{IC}\_t = \frac{\text{Cov}\_t}{\sigma\_{f,t} \cdot \sigma\_{r,t}}$$

To assess the stability of factor performance over time, aggregate IC values using:
* **Mean IC:** $\overline{\text{IC}} = \frac{1}{T} \sum_{t=1}^T \text{IC}_t$
* **Standard Deviation of IC:** $\sigma_{\text{IC}} = \sqrt{\frac{1}{T-1} \sum_{t=1}^T (\text{IC}_t - \overline{\text{IC}})^2}$
* **t-Statistic:** $t = \frac{\overline{\text{IC}} \cdot \sqrt{T}}{\sigma_{\text{IC}}}$. A t-value greater than 1.96 indicates statistical significance at the 95% confidence level.

The **Information Ratio** measures the risk-adjusted performance of a factor:
$$\text{IR} = \frac{\overline{\text{IC}}}{\sigma_{\text{IC}}} \cdot \sqrt{M}$$
where M is the number of periods per year (e.g., M=12 for monthly data).

### Group Backtesting Analysis
**Group backtesting** provides a straightforward yet powerful framework for factor evaluation. It helps identify factors with significant predictive power and assesses their monotonicity, forming the basis for robust quantitative strategies.

Factor standardization is typically performed using z-score normalization:
$$\hat{f}\_{i,t} = \frac{f_{i,t} - \mu_t}{\sigma_t}$$
where $\mu_t = \frac{1}{N}\sum_{i=1}^N f_{i,t}$ and $\sigma_t = \sqrt{\frac{1}{N}\sum_{i=1}^N (f_{i,t} - \mu_t)^2}$.

1.  Assets are sorted into K groups (e.g., quintiles, K=5) based on $\hat{f}\_{i,t}$. The assignment rule for group k is:
    $$G_{k,t} = \left \\\{ i : \frac{(k-1)N}{K} < \text{rank}(\hat{f}_{i,t}) \leq \frac{kN}{K} \right \\\}$$
2.  The equal-weighted return for group k at time t+1 is:
    $$R_{k,t+1} = \frac{1}{|G_{k,t}|} \sum_{i \in G_{k,t}} r_{i,t+1}$$
3.  The long-short portfolio return (factor spread) is:
    $$R_{\text{spread},t+1} = R_{K,t+1} - R_{1,t+1}$$
4.  The mean return for each group over T periods is:
    $$\bar{R}\_k = \frac{1}{T} \sum_{t=1}^T R_{k,t+1}$$
5.  The t-statistic for testing the significance of group returns:
    $$t_k = \frac{\bar{R}\_k}{s_k / \sqrt{T}}$$
    where $s_k = \sqrt{\frac{1}{T-1} \sum_{t=1}^T (R_{k,t+1} - \bar{R}_k)^2}$.
6.  The information ratio (IR) for the factor spread:
    $$\text{IR} = \frac{\bar{R}\_{\text{spread}}}{s\_{\text{spread}}} \sqrt{T}$$

## Conclusion
Overall, factor mining is a core issue in the field of quantitative investment. It employs data analysis to eliminate irrational biases and make optimal decisions. This survey has explored the construction, evaluation, and practical challenges of quantitative factors.

The "**factor zoo**" problem underscores the need for disciplined model selection—balancing predictive power with theoretical coherence. Future work should prioritize ways to leverage data analytics to identify more effective factors and construct concise yet accurate multi-factor models.
