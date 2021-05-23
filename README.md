# case study:divvy-chicago-bike-data-from-mai2020-to-april2021
in this case study i will use the Divvy trip data to examine biking trends 
in Chicago over the course of one year from mai2020 to april 2021
then conduct simple analysis to help answer the key question:
“In what ways do members and casual riders use Divvy bikes differently?”
##step 1 Load packages
install.packages("tidyverse")
library(tidyverse)
library(lubridate)
library(ggplot2)
## Step 2: Import data
m2020_05 <- read.csv("202005-divvy-tripdata.csv")
m2020_06 <- read.csv("202006-divvy-tripdata.csv")
m2020_07 <- read.csv("202007-divvy-tripdata.csv")
m2020_08 <- read.csv("202008-divvy-tripdata.csv")
m2020_09 <- read.csv("202009-divvy-tripdata.csv")
m2020_10 <- read.csv("202010-divvy-tripdata.csv")
m2020_12 <- read.csv("202012-divvy-tripdata.csv")
m2021_01 <- read.csv("202101-divvy-tripdata.csv")
m2021_02 <- read.csv("202102-divvy-tripdata.csv")
m2021_03 <- read.csv("202103-divvy-tripdata.csv")
m2021_04 <- read.csv("202104-divvy-tripdata.csv")
## STEP 3:wrangle data and combine into a single file  
year_data <-      bind_rows(m2020_06,m2020_06,m2020_07,m2020_08,m2020_09,m2020_10,m2020_11,m2020_12,m2021_01,m2021_02,m2021_03,m2021_04)<br>
## understanding data <br>
colnames(year_data) <br>
![alt text](https://github.com/hassansidani/my_first_profilio/blob/main/images/Colnames.PNG)<Br>
### head(year_data) <br>
![alt text](https://github.com/hassansidani/my_first_profilio/blob/main/images/head2.PNG)<br>
# step 4 cleaning data
### remove unnecessary column start_lat, start_lng, end_lat, end_lng<br>
year_data <- year_data %>%  <br>
  select(-c(start_lat, start_lng, end_lat, end_lng))<br>
### adding columns  "date","year","day_of_week" and "ride_length" for calculation <br>
year_data$date <- as.Date(year_data$started_at) #The default format is yyyy-mm-dd <br>
year_data$month <- format(as.Date(year_data$date), "%m")<br>
year_data$day_of_week <- format(as.Date(year_data$started_at), "%A")<br>
year_data$ride_length <- difftime(year_data$ended_at,year_data$started_at)<br>
### The dataframe includes a few hundred entries when bikes were taken out of docks <br>and checked for quality by Divvy or ride_length was negative<br>
all_trips <- year_data[!(year_data$start_station_name == "HQ QR" | year_data$ride_length<0),] <br> 
### Compare members and casual users<br>
aggregate(all_trips$ride_length ~ all_trips$member_casual, FUN = mean)<br>
aggregate(all_trips$ride_length ~ all_trips$member_casual, FUN = median)<br>
aggregate(all_trips$ride_length ~ all_trips$member_casual, FUN = max)<br>
aggregate(all_trips$ride_length ~ all_trips$member_casual, FUN = min)<br>
colnames(all_trips)<br>
## Let's visualize the number of rides by rider type  
  all_trips %>% <br>
  &nbsp; &nbsp; mutate(weekday = wday(started_at, label = TRUE)) %>% <br>
  &nbsp; &nbsp;  group_by(member_casual, weekday) %>%<br> 
  &nbsp; &nbsp; summarise(number_of_rides = n()<br>
  &nbsp; &nbsp; ,average_duration = mean(ride_length)) %>%<br> 
  &nbsp; a&nbsp; arrange(member_casual, weekday)  %>% <br>
  &nbsp; &nbsp; ggplot(aes(x = weekday, y = number_of_rides, fill = member_casual)) +<br>
  &nbsp; &nbsp; geom_col(position = "dodge")<br>
  ![alt text](https://github.com/hassansidani/my_first_profilio/blob/main/images/number%20of%20ride.png)<Br>
## Let's create a visualization for average duration<br>
all_trips %>% <br>
  mutate(weekday = wday(started_at, label = TRUE)) %>% <br>
  group_by(member_casual, weekday) %>% <br>
  summarise(number_of_rides = n()<br>
            ,average_duration = mean(ride_length)) %>% <br>
  arrange(member_casual, weekday)  %>% <br>
  ggplot(aes(x = weekday, y = average_duration, fill = member_casual)) +<br>
  geom_col(position = "dodge")<br>
  ![alt text](https://github.com/hassansidani/my_first_profilio/blob/main/images/average_duration.png)<Br>
## Let's create a visualization for montly numbre of ride duration<br>
all_trips %>% <br>
  mutate(weekday = wday(started_at, label = TRUE)) %>% <br>
  group_by(member_casual, month) %>% <br>
  summarise(number_of_rides = n()<br>
            ,average_duration = mean(ride_length)) %>% <br>
  arrange(member_casual, month)  %>% <br>
  ggplot(aes(x =month, y = number_of_rides, fill = member_casual)) +<br>
  geom_col(position = "dodge")<br>  
  ![alt text](https://github.com/hassansidani/my_first_profilio/blob/main/images/Colnames.PNG)<Br>
