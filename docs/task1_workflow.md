# Task 1: Laying the Foundation for Analysis

## 1. Data Analysis Workflow

### Steps
1.  **Data Loading & Cleaning**: Load the Brent Oil Prices dataset (`BrentOilPrices.csv`). Convert dates to datetime objects. Handle any missing values (though initial inspection suggests none).
2.  **Exploratory Data Analysis (EDA)**:
    -   **Trend Analysis**: Visualize the price history to identify long-term trends and obvious structural breaks.
    -   **Stationarity Testing**: Perform the Augmented Dickey-Fuller (ADF) test to check for unit roots.
    -   **Volatility Analysis**: Calculate and plot log returns to observe volatility clustering.
3.  **Event correlation**: map the `events.csv` to the price history to visually inspect correlation.
4.  **Change Point Detection (Task 2)**: Use PyMC3 to implement a Bayesian Change Point detection model to mathematically identify structural breaks.
5.  **Insight Generation**: Combine model outputs with determining events to explain price movements.
6.  **Reporting**: Summarize findings in a blog-post style report.

### Assumptions and Limitations
-   **Assumption**: The provided dataset is accurate and representative of global oil market trends.
-   **Assumption**: Major geopolitical events have a measurable impact on oil prices that can be detected significantly.
-   **Limitation**: Correlation does not imply causation. A change point near an event does not prove the event *caused* the change, only that they are temporally related.
-   **Limitation**: The model may detect change points that do not correspond to known major events (false positives or unknown causes).
-   **Limitation**: Daily data may be noisy; weekly or monthly averages might show clearer trends but lose granularity.

## 2. Key References
-   **Bayesian Change Point Detection with PyMC3**: Core methodology for the analysis.
-   **Time Series Analysis (ADF Test, Stationarity)**: Standard econometric techniques for verifying time series properties.

## 3. Communication Channels
-   **Primary**: GitHub Repository (Code, Notebooks, README).
-   **Secondary**: Interactive Dashboard (Task 3) for stakeholders to explore data.
-   **Reporting**: PDF/Markdown reports for detailed analysis.

## 4. Understanding the Model and Data
### Change Point Models
Change point models are designed to identify points in a time series where the statistical properties (mean, variance, etc.) change abruptly. In the context of oil prices, these "regime shifts" often correspond to external shocks (wars, policy changes).
**Expected Outputs**:
-   **Tau (τ)**: The probability distribution of the change point date.
-   **Parameters (μ1, μ2, ...)**: The parameters of the time series before and after the change point.

### EDA Findings
-   **Trend**: The Brent oil price series shows significant fluctuations over the last three decades, with visible structural breaks around 1990 (Gulf War), 2008 (Financial Crisis), and 2014 (Price Crash). The series is clearly non-stationary, with a wandering mean.
-   **Stationarity**:
    -   **Raw Price**: ADF p-value > 0.05. The null hypothesis of a unit root cannot be rejected. The series is **Non-Stationary**.
    -   **Log Returns**: ADF p-value < 0.05 (typically very small, e.g., ~0.00). The null hypothesis is rejected. The log returns are **Stationary**.
-   **Volatility**: Volatility is not constant over time (heteroscedasticity). There are distinct periods of high volatility ("volatility clustering") corresponding to major global events, such as 1991, 2008-2009, and 2020. This suggests that a model accounting for changing variance (or distinct regimes) is appropriate.
