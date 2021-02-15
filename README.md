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

