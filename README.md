# WNBA Game Attendance
## The New York Liberty

### Overview
WNBA viewership is on the rise; the 2022-2023 season was the highest viewed season in WNBA history, with an average of 379 thousand viewers. Game attendance is also on the up, after a steep decline in 2020 thanks to Covid and a postponed season that ended up in the Wubble, where no fans were able to attend games, with the condensed season being strictly televised. With this growing interest, the New York Liberty is curious how their game-by-game attendance for the upcoming season will be affected. They have tasked their data team with forecasting game attendance so that other cross-functional teams, such as marketing, ticketing, concessions, etc., can plan for the upcoming 2023-2024 season accordingly. This is going to be achieved using Time Series.

### Business and Data Understanding
Viewership data was hard to come by, so to obtain the necessary game attendance data, I scrubbed game-by-game attendance tables for every team in the WNBA since the league started back in 1997 from https://acrossthetimeline.com/. Each team's data was saved into individual CSV's, from which I then pulled the information specific to the New York Liberty, which I then concatenated with the New York Liberty data set I had created, making a new dataset of every game, home or away, that they had ever played, which I turned into a CSV to be pulled into a notebook. 

Beyond scrubbing the data to create my own dataset, there were a few mre things that needed to be done in order to prep the data for modeling. I dropped all the unecessary columns within the dataset, then enacted datetime to the dataframe and set this as our new index. Any null values were then turned int NANs and dropped, then lastly as each season spans just a few months out of the year, I interpolated the data so that we would be dealing with a consistent line. Once the series was ready, I tested for stationarity, which the data wasn't too bad! But I still decided to try each of the transformers to see which gave us the most stationary dataset. 
This included:
- Log Transform
- Square Root
- Subtracted Rolling Mean
- Differenced
I found that the differenced dataframe got us the closest to '0' (with a p-value of 2.098931e-23), so I ran with that dataframe.

### Modeling
Once I had landed on the dataframe that was ready for modeling, I checked for partial correlation and autocorrelation to helpm me narrow down the best models to move forward with. First, as my baseline, I ran a Naive model, shifting the data by one game, which gave me MSE(mean squared error) of 32.5, so not a bad start! Next, based on the partial correlation, I ran an AR (Auto Regressive) model, with 17 lags, whcih returned and MSE of 14.7, cutting our baseline by more than half! Finally, based on the autocorrelation I ran an MA (Moving Average) model, with 3 lags, which brought our MSE down just a tad to a 14.6. This seems like the best model to move forward with, but I decided to run a SARIMAX just to be sure that this model did indeed contain the best possible parameters. From the SARIMAX, it is suggesting that I use: order = (0, 0, 2), seasonal_order = (0, 2, 2, 12). We will run this and see what happens!

### Evaluation
After determing the best model, I ran some diagnostics on my differenced data. Their didn't seem to be any obvious seasonality and the correlelogram inferred that the time series residuals have low correlation with lagged versions of itself, while the density plot and Normal Q-Q plot showing us that the residuals are normally distributed.
Based on these observations it seems that we have a satisfactory fit to help forecast future values.

To validate the model, I ran a forecast, but did so with the parameters that the SARIMAX suggested, but unfortunately it came out with a higher MSE, so then I ran it gaain for my best model, which resulted in null values. Think I'm going to have to run a broader SARIMAX to find the best oevrall parameters before we can run a successful forecast.

### Conclusion
Based on my best model, we can pretty accurately predict how many attendees we can expect for any game, with a fairly minimal error. Next I just need to nail down the proper parameters so that we can forecast our game attendance!

Some other factors to keep in mind, which could really run up the attendance numbers, are the amazing players that the Liberty have brought onto the team this season. So definitely want to look into how that could affect our data, as well as do some reasearch into the drops in theintial data to see what could've possibly affected a dip in attendance (besides Covid, of course). As well, would like to play with some other ways to fill off-season data, maybe even run some models on monthly or weekly data, or potentially pull information on leagues that the teams/players take part in outside of the regular season.

Overall, we will be able to help our cross-functional partners plan out what they need for the upcoming season.