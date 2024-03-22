# Cyclistic Bike Usage Patterns 

### Project overview
Cyclistic's annual members and casual cyclists have different habits and preferences, which this investigation will examine. I use previous bike trip data to understand how these two customer categories use Cyclistic bikes. We want to uncover usage trends and trip durations distinguish annual members from casual riders through thorough data analysis. These information can help Cyclistic create focused marketing tactics to turn casual cyclists into devoted annual members, boosting growth. Finally, recommendations will be added based on this analysis on what marketing approach can turn casual customers to annual members.

### About Cyclistic
My fictional company, Cyclistic, successfully launched a bike-sharing service in 2016. Since then, the program has expanded to include 5,824 geotracked bicycles connected to 692 stations throughout Chicago. Bikes can be unlocked and returned to any station within the system at any time. 

### Data Source
Lyft Bikes and Scooters, LLC owns the datasets utilized in this case study. The datasets are acceptable and will help me answer business questions. Motivate International Inc. has made this data available under this [license](https://divvybikes.com/data-license-agreement).

### Tools
- BigQuery
- Excel
- RStudio

### Data Cleaning & Preparation
- Loading the most recent 12 month data
- Cleaning and Formatting the merged annual data
- Handling missing values
### Exploratory Data Analysis
Exploring different aspects including the durations of trips, the number of rides, and the average distance between beginning and ending stations on a monthly and weekly basis, categorizing riders as membership or casual.
### Data Analysis
Loading, Cleaning and Preparing:
```r 
setwd("G:/Downloads/Case Study Data/CSV")
FEB_2023 <- read.csv("202302-divvy-tripdata.csv")
MAR_2023 <- read.csv("202303-divvy-tripdata.csv")
APR_2023 <- read.csv("202304-divvy-tripdata.csv")
MAY_2023 <- read.csv("202305-divvy-tripdata.csv")
JUN_2023 <- read.csv("202306-divvy-tripdata.csv")
JUL_2023 <- read.csv("202307-divvy-tripdata.csv")
AUG_2023 <- read.csv("202308-divvy-tripdata.csv")
SEP_2023 <- read.csv("202309-divvy-tripdata.csv")
OCT_2023 <- read.csv("202310-divvy-tripdata.csv")
NOV_2023 <- read.csv("202311-divvy-tripdata.csv")
DEC_2023 <- read.csv("202312-divvy-tripdata.csv")
JAN_2024 <- read.csv("202401-divvy-tripdata.csv")
FEB_2024 <- read.csv("202402-divvy-tripdata.csv")
install.packages("tidyverse")
install.packages("here")
install.packages("janitor")
install.packages("skimr")
install.packages("plotly")
library(plotly)
library(tidyverse)
library(skimr)
library(here)
library(janitor)
annual_data <- bind_rows(FEB_2023,MAR_2023,APR_2023,MAY_2023,JUN_2023,JUL_2023,AUG_2023,
                         SEP_2023,OCT_2023,NOV_2023,DEC_2023,JAN_2024,FEB_2024)
# filtering null values
annual_data_filtered <- filter(annual_data, end_lat != "NA")
annual_data_filtered2 <- filter(annual_data_filtered, end_lng != "NA")
write.csv(annual_data_filtered2, "annual_data_cleaned.csv", row.names = FALSE)
```
Data Manipulation:
```sql
WITH ride_data AS
  (
    SELECT
    ride_id,
    rideable_type,
    member_casual,
    EXTRACT(TIME FROM started_at) AS start_time,
    EXTRACT(TIME FROM ended_at) AS end_time,
    TIME_DIFF(
    EXTRACT(TIME FROM ended_at), EXTRACT(TIME FROM started_at), MINUTE
    ) AS ride_duration,
    ST_DISTANCE(ST_GEOGPOINT(start_lng,start_lat), ST_GEOGPOINT(end_lng,    
     end_lat)) AS distance_travelled,
    EXTRACT(DAYOFWEEK FROM started_at) AS week_day,
    EXTRACT(MONTH FROM started_at) AS month,
    FROM `windy-bearing-415614.cyclist_bike_share.annual_data_cleaned`
    WHERE
    TIME_DIFF(
        EXTRACT(TIME FROM ended_at), EXTRACT(TIME FROM started_at), MINUTE
      ) >= 0
  )
SELECT
  member_casual,
  week_day,
  COUNT(ride_id) AS no_of_ride,
  MAX(ride_duration) AS max_ride_duration,
  AVG(ride_data.ride_duration) AS average_duration,
  AVG(distance_travelled) AS avg_distances_between_stations
FROM `ride_data` 
GROUP BY member_casual, week_day
ORDER BY week_day ASC
```
### Results
The results of the anaysis can be summarized as follows:
- The monthly count of rides taken by members are noticably higher than casual customers aggregating the all weekdays. But the monthly data of only Sundays and Mondays shows that the casual customers tend catch up with the members in terms of ride count and even exceeds them in between June and August.

![Monthly Ride Count-Members VS Casual](https://github.com/iftekhar-kabir/iftekhar-kabir-Documenting-Cyclistic-Bike-Usage-Patterns/assets/163831745/f8e07fcc-fe15-46a0-b726-72769d270130)  ![Monthly Ride Count(Sun-Mon)-Members VS Casual](https://github.com/iftekhar-kabir/iftekhar-kabir-Documenting-Cyclistic-Bike-Usage-Patterns/assets/163831745/8811795b-b0c9-4003-9294-ee1113b5daeb)


- The average trip duration of casual customers is much higher than members. The difference is maximized from March until November. The average trip duration of members tend to be more consistent.
  
![Monthly Trip Duration- Members VS Casuals](https://github.com/iftekhar-kabir/iftekhar-kabir-Documenting-Cyclistic-Bike-Usage-Patterns/assets/163831745/5bb8da51-d110-4a94-9f89-c133a3c82ce0)


- Weekly average distance between start and end station categorized by members and casual customers isn't significantly different enough to explain the discrepancy in trip durations.

![avg distance](https://github.com/iftekhar-kabir/iftekhar-kabir-Documenting-Cyclistic-Bike-Usage-Patterns/assets/163831745/2f768d8b-4c93-45b4-a02a-36d146f4d237)

### Recommendations
1. Due to the fact that casual riders utilize the service more frequently on weekends and holidays, marketing campaigns that target these users specifically would be effective. An example can be to provide weekend or holiday package discounts upon buying memberships to casual riderss
2. Since casual riders do longer trips, targeted recommendations can be offered to convert them to annual members. For instance, marketing communications should promote a yearly membership for frequent riders as a cheaper and easier choice for longer rides.


### Limitations
- The analysis may overlooked temporal differences in customer behavior due to time period. Multiple-year longitudinal studies or analysis may reveal usage patterns over time.
- The analysis focuses on trip duration, ride count, and distance. It may neglect weather, promotions, events, and competitor actions that affect customer behavior.
- The investigation finds connections between membership status and trip time but not causation. Other unobserved variables or confounding variables may affect this relationship.

### References
1. [stackoverflow](https://stackoverflow.com/)
2. [W3Schools](https://www.w3schools.com/)






