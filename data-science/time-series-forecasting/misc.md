# Misc

## Log Transform

* If time series is multiplicative, use log tranform to convert the series into additive. This is because some   algo like ARIMA work better with additive timeseries
* In case there is 0 in time series, add 1 to prevent log(0) and then subtract 1 later

## Power Transform

## Box Cox Transform

* Combines both log and power transform
* Use lambda that minimise variance or constant variance
  * Guerrero method
  * MLE method

