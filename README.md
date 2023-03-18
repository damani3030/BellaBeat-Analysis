---
title: "Google Data Analytics Capstone Project"
author: "Damani Hylton"
date: "2023-01-20"
output: html_document
---

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
```

# [Bellabeat Analysis]{.underline}

### Business Task

Identify opportunities for growth by analyzing smart device usage in order to gain insight into how consumers use non-Bellabeat smart devices.

### About the Company

Bellabeat is a high-tech company that manufactures health-focused smart products. Since it was founded in 2013, Bellabeat has grown rapidly and quickly positioned itself as a tech-driven wellness company for women. Collecting data on activity, sleep, stress, and reproductive health has allowed Bellabeat to empower women with knowledge about their own health and habits.

### Data Sources

-   Fitbit Fitness Tracker Data from Kaggle. <https://www.kaggle.com/datasets/arashnic/fitbit>
-   Thirty Fitbit users consented to the submission of their personal tracker data.
-   Organized in long format and I grouped the daily, hourly, and minutes files in a folder.
-   Reliable? Yes
-   Orginial? Yes
-   Comprehensive? No (Only 30 participants)
-   Current? Yes
-   Cited? Yes

### Setting up environment

Notes: setting up my R environment by loading the 'tidyverse', 'ggplot2' , 'lubridate' , 'dplyr', 'janitor' and 'readr' packages:

```{r}
library(tidyverse)
library(ggplot2)
library(lubridate)
library(dplyr)
library(janitor)
library(readr)

```

### Importing CSV files:

Importing dailyActivity_merged csv

```{r}
daily_fitness_data <- read_csv("~/Data_Analyst_Portfolio/BellaBeat_Project/20160412-20160512_Fitbit_Data/daily_metrics_merged/dailyActivity_merged.csv")
```

Importing sleepDay_merged csv

```{r}
sleep_Total <- read_csv("~/Data_Analyst_Portfolio/BellaBeat_Project/20160412-20160512_Fitbit_Data/sleepDay_merged.csv")
```

Importing hourlySteps_merged csv

```{r}
hourSteps <- read_csv("~/Data_Analyst_Portfolio/BellaBeat_Project/20160412-20160512_Fitbit_Data/hourlySteps_merged.csv")
```

Importing hourlyCalories_merged csv

```{r}
hourlyCalories <- read_csv("~/Data_Analyst_Portfolio/BellaBeat_Project/20160412-20160512_Fitbit_Data/hourlyCalories_merged.csv")
```

## Previewing loaded data

1- Looking at daily_fitness_data.

```{r}
head(daily_fitness_data)
```

2- Identifying column names for daily_fitness_data

```{r}
colnames(daily_fitness_data)
```

3- Looking at sleep_Total data

```{r}
head(sleep_Total)
```

4- Looking at hourlySteps_merged data

```{r}
head(hourSteps)
```

5- Looking at hourlyCalories_merged data

```{r}
head(hourlyCalories)
```

## Cleaning the imported data

1- Notes: Cleaned the column names for better readability, renamed the 'Activity Date' column to 'date', assigned YYYY-MM-DD format and changed column datatype from character to 'Date'

```{r}
cleaned_daily_fitness <- clean_names(daily_fitness_data)

```

2- Renamed the 'activity_date' column to 'date'.

```{r}
cleaned_daily_fitness <- cleaned_daily_fitness %>% 
  rename(date = activity_date)
```

3- Changed date format to YYYY-MM-DD

```{r}
cleaned_daily_fitness$date <- mdy(cleaned_daily_fitness$date)
```

4- Added Weekday column to specify Days of the week

```{r}
cleaned_daily_fitness <- cleaned_daily_fitness %>% 
  mutate(week_day = weekdays(as.Date.factor(date)))
```

See the changes made to cleaned_daily_fitness

```{r}
str(cleaned_daily_fitness)
```

5- Cleaned hourlySteps_merged data Notes: Cleaning up the name format of the columns and changing the date format to YYYY-MM-DD-HH-MM

```{r}
cleaned_hourlySteps <- clean_names(hourSteps)

cleaned_hourlySteps$activity_hour <- mdy_hms(cleaned_hourlySteps$activity_hour)

```

6- Cleaned sleep_Total data and renamed 'sleep_day' to 'date'

```{r}

cleaned_sleep_Total <- clean_names(sleep_Total) %>% 
  rename(date = sleep_day)

cleaned_sleep_Total$date <- mdy_hms(cleaned_sleep_Total$date)

```

7- Cleaned hourlyCalories data and changed the date format to YYYY-MM-DD-HH-MM

```{r}
cleaned_hourly_Calories <- clean_names(hourlyCalories)

cleaned_hourly_Calories$activity_hour <- mdy_hms(cleaned_hourly_Calories$activity_hour)
```

Since all the data frames have 'id' in common, let's check how many unique participants and rows are in each dataframe:

```{r unique ids and rows}
n_distinct(cleaned_daily_fitness$id)
n_distinct(cleaned_sleep_Total$id)


nrow(cleaned_sleep_Total)
nrow(unique(cleaned_sleep_Total))
```

8- Removed duplicate rows found in 'cleaned_sleep_total'

```{r}
cleaned_sleep_Total <- unique(cleaned_sleep_Total)

nrow(cleaned_sleep_Total)
```

9- Note: Not all participants logged sleep data, as such, merge data frames by 'id' and leave out the participants that did not record sleep data for this merge.

```{r merging data frames}
combined_merge <- merge(cleaned_daily_fitness, cleaned_sleep_Total)

n_distinct(combined_merge$id)
```

10- Merging the hourly steps and calories data frames by 'id' and 'activity hour'.

```{r}
combined_hourly <- cleaned_hourlySteps %>% 
  inner_join(cleaned_hourly_Calories, by = c("id", "activity_hour"))

```

11- Merging average sleep and average steps taken by participants and creating a new data frame 'sleep_vs_steps'.

```{r}
avg_sleeping_mins <- combined_merge %>% 
  group_by(id) %>% 
  summarize(avg_sleeping_mins = mean(total_minutes_asleep))

avg_total_steps <- combined_merge %>% 
  group_by(id) %>% 
  summarize(avg_total_steps = mean(total_steps))

sleep_vs_steps <- merge(avg_sleeping_mins, avg_total_steps)
```

Looking at a summary of the combined_merge data

```{r}
combined_merge %>% 
  select(total_steps,
         calories,
         total_minutes_asleep,
         total_time_in_bed) %>% 
  summary()
```

Summary of combined_hourly data

```{r}
combined_hourly %>% 
  select(activity_hour,
         step_total,
         calories) %>% 
  summary()
```

Summary of sleep_vs_steps data

```{r}
sleep_vs_steps %>% 
  select(avg_sleeping_mins, 
         avg_total_steps) %>% 
  summary()
```

### According to the U.S. Department of Health and Human Services, most adults need 7 or more hours of good-quality sleep each night. As such, I am filtering participants that get less than 7 hours sleeping and less than the group average in steps taken daily.

```{r filter sleep vs steps}
sleep_vs_steps %>%
  filter(avg_sleeping_mins <= 420,
         avg_total_steps <= 7890)
  
```

## Analysis

... what hours they can use a bump in motivation from a Bellabeat membership and perhaps a reminder on their smart device- if nothing wrong, give some mindfulness, stress relief from app) also which day of the week is more active perhaps?? OR who slept more burnt more calories in the day for plot??? FILTER OUT PARTICIPANTS THAT HAVE LESS THAN 8HRS OF SLEEP AND RECOMMEND IN MY ANALYSIS TO USE APP TO HELP THEM SLEEP, WELLNESS SIDE OF APP

### Now that we have some data cleaned and merged, we can begin some exploratory analysis

#### Figure 1
Checking out if more sleep on average, leads to more average total steps per day

```{r}
ggplot(data = sleep_vs_steps, mapping = aes(x = avg_sleeping_mins, y = avg_total_steps)) +
  geom_jitter(color = "brown") + 
  geom_smooth() +
  labs(title = "Overall Average Mins Asleep vs Average Total Steps", subtitle = "Per participant", x = "Average Mins Asleep", y = "Average Total Steps")
```

As shown, it does NOT seem, at least for these 30 women, that more sleep on average, leads to more average total steps. Running a correlation test seems to confirm this.
```{r}
cor(sleep_vs_steps$avg_sleeping_mins, sleep_vs_steps$avg_total_steps)
```
#### Figure 2
What days of the week are participants most likely to burn the most calories?
```{r}
week_dayz <- factor(combined_merge$week_day, levels = c("Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday", "Sunday"))

ggplot(data = combined_merge, mapping = aes(x = week_dayz, y = calories, color = week_dayz)) +
  geom_point(position = "jitter", stat = "identity") +
  labs(title = "Calories burnt throughout the week") +
  ylab("Calories") +
  scale_x_discrete("Week Day", guide = guide_axis(angle = 90)) +
  scale_color_discrete("Legend")

```
Thursdays and Saturdays seem like the days participants are burning the most calories, with Fridays and Sundays being the least.

#### Figure 3
Does more time asleep lead to a more active day? Comparing the total minutes asleep and total steps taken to see how they vary during the week.
```{r}

ggplot(data = combined_merge, mapping = aes(x = total_minutes_asleep, y = total_steps, color = week_dayz, size = total_time_in_bed)) +
  geom_point(alpha = 0.5) +
  labs(title = "Total Minutes Asleep vs Total Steps", subtitle = "Per Weekday", x = "Total Minutes Asleep", y = "Total Steps") +
  theme(legend.position = "right") +
  scale_color_discrete("Week Day")
```
#### Figure 4
Hourly Activity: what hours are the participants most active?

```{r}

twenty_four_hrs <- format(as.POSIXct(combined_hourly$activity_hour), format = "%H")

ggplot(data = combined_hourly, aes(x = twenty_four_hrs, y = step_total, fill = twenty_four_hrs)) +
  geom_bar(stat = "identity") +
  labs(title = "Hourly Steps", x = "Activity Hour", y = "Step Total")


combined_merge %>% 
  group_by(id) %>% 
  summarize(sumz = sum(total_steps))
```
This graph shows that the participants are most active at 6-7pm in the weekdays. Graph also shows there's room for improvement at the 3pm time slot as there is considerable decline at that time.

### Recommendations
1. The Bellabeat Membership (app) should notify it's users when they 'fall' out of the average inactivity period during certain times of the day. One way to monitor activity is by steps taken per hour and if the app calculates that the user would not make their recommended steps per day, give them notifications and reminders.

2. Bellabeat should incorporate the Bellabeat membership into everyday living and activity monitoring. If for some reason a user has been in bed but spending less time sleeping for a couple days, someone from Bellabeat could do a quick check in on the user, either via text or video.

3. Wellness sessions can also take place right from the Bellabeat membership app* that allows users to unwind on a stressful day, while also incorporating light exercises, for example yoga. To capitalize on this, looking into exercise equipment, like an stationary bike, could be a new revenue stream.

4. Users of the Bellabeat membership app* should have access to chefs' personalized menu, that can cater meals that heal, nurture and rejuvenate users for another day.

#### Note: Data collection was not done in an unbiased why and there needs to be a larger sample size than the 30 users that participated in this study. Furthermore, a longer data collection time period needs to be adheard to, than the 2 months given with this data-set.
