# Airbnb-COVID-19-Data-Analaysis
## Table of Contents
**Introduction**  
1.	 Data   
2.	 Datasets Overview   
3.	 EDA
4.	 Assumptions
5.	Covid in San Francisco

**Market Supply**

**Market Demand**

**Revenue Analysis**

**Exit Listings**

**Text Mining**
1.	Sentiment Analysis
2.	Topic Modeling 

**Conclusion**

**Appendix**

---
## Introduction
For this project, we are looking into the Covid-19 impact on the San Francisco Air BnB market. San Francisco was one of the first cities in the US deeply affected by Covid-19, with its first case on February 3 and an official lockdown order going into place on March 16. The lockdown remained in effect until June 1. This caused many small businesses, particularly in the travel and tourism industry, to have to shut down for extended periods of time. Because of this, we wanted to analyze how the pandemic affected the Air BnB market along with the projections for the future and which types of listings are most likely to survive through the pandemic without closing their doors.
Through this, we are looking to answer the following questions:

●	How does the COVID 19 pandemic affect the selected city’s Airbnb market?

●	What factors have affected Airbnb hosts’ market exit decisions?

●	What types of properties have been affected the most?

As a note, because of the amount of code generated along with the current length of the report, exact codes for replication are included within the associated code files folder. All files are named respective to the section they cover in the report.

**Data**

AirBnB data was sourced from: http://insideairbnb.com/get-the-data.html
For this project, datasets were used from the Calendar, Listings, and Reviews gz files scraped in October 2020. 
Additional reviews and listings files were scraped from December 2020 as more information became available.

Calendar Dataset

The calendar dataset has 7 columns: listing_ID, date, available, price, adjested_price, minimum_nights, and maximum_nights. No columns are unique identifiers. Available is a binary category indicating whether a listing is available “t” or already booked “f”. No columns were removed from the calendar datasets before pre-processing. 

The calendar data files display availability information per listing per day from the day of the scrape through one year into the future. Because of this, historical booking data was unavailable. As a proxy, Calendar gz files were taken every month from December of 2018 through October of 2020. Availability information was preserved solely from the date of scrape to the date before the following scrape, preventing duplicate data from being included inside the data frame as shown below:

![](images/Picture1.png)

However, it is important to note that because this booking information is forward-facing, information such as cancellations and last-minute bookings are unavailable. Thus, booking information is calculated from bookings taken between 0-30 days in advance of each scrape.

A time series of January 2019 through October 2020 was selected in order to provide a solid comparison of the 2020 data to a typical trend. The addition of the whole of 2019 also provided key information to input into our time-series analysis for prediction 180 days in advance.

![](images/Picture2.png)

Listings Dataset

The Listings dataset has 73 columns which describe about the attributes of the individual listings. Each listing is identified by its unique ‘ID’. The attributes of the listings can be known by the columns ‘description’, ‘listing_url’, ‘picture_url’. The ‘latitude’ and ‘longitude’ columns tell us about the location of the listing. The price for each listing is mentioned under the ‘price’ column. All the amenities provided by the listing are aggregated and mentioned in a separated column. 
Each listing has ‘host_id’ which gives the ID of the host. There are several columns that describe the characteristics of the host such as name, their location,their verification status, when they joined Airbnb, what their response and acceptance rate is, how many other listings are registered by the same host and binary column indicating if the host is superhost or not. 

There are few columns that describe the neighbourhood of the property and few others that describe the room type, accommodations, number of beds, bedrooms and bathrooms. Availability columns indicate the days the listing is available for the next 30, 60, 90 and 365 days. Columns ‘minimum_nights’ and ‘maximum_nights’ tell us about the number of minimum and maximum nights the listing can be booked.

The reviews columns give information about the dates of the first and last review, total numbers of reviews from the begin date and reviews in the last month. Each listing has review scores based on rating, accuracy, cleanliness, checkin, communication, location and value. These ratings are scored in the range of 2-10. 

Column ‘calculated_host_listings_count’ gives the value which is the actual number of host listings. This column is further split based on the type of listing such as entire homes, private rooms and shared rooms. 

Reviews Dataset

The reviews file has 6 columns. The columns would be listing_id, id,date, reviewer_id, reviewer_name, comments. The listing_id column is the id of the which is unique to every listing.The id column is the id which uniquely identifies each record of the dataset. The date column has the date as to when the customer has given a review to the listing he has stayed.The reviewer_id and reviewer_name are the attributes of each reviewer where in the former attribute gives an id which uniquely identifies each customer and the later gives the name of the reviewer. The column comments is the review given by each customer for their stay at a place. For text mining, our focus is more on the comments column for performing sentiment analysis and topic modeling.

Covid Dataset

Covid data is taken from DataSF. 
File used: COVID-19_Cases_Summarized_by_Date__Transmission_and_Case_Disposition.csv  This file does include covid information until 10th Dec 2020. Original file has 5 columns “Specimen Collection Date, Case Disposition, Transmission Category, Case Count, Last Updated At”.  The column ‘Case Disposition’ has two values - Confirmed and death. Value ‘Confirmed’ represents confirmed covid case and value ‘death’  represents covid death.  


We have done some data preprocessing on the file above before reading

•	Filled missing dates with 0 records(very few at the start), filled those dates with 0 cases and deaths.

•	Based on the column ‘Case Disposition’, new columns are created 

   	Column with values ‘Confirmed’ are taken as New_cases for that date

   	Column with values ‘Death’ are taken as New_Deaths for that date

•	New columns ‘Cumulative_Cases’, ‘Cumulative_Deaths’ are created by accumulating the values of ‘New_Cases’ and ‘New_Deaths’ respectively


Final Covid dataset after preprocessing:
Upon preprocessing, new file has the following columns:
Date, New_Cases, Cumulative_cases, New_Deaths, Cumulative_Deaths

Final Datasets
Calender_Merged was created by merging the calendar dataset with the selected columns from the Listings dataset by Listing_ID. Because the listings file was not spliced by month, all of the merged columns from that table are stagnant by date. The only columns that change over time are from the calendar dataset.

![](images/Picture3.png)

Reviews_Merged was created by merging the reviews dataset with the selected columns from the Listings dataset by Listing_ID. 

**EDA and Initial Preprocessing**

Initial EDA was done on the listings file. This would us in deciding the significant features and the the ones that need to be dropped due to their redundancy to the final model.

![](images/Picture4.png)

There were few columns such as bedrooms, calendar_updated that had entirely missing values. We decided to drop all of them as they would be redundant to be included in the model

![](images/Picture5.png)

The correlation was done on the listings file and we could observe multicollinearity between a few features such as  maximum_nights and maximum_maximum_nights. We decided to drop such columns that were highly correlated as it would not be good for the model if included.

![](images/Picture6.png)

From the has_availability column, we can observe that there were only listings that were available and not booked.

![](images/Picture7.png)

From the above EDA, we can observe that there were more listings that included Entire home/apt,  Private rooms and a few number of hotel rooms. This would be useful in explaining the effects Covid has on airbnb further.

After careful analysis and EDA done on the listings file, we could come up with a few data preprocessing steps as follows:

●	Calender_updated, neighbour_group_cleansed, bathrooms were dropped due to containing all missing values

●	listing_url and host_url were dropped as we already have their respective ID columns 

●	scrape_id was dropped due to being a constant

●	last_scraped was dropped due to its insignificance to the model

●	calendar_last_scraped was dropped as it is same as the last_scraped column

●	maximum_maximum_nights, maximum_nights_avg_ntm were dropped as we already have maximum nights column

●	minimum_minimum_nights, minimum_maximum_nights, minimum_nights_avg_ntm were dropped as we already have minimum nights column and would be redundant to include

●	has_availability, availability_30, availability_60, availability_90, availability_365 were dropped as we already have availability by date

**Assumptions**

Because the calendar and listings datasets are forward-facing, a few assumptions needed to be made. These are as follows

●	Booked rooms are indicative of room stays

●	Reviews are indicative of room stays

●	Exited listings can be defined as a listing which has not had a review in the past three months


**Analysis - Covid in San Francisco**

New cases per day

![](images/Picture8.png)


Covid cases raisen to 200+ numbers per day since 1st December. Top 3 months with the highest number of cases recorded in a day are December(297), November(275), July(163). 

SF Covid cases per month

![](images/Picture9.png)


Joy plot above shows the distribution of covid cases per month in SF. July and November are the months with the highest number of cases. 

![](images/Picture10.png)


Overall, total number covid cases crossed 18000 by the first week of december.  Better month in terms of the number of covid cases is May with the lowest number of cases of 880. The graph shows that covid cases are on the rise in the months of July(3262), November(3633) and December(by 10th dec- 2305).

SF Covid Deaths per day

![](images/Picture11.png)


Month of October recorded 5 deaths in a day. 

Cumulative Covid Deaths - per month

![](images/Picture12.png)


Overall, total number covid deaths crossed 160 by the first week of December. Covid deaths are high in September(31) followed by April(26) and October(26). The better month in terms of covid deaths is June, with 4 people lost lives to covid.

Covid cases - Pairplot by month

![](images/Picture13.png)

![](images/Picture14.png)


On average, number cases per day are high in the months of July, November and December.
On average, the number of deaths are high in September(31) followed by October(26).

AirBnB Data Analysis

SF hosts joining Airbnb and listings getting their first review in each month.

![](images/Picture15.png)


As depicted in the graph, hosts joining airbnb have significantly dropped in the covid period. Which also indicates that new listings and that listings getting their first review is dropped as well. The last date any host joined Airbnb is on 2020-08-25.

Monthly reviews per listing

![](images/Picture16.png)


This graph shows the highest number of reviews any listing received in the respective months. As you can see, review data is divided into two sets - pre covid(till Feb2020) and covid(From Mar 2020) periods. This graph also shows that the highest number of reviews any listing received after covid is way below pre-covid period, which reiterates the negative impact of covid on the airbnb market. 

Time Series Forecasting

Time series forecasts were created by performing data aggregation by date to determine the daily number of listings along with the number booked. From here, the Facebook Prophet model was used to determine what can be expected moving forward for both the market demand and supply for the next 180 days.

---
## Market Supply

Market supply was determined as the total number of listings per month, regardless of whether the availability was denoted as true or false. The underlying assumption for this is that if a listing is taken off of the market, it will no longer be shown within the dataset.

The chart below shows the total number of listings by day, along with the projected number of listings by month for the next 180 days.

![](images/Picture17.png)

In this chart, the black lines are our actuals, the blue line is the yhat value, and the light blue shaded areas are our 85% confidence interval. Surprisingly, we do not see a drop in the number of listings by month in our dataset. Instead, we see a fairly consistent trend upwards in terms of the number of Air BnB supply numbers.

In order to determine if this trend was consistent across the board, the data was then subsetted by room type. 

![](images/Picture18.png)

![](images/Picture19.png)

![](images/Picture20.png)

![](images/Picture21.png)

From the above daily listings summaries and prophet model, it appears that Covid-19 has had almost no impact in the upward trend in total listings for the category “entire home/ apt.” This is likely due to the hosts having little to no contact with the listing space, having much lower risk than the other categories. While the category “private room” also sees an increase in listings through the entire timespan of our data, there is a distinct slowing of the listing increase after about February 2020. Shared room listings also seem, surprisingly, to be on the rise post-covid. Hotel rooms appear to have taken a dive from January through May but has been on the rise since.
In addition, models were generated by subsetting the data by neighborhood. Below is a graph showing the overall number of listings per neighborhood as of October 2020. 

![](images/Picture22.png)

Graphs are shown below for the four neighborhoods with the highest count of listings over the course of 2019-2020. From the visualizations, the number of active listings are increasing at a steady pace across neighborhoods. Additional models were constructed for other neighborhoods, but those did not display a difference in trend either, and thus were not included in the report.

![](images/Picture23.png)

![](images/Picture24.png)

![](images/Picture25.png)

![](images/Picture26.png)

Market supply - Total percent of listings available

The graph below shows the percentage of listings available for bookings per day that have not been booked. This is inversely related to the number of bookings. It is showing an increasing trend from December 2019 which consistently increased until June 2020.  It has slightly decreased since then, but overall many listings are available for booking during covid period compared with pre-covid. 

![](images/Picture27.png)

## Market Demand

For market demand, we first looked into the overall count of daily bookings over time from 2019 through 2020.

![](images/Picture28.png)

From the above trends, it is clear that bookings for the months of January and February were high in 2020 when compared to 2019. Whereas in the following months, the count of the bookings is similar or decreasing compared to 2019. However, we could see that from October bookings have begun to pick up.
However, the above chart is confounded in the sense that there is a trend of increasing listings available over time. Because of this, we then looked into occupancy rate, defined as the number of daily listings booked with an availability of “f” was divided by the total number of listings for that day. The chart on the left demonstrates the pattern in occupancy rate over time, while the one on the right shows the predictive booking percentage over the course of the next six months from the Prophet model.

![](images/Picture29.png)

![](images/Picture30.png)

From the above charts, it appears that August 2019 has a peak for the percentage of listings booked. Due to normal seasonality, it would be assumed that, without Covid, 2020 would follow a similar trend to 2019, with the highest percentage of bookings in August, with a low in January. However, we instead see a consistent decline in bookings, with the lowest percentage of listings booked in June of 2020. This indicates that starting in February 2020, potential customers are starting to become anxious about taking trips to San Francisco. Lock down orders start to be put in place from there, also contributing to a decline in bookings.
Once July of 2020 hits, there is a steady increase in bookings through October, indicating that consumers are now becoming more likely to book. The model also indicates that this rise will continue throughout the next few months, regardless of the increase of Covid cases during that time.
As with the market supply, booking percentage was then divided by room type.

![](images/Picture31.png)

![](images/Picture32.png)

![](images/Picture33.png)

![](images/Picture34.png)

All of the produced graphs seem to follow a similar pattern of having a decrease in bookings starting in August 2019 and an increase after May-June 2020. The “entire room/apt” appears to have bounced back the most with a steady increase in booking percentage similar to that pre-covid. As expected, private rooms are increasing in booking percentage, but at a much slower rate when compared with “entire room/apt.” Hotel rooms follow a very unexpected trend of increasing in bookings since March of 2020, while shared rooms are also seeing an unexpected sharp rise in booking percentages. However, it is important to note that very few of the AirBnB listings are shared rooms and hotel rooms, meaning that the data may not be as standardized as the information for the other categories. 

Daily Percent of AirBnB’s booked by neighborhood

Below are the four neighborhoods with the most listings. Unsurprisingly, listings in downtown have a lower percentage of bookings compared to pre-covid. However, most of the other neighborhoods follow a similar pattern to the overall trend of booking percentage being on the rise since about June 2020. It seems that while individuals are hesitant to book places to stay downtown, they are less likely to be wary of the nearby neighborhoods. 

![](images/Picture35.png)

![](images/Picture36.png)

![](images/Picture37.png)

![](images/Picture38.png)

![](images/Picture39.png)

![](images/Picture40.png)

Additional charts for the number of bookings by day can be found in the appendix. However, they are not included as they do not provide any additional insight on the market supply and demand.

 
Market Demand By Reviews

In addition, market demand was also calculated by the number of reviews per month, as shown below:

![](images/Picture41.png)

From the number of reviews, there is a huge dip between March and June across the board. The reason this exists for this chart and not the bookings data is because the bookings data does not contain cancellations. This is in line with the San Francisco stay at home order, lasting from March 16 through May 31 2020. This is divided into room type below:

![](images/Picture42.png)

![](images/Picture43.png)

![](images/Picture44.png)

![](images/Picture45.png)

Number of reviews for the private room and entire home/ apt have been on the rise since June 2020, with the entire home/ apt increasing at a faster rate. In contrast, shared room and hotel room have not recovered since Covid-19.
After this, analysis was also conducted for the neighborhoods with the most reviews.


![](images/Picture46.png)

![](images/Picture47.png)

![](images/Picture48.png)

![](images/Picture49.png)

![](images/Picture50.png)

![](images/Picture51.png)

All neighborhoods seem to have a steady increase in reviews since the lockdown order. However, neighborhoods away from downtown, such as Bernal Heights and Upper Market appear to be bouncing back quicker than downtown and nearby neighborhoods.

 
Analyzing Revenue (Sales)

To further explore the monthly revenue generated from the San Francisco market, we plotted the projected income generated from bookings for both the years 2019 and 2020. For this, we have used the merged dataset of both listings and calendar and calculated the monthly income from the price column.


![](images/Picture52.png)

![](images/Picture53.png)

From the Revenue bar chart, it is visible that in 2019 the monthly revenue is gradually increasing from January and reaches maximum in the year end. Under normal circumstances, a similar trend would be seen for 2020 as well. But we could see that the year 2020 began very well in January making great revenue but started to decline from February followed by March and so on which could clearly be the impact of lockdowns that were announced during COVID 19. The sales/revenue again began to pick up slowly in the following months.

From the above charts, an interesting observation that can be seen is that although overall bookings seem to have gone down, the income generated from bookings in the year 2020 are still higher when compared to 2019.  It is also important to note that the current dataset does not consider cancellations, something that inevitably occurred more often in 2020 than 2019 given the lockdown order from March through May.

Given the projection of a positive increase in Air BnB revenue, we decided to explore the reason for this observation by looking into the revenue generated from different room types. This was conducted using the median rather than the mean due to a high discrepancy between a majority of the dataset and outliers.

![](images/Picture54.png)

![](images/Picture55.png)

From the monthly average price plots of both 2019 and 2020, we can see that average prices are relatively consistent for the entire home/ apt, shared room, and private room. However, prices for hotel rooms have dropped significantly from 2019 to 2020 relative to the same time period. In order to get a closer look at the data, a graph was then created showing the difference in pricing for each room type for 2020 relative to 2019.

![](images/Picture55.png)

The above graph indicates that we notice a sizable difference in pricing right after the start of Covid. While the median price of a shared room had already been decreasing prior to April 2020, we see a decrease in pricing for all room types from April 2020 onwards.
















