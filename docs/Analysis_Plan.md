# Analysis Plan: Brent Oil Price Change Point Detection

## 1. Defining the Data Analysis Workflow

### 1.1. Outline of Analysis Steps

1.  **Project Scoping and Objective Clarification:**
    *   **Goal:** Identify significant change points in Brent oil prices and associate them with major events.
    *   **Scope:** Daily Brent oil prices from May 20, 1987, to September 30, 2022. Focus on geopolitical events, economic shocks, and OPEC decisions.
    *   **Stakeholders:** Investors, policymakers, and energy companies.
2.  **Data Acquisition and Preprocessing:**
    *   Load `BrentOilPrices.csv`.
    *   Convert 'Date' column to datetime objects.
    *   Handle any missing values (e.g., interpolation, forward/backward fill, or removal).
    *   Check for outliers and decide on a handling strategy.
    *   Consider analyzing log returns: `log(price_t) - log(price_{t-1})` for stationarity.
3.  **External Event Data Research and Compilation:**
    *   Research major geopolitical events, OPEC decisions, and economic shocks relevant to the oil market from the past decade (approximately 2012-2022).
    *   Compile a structured dataset (`events.csv`) with at least 10-15 key events, including their approximate start dates and descriptions.
4.  **Exploratory Data Analysis (EDA) for Brent Oil Prices:**
    *   Plot the raw 'Price' series over time to visually identify major trends, shocks, and periods of high volatility.
    *   Plot log returns to observe volatility clustering.
    *   Perform statistical tests for stationarity (e.g., Augmented Dickey-Fuller test, KPSS test).
    *   Analyze autocorrelation and partial autocorrelation using correlograms.
5.  **Change Point Model Selection and Justification (Bayesian Approach with PyMC):**
    *   **Model:** Bayesian Change Point Model.
    *   **Rationale:** Bayesian methods provide a probabilistic framework for uncertainty quantification in change point detection, allowing for more nuanced interpretations. PyMC is a robust library for probabilistic programming.
6.  **Model Implementation and Training:**
    *   Define the Switch Point (tau) as a discrete uniform prior over all possible days.
    *   Define "Before" (μ₁) and "After" (μ₂) parameters with appropriate priors (e.g., Normal distributions).
    *   Use `pm.math.switch` to select the correct parameter based on the time index relative to tau.
    *   Define the Likelihood using `pm.Normal` distribution where the mean is determined by the switch function.
    *   Run the MCMC simulation using `pm.sample()`.
7.  **Interpretation and Validation of Model Output:**
    *   Check for MCMC convergence using `pm.summary()` (r_hat values close to 1.0) and `pm.plot_trace()`.
    *   Identify the change point by plotting the posterior distribution of tau.
    *   Quantify the impact by plotting posterior distributions for before/after parameters and making probabilistic statements about price shifts.
    *   Associate detected change point dates with the researched list of key events.
8.  **Reporting and Communication:**
    *   Prepare a comprehensive report (blog post format) including methodology, findings, visualizations, and quantified impacts.
    *   Develop an interactive dashboard (Flask backend, React frontend) for stakeholders to explore results.

### 1.2. Research and Compile Event Data

**Categories of Events to Research (Past Decade, approx. 2012-2022):**

*   **Geopolitical:** Major conflicts, sanctions, political instability in oil-producing regions.
*   **Economic:** Global economic slowdowns/recoveries, financial crises, significant policy changes.
*   **OPEC+ Decisions:** Production cuts, increases, or changes in quota agreements.
*   **Major Supply/Demand Shocks:** Disruptions due to natural disasters, significant changes in global demand.

**Deliverable:** A structured CSV/tabular data file (`events.csv`) with at least 10-15 key events, including their approximate start dates and descriptions.

### 1.3. State Assumptions and Limitations

**Assumptions:**

*   Brent crude oil prices are a reasonable proxy for global oil market dynamics.
*   Major geopolitical and economic events have a discernible impact on oil prices.
*   The Bayesian change point model is capable of identifying significant structural breaks in the time series.
*   Historical data is sufficiently accurate and representative.

**Limitations:**

*   **Correlation vs. Causation:** Identifying a statistical correlation in time between an event and a change point does not definitively prove a causal impact. Other unobserved factors might be at play. We will aim to formulate hypotheses about causal links but acknowledge the complexity.
*   **Event Data Granularity:** Event dates may be approximate, and the exact timing of market reaction can vary.
*   **Model Simplification:** The initial model (change in mean) is a simplification. Real-world price dynamics are complex and influenced by many variables beyond a single mean shift. Advanced models might be needed for deeper insights.
*   **Exogenous Factors:** It is challenging to account for all possible external influences on oil prices.
*   **Data Period:** The analysis is limited to the available historical data (up to September 30, 2022), meaning more recent events are not included.

### 1.4. Determine Communication Channels

*   **Primary:** A comprehensive "Final Report" (blog post format) detailing analysis, findings, and recommendations.
*   **Secondary:** An interactive "Dashboard" (Flask + React) for visual exploration of trends, change points, and event correlations.
*   **Internal:** GitHub repository for code, documentation, and task tracking. Slack channel for team communication.

## 2. Understanding the Model and Data

### 2.1. Review Key References

*   **Data Science Workflow:** Resources like `www.datascience-pm.com/data-science-workflow/` will guide the overall project structure.
*   **Change Point Analysis:** Articles such as `https://forecastegy.com/posts/change-point-detection-time-series-python/` and academic papers on structural breaks.
*   **Bayesian Change Point Detection with PyMC:** Specific PyMC tutorials and examples will be crucial.
*   **Bayesian Inference and MCMC:** Foundations from `https://warwick.ac.uk/fac/sci/statistics/staff/academic-research/steel/steel_homepage/bayesiantsrev.pdf` and other resources.

### 2.2. Analyze Time Series Properties of Brent Oil Price Data

*   **Trend Analysis:** Visually inspect plots to identify long-term upward, downward, or stable trends. Use rolling statistics (mean, variance) to quantify.
*   **Stationarity Testing:**
    *   **Definition:** A stationary process has constant mean, variance, and autocorrelation structure over time.
    *   **Importance:** Many time series models assume stationarity. Non-stationarity often implies structural breaks, which change point analysis aims to detect.
    *   **Tests:** Augmented Dickey-Fuller (ADF) test, Kwiatkowski-Phillips-Schmidt-Shin (KPSS) test.
*   **Volatility Patterns:** Observe periods of high and low price fluctuations. Look for volatility clustering, where large changes tend to be followed by large changes, and small by small. This can be visually assessed from log returns plots.
*   **Informing Modeling Choices:** These properties will help decide if transformations (e.g., differencing, log transformation) are needed before modeling, and will justify the use of models robust to non-stationarity or designed to detect changes in these properties.

### 2.3. Explain Change Point Models

**Purpose:**

Change point models are statistical tools used to identify points in time where the statistical properties of a time series undergo a significant, abrupt shift. In the context of Brent oil prices, they help:

*   **Identify Structural Breaks:** Pinpoint specific dates when the underlying dynamics of oil prices changed.
*   **Contextualize Price Fluctuations:** Link these changes to real-world events, providing a deeper understanding of market behavior.
*   **Inform Decision Making:** By understanding when and why changes occur, investors, policymakers, and energy companies can make more informed decisions regarding risk management, policy adjustments, and operational planning.

**How they help identify structural breaks:**

Change point models essentially segment a time series into different regimes. Each regime is assumed to have distinct statistical characteristics (e.g., a different mean, variance, or trend). The points at which these regimes shift are the "change points" or "structural breaks."

### 2.4. Describe Expected Outputs

The expected outputs of a change point analysis using a Bayesian approach (e.g., with PyMC) include:

*   **Posterior Distribution of Change Points (τ):** Instead of a single "best" change point, the model will output a probability distribution over all possible change point locations. A sharp peak in this distribution indicates high certainty about the location of a change.
*   **Posterior Distributions of Parameters (e.g., μ₁, μ₂):** For models that identify changes in parameters (like the mean), we will get a distribution for the parameter values before and after each detected change point. This allows for quantifying the magnitude and uncertainty of the shift.
*   **Dates of Changes:** Specific dates corresponding to the most probable change points.
*   **Quantified Impact Statements:** Probabilistic statements describing how much a specific parameter (e.g., average price) changed around a detected event. For example, "There is a 95% probability that the average Brent oil price increased by $X to $Y after [Date]."
*   **Visualizations:**
    *   Raw price series with detected change points highlighted.
    *   Posterior distributions of change points and model parameters.
    *   Trace plots for MCMC convergence diagnostics.

**Limitations of Expected Outputs:**

*   **Uncertainty:** While Bayesian methods quantify uncertainty, there will always be some level of uncertainty around the exact change point locations and parameter estimates, especially with noisy data or subtle shifts.
*   **Model Assumptions:** The outputs are conditional on the assumptions of the chosen model (e.g., a change only in mean). If these assumptions are violated, the interpretations might be misleading.
*   **Interpretation Complexity:** Interpreting posterior distributions and making probabilistic statements requires a good understanding of Bayesian statistics.
*   **Causality:** The outputs will highlight *when* changes occurred and *what* the statistical impact was, but they do not directly prove *why* a change happened. Associating changes with causes requires external event data and careful hypothesis formulation.
