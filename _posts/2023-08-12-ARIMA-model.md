---
title: "An ARIMA model of the global average temperature"
layout: post
---
I wrote an ARIMA model to predict the average global temperature.

I would not call it a climate model because it only covers one of the many aspects of the climate. Of course, it is much more limited compared to those developed by experts in the field. Still, writing it taught me a lot about forecasting of time series.

Today, I would do some things different. In particular, I probably would discourage people who use ARIMA models from interpolating missing data points.
The ARIMA model can still be used when some data is missing. I used interpolation originally because I also experimented with ETS models, who require a time series without gaps. But the ETS model is not in the published version of the code because its predictions were not very good.

The code (with extensive commentary) can be downloaded [here](https://github.com/InaKrapp/Global_temperature_ARIMA_model). It is written in a quarto document and should run on any relatively recent version of R and Rstudio. 
The [Global Temperature.txt](https://github.com/InaKrapp/InaKrapp.github.io/blob/master/_posts/Global_Temperature.txt) and [merged_ice_core_yearly.csv](https://github.com/InaKrapp/InaKrapp.github.io/blob/master/_posts/Global_Temperature.txt) files contain the data the model uses, so they have to be downloaded to run the code as well.
For anyone who just wants to take a look at the results and doesn‘t want to run or modify the code themselves, here is the [pdf version](https://github.com/InaKrapp/InaKrapp.github.io/raw/master/_posts/Global_Temperature_prediction_model.pdf).
