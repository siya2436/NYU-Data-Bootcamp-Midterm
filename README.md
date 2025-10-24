# NYU-Data-Bootcamp-Midterm
## Sentiment, Risk, and Investor Behavior—An Exploratory Study on Tesla (TSLA)
Jiamei Shi(js14017), Siya Xu(sx2436), Tingyun Zhang(tz2674)
### 1. Introduction
The modern financial market is increasingly shaped by the flow of online information. From Reddit threads, investor sentiment now moves markets faster than ever before. This project investigates how social-media-driven sentiment relates to Tesla’s stock performance and downside risk. Specifically, the goal is to explore whether changes in public mood can explain or even predict variations in Tesla’s volatility, crash likelihood, and risk-adjusted investor welfare.

Tesla, Inc. (ticker: TSLA) offers a compelling case for such analysis. As one of the most publicly discussed and traded companies, Tesla’s stock has historically been influenced not only by earnings and fundamentals but also by the behavior of its online fanbase. Between July 2021 and July 2022, a period marked by major corporate announcements, product updates, and broad market volatility, Tesla’s price movement reflected the complex interplay between optimism, speculation, and fear.

### 2. Research Motivation and objective
#### 2.1 Motivation
The motivation behind this project stems from the growing influence of social media on modern financial markets. Platforms like Reddit and Twitter have transformed how investors exchange information, form opinions, and coordinate trading behavior. These digital interactions often generate collective emotions such as optimism, fear, or hype that can move asset prices independently of fundamentals.

Drawing inspiration from concepts studied in the Macroeconomic Foundations for Asset Pricing course, particularly the Poisson–Normal Mixture models used to capture rare-event risk, this project seeks to explore how such emotional dynamics translate into measurable financial risk.

Therefore, our project focus on Tesla (TSLA), a stock that uniquely blends strong fundamentals with a passionate online investor community.

#### 2.2 Objective
The project’s central question is:
Does investor sentiment, as expressed on Reddit, influence Tesla’s market risk and crash probability?

To answer this, several related objectives were pursued:
1. Explore sentiment dynamics: Quantify and visualize how investor mood fluctuates over time and whether it aligns with changes in Tesla’s stock returns.
2. Characterize downside risk: Use downside semivariance and crash indicators to measure periods of heightened negative volatility.
3. Test sentiment–risk relationships: Evaluate statistically whether lower sentiment predicts higher downside risk and higher probability of extreme losses.
4. Model “disaster intensity”: Apply a Poisson–Normal Mixture (PNM) model to decompose Tesla’s returns into normal daily fluctuations and rare, jump-like “disaster” events.
5. Assess investor welfare: Estimate how changes in sentiment alter the risk-adjusted welfare of a representative investor, using a constant-relative-risk-aversion (CRRA) framework.
   
By combining exploratory visualization and quantitative modeling, the study bridges behavioral finance and modern risk theory.

### 3. Data Source
#### 3.1 Market Data (Yahoo Finance)

Using the yfinance API, the script retrieved Tesla’s daily adjusted closing prices, trading volume, and returns from January 2021 to December 2022. Returns were computed as daily percentage changes in adjusted close prices.

These variables provide the market backbone for merging with sentiment information and constructing financial risk measures such as downside semivariance and crash indicators.

#### 3.2 Reddit Sentiment Data (Kaggle Dataset)

The Reddit dataset includes every post and comment mentioning “TSLA” between July 5, 2021, and July 4, 2022. Each record includes: create_utc: the timestamp of the post or comment, sentiment: a sentiment score (positive = optimism, negative = pessimism), score: the Reddit upvote score (a measure of engagement).

The analysis first converted timestamps into readable datetime format and extracted daily averages.

This produced a daily sentiment index representing collective investor mood toward Tesla.

#### 3.3 Merging and Derived Variables

The sentiment and market datasets were merged on the common date variable, resulting in a combined panel of TSLA’s stock price, daily average sentiment, stock returns, and volume. Several derived features were created:
1. Downside Semivariance (7-day rolling): Captures average negative deviation from the mean, measuring only downside risk.
2. Crash Indicator: Binary variable equal to 1 if the daily return < –5%, 0 otherwise.
3. Lagged Sentiment (1-day, 3-day): Measures the delayed effect of sentiment on market risk.
   
This preprocessing stage ensured that both behavioral (sentiment) and financial (risk) dimensions were aligned on a comparable temporal scale.

### 4. Exploratory Data Analysis
#### 4.1 Sentiment vs. Volume

A scatter plot of average daily sentiment against trading volume revealed a negative correlation, showing that as sentiment became more positive, Tesla’s trading volume tended to decrease.

This counterintuitive result aligns with behavioral finance theory: when investors are optimistic and confident, they are less likely to trade, while pessimism and uncertainty trigger higher trading activity and market churn.

#### 4.2 Downside Risk and Sentiment

Using rolling windows, the 7-day downside semivariance was calculated and plotted against average sentiment.

The scatterplot revealed that as sentiment decreases, downside risk rises, with crash days clustering in regions of low sentiment and high semivariance. This suggests that collective pessimism often coincides with or precedes large downward price swings.

#### 4.3 Returns and Sentiment over Time

Overlaying daily stock returns and sentiment trends over the same period showed a generally positive, but also complex relationship:
1. Generally Positive Relationship: Periods of elevated sentiment often coincided with positive stock returns, while lower sentiment generally aligned with market declines.
2. High-Volatility Phases: Noticeable fluctuations occurred around November 2021 and early 2022, when both sentiment and returns became unstable.
3. Lagged Interactions: The connection between sentiment and returns was not perfectly synchronized. At times, changes in sentiment appeared to precede movements in returns, while in other cases, price shifts influenced subsequent sentiment.
4. Turning Points: Sentiment occasionally acted as an early indicator of major market movements, signaling shifts before they materialized in prices.
5. Example Periods: In December 2021, sentiment and returns showed a strong positive correlation during Tesla’s bull phase. Conversely, from March to July 2022, declining sentiment paralleled falling returns, likely reflecting investor anxiety amid rising interest rates.

#### 4.4 Distribution Analysis

Histograms and kernel density plots revealed that Tesla’s returns were right-skewed with fat tails, while sentiment scores were centered slightly above zero with moderate variability.

This implies that, on average, investors were modestly optimistic, though large sentiment swings occurred during volatile periods (e.g., after earnings announcements or Elon Musk’s tweets).

#### 4.5 Correlation Heatmap

A correlation matrix among key variables summarized the relationships:
1. Sentiment correlated negatively with downside semivariance (–0.34) and crash probability (–0.26).
2. Lagged sentiment (1-day, 3-day) also showed negative correlations with future downside risk, suggesting higher sentiment from previous days can potentially predict lower future downside risk.
3. Sentiment and return had a positive correlation, which consistents with the general intuition that optimism aligns with market gains.

### 5. Quantitative Modeling: Poisson–Normal Mixture Framework
Earlier exploratory analysis revealed intuitive relationships between sentiment and market risk. Lagged sentiment (1-day and 3-day) showed negative correlations with future downside risk, suggesting that higher optimism today is often followed by lower crash likelihood. In addition, sentiment and returns were positively correlated, consistent with the general intuition that investor optimism tends to accompany market gains. These findings motivated a more formal, quantitative approach to examine how sentiment influences the dynamics of tail risk.

#### 5.1 Theoretical Setup
To model these relationships, we adopted the Poisson–Normal Mixture (PNM) framework, which describes stock returns as a combination of two components:
1. A Normal component, representing day-to-day market fluctuations.
2. A Poisson component, representing rare but severe “disaster” events.
   
Formally, returns are defined as:

rₜ = μ – jₜθ + εₜ,

where jₜ follows a Poisson distribution with intensity ω, and εₜ follows a Normal distribution with variance σ².

Here,

μ is the average daily return,

σ represents normal volatility,

θ is the average size of a crash, 

ω is the disaster intensity (the expected frequency of crashes).

These parameters were estimated using the method of moments on rolling 60-day windows, allowing the model to evolve over time and reflect changing risk conditions.

#### 5.2 Sentiment and Disaster Intensity
To test whether sentiment helps explain rare-event risk, we ran the following regression:

ωₜ = β₀ + β₁(Sentimentₜ₋₁) + β₂σₜ + εₜ.

The results showed a negative coefficient on sentiment (–3.85), indicating that higher investor optimism is associated with lower perceived disaster intensity. Volatility also had a negative and moderately significant effect (–26.37), suggesting that routine volatility and rare disasters represent distinct sources of risk. The model explained roughly one-third of the variation in disaster intensity (R² = 0.33), which is reasonable given the inherent noise in financial time series.

### 6. Model Validation and Crash Prediction
#### 6.1 Empirical vs. Model-Implied Moments
Comparing rolling skewness and kurtosis of observed returns with model-implied values showed that the PNM captured asymmetry (skewness) reasonably well but struggled to replicate fat tails (kurtosis). This limitation reflects the simplicity of the Type I specification, which allows mean shifts during disasters but keeps volatility constant.

#### 6.2 Crash Probability Calibration
Next, we evaluated the model’s ability to predict extreme events by computing the daily crash probability—the likelihood that returns fall below –5%. The predicted probabilities were broadly consistent with actual crash frequencies, but the relationship was not perfectly monotonic. This indicates that while the model captures broad patterns of risk, it cannot precisely time rare events.

#### 6.3 Welfare Implications
To assess economic significance, we simulated the certainty equivalent (CE) for a risk-averse investor under high- and low-sentiment conditions using a CRRA utility function. Results showed:

CE_low = –76.1%

CE_high = –65.5%

ΔCE = +10.6%

This means that during high-sentiment periods, the risk-adjusted welfare of a representative investor improves by about 10.6 percentage points. Optimism thus appears to reduce perceived disaster risk and increase expected utility, even though overall downside risk remains substantial.

Overall, the Poisson–Normal Mixture model offers a structured yet imperfect view of sentiment-driven market behavior—capturing shifts in perceived risk but missing the full complexity of real-world crash dynamics.

### 7. Key Insights and Interpretations
This project produced several meaningful findings:
1. Behavioral dynamics drive risk: Market risk is not purely statistical but also psychological. Periods of low sentiment coincide with elevated downside risk and frequent crashes.
2. Inverse sentiment–volume relation: Higher investor optimism tends to reduce trading activity — investors hold positions longer when confident.
3. Sentiment predicts future risk: Lagged sentiment negatively correlates with future downside semivariance and crash probability, hinting at predictive value.
4. Model limitations: The Poisson–Normal Mixture captures some aspects of disaster risk but underestimates tail thickness. Incorporating time-varying parameters or heavier-tailed innovations could improve accuracy.
5. Economic relevance: Despite its simplicity, the welfare analysis quantifies the tangible impact of sentiment — optimism may feel good but does not eliminate systemic risk.

### 8. Conclusion
This project looked at how people's feelings about Tesla on social media like Reddit are connected to its stock market risk.

We found that when investors online became more negative or fearful, it often happened right before the stock became more volatile and risky. On the other hand, when people were feeling optimistic, the stock market was usually calmer.

By using Poisson mixture model with data about online moods, this project shows that the emotions people express online can actually influence real-world stock prices.

In short, the stock market isn't just about numbers. It's also a social system where people's feelings and the way they share information play a huge role in determining a stock's value and risk.

### Reference
Yahoo Finance API — yfinance Python Library, https://pypi.org/project/yfinance/.

Kaggle Dataset: One Year of TSLA on Reddit, https://www.kaggle.com/datasets/pavellexyr/one-year-of-tsla-on-reddit.












