# **Google Data Analytics : Capstone Case Study**
## Cyclistic Bike-Share Analysis
This analysis is an optional capstone project for the Google Data Analytics Professional Certificate offered via Coursera. Below is my attempt to put together everything I've learned throughout my journey on this course.

****************************************************************************************************************************

# *ASK*

## Introduction :
Cyclistic Bike-Share is a one of the leading bike sharing operators or company based in Chicago. Their bike share program is perfect for commuting, running errands, visiting friends, or exploring the city. Moreover, inspiring an eco-friendly way and culture to get around (coupled with exercise benefits) in an effort to contribute to the preservation of beautiful our environment.

The director of marketing (Moreno) believes the company’s future success depends on maximizing the number of annual memberships, and is confident that an analysis of the casual riders would reveal insight on the potential growth opportunities for the company.

## Business Task :
Analyze Cyclistic historical trips data to gain insights into how do annual members and casual riders use Cyclistic bikes differently, user's behavior patterns, and discover trends and insights for the Cyclistic marketing team.

## Business Objectives :
* What are the trends and insights identified?
* How could these trends apply to all the customers of Cyclistic?
* How could these trends influence change in casual and new customers?
* How could these trends and insights help influence the marketing team of Cyclistic strategies?


## Deliverables :
* A clear statement of the business task.
* A description of all data sources used.
* Documentation of any cleaning, transformation or manipulation of data.
* A clear summary of analysis.
* Supporting visualizations and key findings.
* Threefold recommendations based on analysis.

## Stakeholders :
* Moreno - Head of Cyclistic Marketing Team
* Cyclistic Financial Analysts Team - A team of financial analysts responsible for identifying the financial profitability of annual membership.
* Cyclistic Marketing Team - A team of analysts informing and aiding Cyclistic marketing strategy 

*************************************************************************************************************************

# *PREPARE*

## Dataset :
* The data set is publicly available on [divvy-tripdata](https://divvy-tripdata.s3.amazonaws.com/index.html) , index of bucket.
* The data has been made available by
Motivate International Inc, a third party company.
* Data Collected include :
   1. ride_id,
   2. rideable_type
   3. started_at
   4. ended_at
   5. start_station_name
   6. start_station_id
   7. end_startion_name
   8. end_station_id
   9. member_causal (membership type)
* Data was downloaded according to the [data licence agreement](https://www.divvybikes.com/data-license-agreement)

## Dataset Limitations :
1. Data used was collected from Q1 and Q2 of 2021. User's bike share and usage activity may have changed since then, perhaps influenced by external factors.
2. As per the data licence agreement, Divvy system data agreed with the Cuty of Chicago to only make the certain data owned by the City available to the public, hence some parameters may be missed which could affect the accuracy of our analysis.

## Is Data ROCCC? :
A reliable dataset or data-source should ROCCC which is an abbriviation for **R**eliable, **O**riginal, **C**omprehensive, **C**urrent, and **C**ited.
 1. Reliable - HIGH - Reliable as the data comes from the secondary data source, which would be DivvyBikes (which happens to be a reliable data source)
 2. Original - MEDIUM - Second party data source (DivvyBikes Data)
 3. Comprehensive - HIGH - Parameters match Cyclistic Bike-Share inventory parameters
 4. Current - HIGH - Data is from 2021's first and second quarter
 5. Cited - MEDIUM - Data collected from second party, hence a fair level of uncertainty is observed and expected.

The dataset is good quality data, and is recommended to produce business recommendations based on the insights drawn from it.

## Data Selection :
The following files are selected and copied for analysis.
 * 202101-divvy-tripdata
 * 202102-divvy-tripdata
 * 202103-divvy-tripdata
 * 202104-divvy-tripdata
 * 202105-divvy-tripdata
 * 202106-divvy-tripdata

*******************************************************************************************************************************
# *PROCESS*
Using R to process, transform and process the data.

## Environment Setup :
Import packages
```{r}
library(tidyverse)
library(lubridate)
library(skimr)
library(dplyr)
```


## Load or Import Datasets :
Reading required CSV files
```{r}
jan_2021 <- read.csv("202101-divvy-tripdata.csv")
feb_2021 <- read.csv("202102-divvy-tripdata.csv")
mar_2021 <- read.csv("202103-divvy-tripdata.csv")
apr_2021 <- read.csv("202104-divvy-tripdata.csv")
may_2021 <- read.csv("202105-divvy-tripdata.csv")
june_2021 <- read.csv("202106-divvy-tripdata.csv")
```
Merge CSV files into a single data-frame
```{r}
trips_frame <- rbind(jan_2021, feb_2021, mar_2021, apr_2021, may_2021, june_2021)
```

## Data Cleaning and Manipulation:

### Data Cleaning Steps :
1. Define and count null or missing values and, then remove them
2. Check for duplicated entries, and amend
3. Define incorrect values and clean up incorrect values (ex. data with started_at > ended_at)

```{r}
null_values <-sapply(trips_frame, function(x) sum(is.na(x))) %>%
  as.data.frame()
null_values %>%
  ggplot(aes(x = rownames(missing_values), y = .)) + geom_bar(stat = "identity")
 colSums(is.na(trips_frame))
 
 trips_frameClean <- trips_frame[complete.cases(trips_frame), ]
 
 trips_frameClean <- trips_frameClean %>% 
  filter(trips_frameClean$started_at < trips_frameClean$ended_at)
```
  



### Data Transformation/Manipulation Steps :
1. Create new coulmn (ride_length)
2. Create new column (day_of_week)


```{r}
trips_frameClean$ride_length <- trips_frameClean$ended_at - trips_frameClean$started_at
trips_frameClean$ride_length <- hms::hms(seconds_to_period(trips_frameClean$ride_length))


 trips_frameClean$day_of_week <- wday(trips_frameClean$started_at, label = FALSE)
```



*********************************************************************************************************************************************************

 # *ANALYSIS*
 
 ## Perform Calculations :
  * count number of rides
  * averages (ride_length, ride_length for members & casual riders)
  * min and max ride lengths
  * number of rides for member_casual number
  * average ride length by each day for member_casual
  * observe data by membership type and day_of_week
 
 
  
```{r}
total_rides <- trips_frameClean %>%
   group_by(member_casual) %>%
      summarize(count = n(), .groups="drop")
  
trips_frameClean %>%
   group_by(ride_id, day_of_week) %>%
      summarize(num_of_rides = n())

trips_frameClean %>% 
  summarize(mean(ride_length))
  
trips_frameClean %>%
   group_by(member_casual) %>%
      summarize(mean(ride_length))
      
 trips_frameClean %>%
   group_by(day_of_week) %>%
      summarize(mean(ride_length))
  
trips_frameClean %>% 
  summarize(max(ride_length))
  
 trips_frameClean %>% 
  summarize(min(ride_length))
  
 aggregate(trips_frameClean$ride_length ~ trips_frameClean$member_casual + trips_frameClean$day_of_week, FUN = mean)
 
 Mode(trips_frameClean$day_of_week)
 
 trips_frameClean %>% 
  mutate(weekday = wday(started_at, label = TRUE)) %>% 
   group_by(member_casual, weekday) %>% 
     summarize(numb_of_rides = n(), average_length = mean(ride_length)) %>% 
      arrange(member_casual, weekday)
 ````

*****************************************************************************************************************************************************
# *SHARE*










