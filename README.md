# predicting-air-pollution-climate
My 5-week capstone project whilst doing General Assembly's Data Science Immersive bootcamp in London. I used climate data from years 2011 to 2020 to predict PM10 and PM2.5 air particulates concentration in London Kensington. I used various regression models and climate data from WeatherSource and particulate data from uk.gov's AURN archive.
## Motivation
Air Pollution is a big contributor to the deaths which occur in the UK as per World Health Organisation,  air pollution caused the deaths of 7 million people*[1] in the year 2016, with Africa having the worst effects on it. 
“In the UK it causes 40,000 premature deaths each year”[2].
“29,000 of those are from the particulate matter PM2.5, particulates less than 2.5micrometers.”[2]
While it is a well established fact that fumes from cars and industrial combustion processes are a leading contributor to concentration levels, but looking much more deeply at the concentration levels reveal a much more volatile state.
[1]: https://apps.who.int/gho/data/node.sdg.11-6-viz?lang=en
[2]: https://wintoncentre.maths.cam.ac.uk/news/does-air-pollution-kill-40000-people-each-year-uk/

## Introduction
In this project I start by taking particulate matter concentration data PM10, PM2.5 & Nitrogen Oxide (NO) from the Automatic Urban and Rural Network (AURN), the largest automatic monitoring network measuring air pollution.
PM10 & PM2.5 are particulate matters of size 10 micrometers and 2.5 micrometers (thats 10 and 2.5  billionth parts of a meter). I then take climate data credit to WeatherSource for making available the data.

*Null Hypothesis : PM particle fluctuation is controlled by the weather and therefore controlled by nature.*

*Alternative Hypothesis: Change in concentration is due to a sustained and controlled effort to reduce air pollution and also voluntary human activity.*

If the score I obtain for the model is > 0 aka the baseline score, then depending on the magnitude we can induce to which degree the changing weather has an influence on the concentration levels.

## The process
* Data Acquisition *Credit Weather-Source
* Cleaning
* Feature Engineering+Merging
* Exploratory Analysis
* Model+Troubleshooting & Further feature engineering
* Limitations & Cause for improvement.

### Data Acquisition
![AURN webpage](predicting-air-pollution-climate/images/AURN-web.png)
The data was acquired by visiting uk-air.defra.gov.uk and in the data archive section. There I was able to select what concentration type that I wanted and also the data range that the concentration was going to be for. The measuring point was at London Kensington. Extracting the data was a quick and easy process (also free!). The main drawback to this though that the csv was limited to around 90000x152, this broadly corresponds to about 10 years of **hourly averaged** concentration data. Anyways, I chose to pick PM10, nitric oxide and PM2.5 concetrations 10 years from 12/05/2020.


The next step was acquiring climate data and this was done through WeatherSource's weather-data. Location was London Kengsinton and the date corresponded to 01/01/2011 up until 12/05/2020. The API is charged but luckily they were kind enough to provide this for free to me. 
### Data Cleaning
Examined the air pollution data first and proceeded to change the columns. Then I checked for NaN values. 
|column|null_count|
|------|----------|
|date|0|
|time|0|
|PM10|0|
|PM2.5|0|
|nitric_oxide|0|
|temp|0| 
|wind_speed|0|
|wind_direction|0|

No NaNs so I looked at column datatype and they were all objects. So I looked at like the first 50 entries and found a 'No data' entry. The dataframe had 6000 such entries that were scattered across the whole dataframe. I decided to fill these in by propagating the next available value above forward using ffill. First though I had to convert cells containing 'No data' into NaN because ffill fills NaN values only. For example applying this to the nitric_oxide column,

df.nitric_oxide.map(lambda x: x if x!='No data' else np.nan),

and then filling this with ffill solved the problem. Then I converted date and time columns into DateTime and TimeDelta formats respectively, and an additional column 'date-time' was engineered by combining the aforementioned formatted column together. 

Removing NaN values in the climate dataframe was alot simpler as there were NaN values that I found by checking. I simply just used ffill to replace them.

### Feature Engineering and Merging
I calculated the average concentration just focussing on PM data because I found in the EDA stage that nitric oxide changes quite differently to PM. I converted the temperature columns from farenheit into degrees celcius using a simple function mapping this to all temperature columns. 

I took days of the week out from the date column using .day_of_week() method and mapped this a new column called 'day_of_week'. Then I mapped the month,year and day from the date column and from the month I produced a season column. This was quite simple to implement, if month lies in [12,1,2] return Winter and [3,4,5] return Spring so on. I also extracted wind directions from the wind_Dirc column. I chose 10m above seawater because these air particulates are closer to us so affect us more ultimately than air particulates much further up in the air.

Merging process was a straight forward process. I first sorted the dataframes in ascending order by date-time then I set this as the index. Then I compared all dataframes interms of row count and found that one of them, the climate dataframe, had to be resized down. Both dataframes were then combined using concat inner join on date-time.

### Exploratory Analysis
Heatmap was interesting to see. The wind variables have a negative correlation with concetration and most notably the temperatures have a positive correlation, around .34

![correlations](predicting-air-pollution-climate/images/corr.png)


### Modelling

### Conclusion

### Limitations & Cause for improvement
