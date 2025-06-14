# Class 3: Interpretation of Machine Learning Model Results

This section interprets the results from the Machine Learning approaches (Prophet, XGBoost) applied to the AAPL adjusted close price data and compares them to the statistical models from Class 2.

## 1. Prophet Model

*   **Process:** The Prophet model was instantiated with default yearly and weekly seasonality enabled. The data was formatted correctly (ds, y), and the model was fitted to the training data.
*   **Prediction Failure:** An error occurred during the prediction phase: "Length of values (241) does not match length of index (252)". This mismatch likely happened because Prophet's `make_future_dataframe` with `freq='B'` (business days) generated a different number of dates than were present in the actual `test_ts` index over the same period (which might include holidays where the market was closed but Prophet predicted a value, or vice-versa). 
*   **Troubleshooting (for lecture):** This is a valuable teaching point about handling date indices carefully, especially when dealing with business day frequencies and potential holidays. Solutions could involve:
    *   Ensuring the `future_dates` dataframe generated by Prophet perfectly aligns with the target test set index *before* prediction.
    *   Using the test set's actual dates directly to create the `future_dates` dataframe instead of relying solely on `make_future_dataframe` with a frequency.
    *   Post-processing the forecast to align it with the test index (e.g., reindexing).
*   **Performance:** Due to the prediction error, RMSE and MAE could not be calculated for Prophet.
*   **Components Plot (`plot_16_prophet_components.png`):** Although the forecast failed on the test set, the components plot generated from the training fit is still useful. It would typically show the overall trend detected by Prophet, as well as the estimated yearly and weekly seasonal patterns learned from the data. This helps in understanding the model's internal view of the time series structure.

## 2. XGBoost Model

*   **Process:** An XGBoost Regressor was used. Feature engineering was crucial, creating time-based features (day of week, month, year, etc.) and lag features (past values of the price) and rolling mean features.
*   **Feature Engineering:** Lags (1, 5, 10, 21 days) and rolling means (5, 21 days) were created. It's important to create these on the full dataset before splitting to avoid lookahead bias within the training set, and then carefully split X and y. NaNs introduced by these features at the start of the training data were dropped.
*   **Training:** The model was trained using early stopping based on a validation set (taken from the end of the training data) to prevent overfitting.
*   **Performance (Test Set):**
    *   RMSE: 53.3728
    *   MAE: 50.9893
*   **Interpretation:** The XGBoost model produced a forecast, but its performance (RMSE ~53.4, MAE ~51.0) was significantly worse than the simple ARIMA(1,1,1) model (RMSE ~35.0, MAE ~31.6) and the SARIMAX model (RMSE ~35.3, MAE ~31.9) on this test set. This highlights that more complex ML models are not automatically better for time series forecasting, especially with default settings or basic feature engineering. The performance heavily depends on the quality and relevance of the features created.

## 3. Comparing Statistical vs. Machine Learning Approaches

Based on the results from this specific dataset and modeling exercise:

*   **Statistical Models (ARIMA/SARIMAX):**
    *   *Pros:* Relatively simple to implement for basic cases, provide strong theoretical grounding (stationarity, autocorrelation), often perform well as benchmarks, model summaries offer interpretability (coefficients, significance).
    *   *Cons:* Rely on assumptions (stationarity), can be challenging to manually identify optimal orders (p,d,q)(P,D,Q), may struggle with complex non-linearities or incorporating many exogenous variables easily.
    *   *Performance:* Provided the best forecasting accuracy (lowest RMSE/MAE) in this specific comparison (excluding the failed Prophet and Auto ARIMA forecasts).
*   **Machine Learning Models (Prophet/XGBoost):**
    *   *Pros (Prophet):* Designed for business time series, handles multiple seasonalities and holidays well, robust to missing data/outliers, often requires less manual tuning for seasonality/trend.
    *   *Pros (XGBoost):* Can capture complex non-linear relationships, easily incorporates many features (lags, time features, external data), powerful and often high-performing in ML tasks.
    *   *Cons (Prophet):* Can be a 
