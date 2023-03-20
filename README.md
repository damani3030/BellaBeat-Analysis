Google Data Analytics Capstone Project
================
Damani Hylton
2023-01-20

# <u>Bellabeat Analysis</u>

### Business Task

Identify opportunities for growth by analyzing smart device usage in
order to gain insight into how consumers use non-Bellabeat smart
devices.

### About the Company

Bellabeat is a high-tech company that manufactures health-focused smart
products. Since it was founded in 2013, Bellabeat has grown rapidly and
quickly positioned itself as a tech-driven wellness company for women.
Collecting data on activity, sleep, stress, and reproductive health has
allowed Bellabeat to empower women with knowledge about their own health
and habits.

### Data Sources

- Fitbit Fitness Tracker Data from Kaggle.
  <https://www.kaggle.com/datasets/arashnic/fitbit>
- Thirty Fitbit users consented to the submission of their personal
  tracker data.
- Organized in long format and I grouped the daily, hourly, and minutes
  files in a folder.
- Reliable? Yes
- Orginial? Yes
- Comprehensive? No (Only 30 participants)
- Current? Yes
- Cited? Yes

### Setting up environment

Notes: setting up my R environment by loading the ‘tidyverse’, ‘ggplot2’
, ‘lubridate’ , ‘dplyr’, ‘janitor’ and ‘readr’ packages:

``` r
library(tidyverse)
```

    ## ── Attaching packages ─────────────────────────────────────── tidyverse 1.3.2 ──
    ## ✔ ggplot2 3.4.0      ✔ purrr   1.0.0 
    ## ✔ tibble  3.1.8      ✔ dplyr   1.0.10
    ## ✔ tidyr   1.2.1      ✔ stringr 1.5.0 
    ## ✔ readr   2.1.3      ✔ forcats 0.5.2 
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()

``` r
library(ggplot2)
library(lubridate)
```

    ## Loading required package: timechange
    ## 
    ## Attaching package: 'lubridate'
    ## 
    ## The following objects are masked from 'package:base':
    ## 
    ##     date, intersect, setdiff, union

``` r
library(dplyr)
library(janitor)
```

    ## 
    ## Attaching package: 'janitor'
    ## 
    ## The following objects are masked from 'package:stats':
    ## 
    ##     chisq.test, fisher.test

``` r
library(readr)
```

### Importing CSV files:

Importing dailyActivity_merged csv

``` r
daily_fitness_data <- read_csv("~/Data_Analyst_Portfolio/BellaBeat_Project/20160412-20160512_Fitbit_Data/daily_metrics_merged/dailyActivity_merged.csv")
```

    ## Rows: 940 Columns: 15
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr  (1): ActivityDate
    ## dbl (14): Id, TotalSteps, TotalDistance, TrackerDistance, LoggedActivitiesDi...
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

Importing sleepDay_merged csv

``` r
sleep_Total <- read_csv("~/Data_Analyst_Portfolio/BellaBeat_Project/20160412-20160512_Fitbit_Data/sleepDay_merged.csv")
```

    ## Rows: 413 Columns: 5
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (1): SleepDay
    ## dbl (4): Id, TotalSleepRecords, TotalMinutesAsleep, TotalTimeInBed
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

Importing hourlySteps_merged csv

``` r
hourSteps <- read_csv("~/Data_Analyst_Portfolio/BellaBeat_Project/20160412-20160512_Fitbit_Data/hourlySteps_merged.csv")
```

    ## Rows: 22099 Columns: 3
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (1): ActivityHour
    ## dbl (2): Id, StepTotal
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

Importing hourlyCalories_merged csv

``` r
hourlyCalories <- read_csv("~/Data_Analyst_Portfolio/BellaBeat_Project/20160412-20160512_Fitbit_Data/hourlyCalories_merged.csv")
```

    ## Rows: 22099 Columns: 3
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (1): ActivityHour
    ## dbl (2): Id, Calories
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

## Previewing loaded data

1- Looking at daily_fitness_data.

``` r
head(daily_fitness_data)
```

    ## # A tibble: 6 × 15
    ##       Id Activ…¹ Total…² Total…³ Track…⁴ Logge…⁵ VeryA…⁶ Moder…⁷ Light…⁸ Seden…⁹
    ##    <dbl> <chr>     <dbl>   <dbl>   <dbl>   <dbl>   <dbl>   <dbl>   <dbl>   <dbl>
    ## 1 1.50e9 4/12/2…   13162    8.5     8.5        0    1.88   0.550    6.06       0
    ## 2 1.50e9 4/13/2…   10735    6.97    6.97       0    1.57   0.690    4.71       0
    ## 3 1.50e9 4/14/2…   10460    6.74    6.74       0    2.44   0.400    3.91       0
    ## 4 1.50e9 4/15/2…    9762    6.28    6.28       0    2.14   1.26     2.83       0
    ## 5 1.50e9 4/16/2…   12669    8.16    8.16       0    2.71   0.410    5.04       0
    ## 6 1.50e9 4/17/2…    9705    6.48    6.48       0    3.19   0.780    2.51       0
    ## # … with 5 more variables: VeryActiveMinutes <dbl>, FairlyActiveMinutes <dbl>,
    ## #   LightlyActiveMinutes <dbl>, SedentaryMinutes <dbl>, Calories <dbl>, and
    ## #   abbreviated variable names ¹​ActivityDate, ²​TotalSteps, ³​TotalDistance,
    ## #   ⁴​TrackerDistance, ⁵​LoggedActivitiesDistance, ⁶​VeryActiveDistance,
    ## #   ⁷​ModeratelyActiveDistance, ⁸​LightActiveDistance, ⁹​SedentaryActiveDistance

2- Identifying column names for daily_fitness_data

``` r
colnames(daily_fitness_data)
```

    ##  [1] "Id"                       "ActivityDate"            
    ##  [3] "TotalSteps"               "TotalDistance"           
    ##  [5] "TrackerDistance"          "LoggedActivitiesDistance"
    ##  [7] "VeryActiveDistance"       "ModeratelyActiveDistance"
    ##  [9] "LightActiveDistance"      "SedentaryActiveDistance" 
    ## [11] "VeryActiveMinutes"        "FairlyActiveMinutes"     
    ## [13] "LightlyActiveMinutes"     "SedentaryMinutes"        
    ## [15] "Calories"

3- Looking at sleep_Total data

``` r
head(sleep_Total)
```

    ## # A tibble: 6 × 5
    ##           Id SleepDay              TotalSleepRecords TotalMinutesAsleep TotalT…¹
    ##        <dbl> <chr>                             <dbl>              <dbl>    <dbl>
    ## 1 1503960366 4/12/2016 12:00:00 AM                 1                327      346
    ## 2 1503960366 4/13/2016 12:00:00 AM                 2                384      407
    ## 3 1503960366 4/15/2016 12:00:00 AM                 1                412      442
    ## 4 1503960366 4/16/2016 12:00:00 AM                 2                340      367
    ## 5 1503960366 4/17/2016 12:00:00 AM                 1                700      712
    ## 6 1503960366 4/19/2016 12:00:00 AM                 1                304      320
    ## # … with abbreviated variable name ¹​TotalTimeInBed

4- Looking at hourlySteps_merged data

``` r
head(hourSteps)
```

    ## # A tibble: 6 × 3
    ##           Id ActivityHour          StepTotal
    ##        <dbl> <chr>                     <dbl>
    ## 1 1503960366 4/12/2016 12:00:00 AM       373
    ## 2 1503960366 4/12/2016 1:00:00 AM        160
    ## 3 1503960366 4/12/2016 2:00:00 AM        151
    ## 4 1503960366 4/12/2016 3:00:00 AM          0
    ## 5 1503960366 4/12/2016 4:00:00 AM          0
    ## 6 1503960366 4/12/2016 5:00:00 AM          0

5- Looking at hourlyCalories_merged data

``` r
head(hourlyCalories)
```

    ## # A tibble: 6 × 3
    ##           Id ActivityHour          Calories
    ##        <dbl> <chr>                    <dbl>
    ## 1 1503960366 4/12/2016 12:00:00 AM       81
    ## 2 1503960366 4/12/2016 1:00:00 AM        61
    ## 3 1503960366 4/12/2016 2:00:00 AM        59
    ## 4 1503960366 4/12/2016 3:00:00 AM        47
    ## 5 1503960366 4/12/2016 4:00:00 AM        48
    ## 6 1503960366 4/12/2016 5:00:00 AM        48

## Cleaning the imported data

1- Notes: Cleaned the column names for better readability, renamed the
‘Activity Date’ column to ‘date’, assigned YYYY-MM-DD format and changed
column datatype from character to ‘Date’

``` r
cleaned_daily_fitness <- clean_names(daily_fitness_data)
```

2- Renamed the ‘activity_date’ column to ‘date’.

``` r
cleaned_daily_fitness <- cleaned_daily_fitness %>% 
  rename(date = activity_date)
```

3- Changed date format to YYYY-MM-DD

``` r
cleaned_daily_fitness$date <- mdy(cleaned_daily_fitness$date)
```

4- Added Weekday column to specify Days of the week

``` r
cleaned_daily_fitness <- cleaned_daily_fitness %>% 
  mutate(week_day = weekdays(as.Date.factor(date)))
```

See the changes made to cleaned_daily_fitness

``` r
str(cleaned_daily_fitness)
```

    ## tibble [940 × 16] (S3: tbl_df/tbl/data.frame)
    ##  $ id                        : num [1:940] 1.5e+09 1.5e+09 1.5e+09 1.5e+09 1.5e+09 ...
    ##  $ date                      : Date[1:940], format: "2016-04-12" "2016-04-13" ...
    ##  $ total_steps               : num [1:940] 13162 10735 10460 9762 12669 ...
    ##  $ total_distance            : num [1:940] 8.5 6.97 6.74 6.28 8.16 ...
    ##  $ tracker_distance          : num [1:940] 8.5 6.97 6.74 6.28 8.16 ...
    ##  $ logged_activities_distance: num [1:940] 0 0 0 0 0 0 0 0 0 0 ...
    ##  $ very_active_distance      : num [1:940] 1.88 1.57 2.44 2.14 2.71 ...
    ##  $ moderately_active_distance: num [1:940] 0.55 0.69 0.4 1.26 0.41 ...
    ##  $ light_active_distance     : num [1:940] 6.06 4.71 3.91 2.83 5.04 ...
    ##  $ sedentary_active_distance : num [1:940] 0 0 0 0 0 0 0 0 0 0 ...
    ##  $ very_active_minutes       : num [1:940] 25 21 30 29 36 38 42 50 28 19 ...
    ##  $ fairly_active_minutes     : num [1:940] 13 19 11 34 10 20 16 31 12 8 ...
    ##  $ lightly_active_minutes    : num [1:940] 328 217 181 209 221 164 233 264 205 211 ...
    ##  $ sedentary_minutes         : num [1:940] 728 776 1218 726 773 ...
    ##  $ calories                  : num [1:940] 1985 1797 1776 1745 1863 ...
    ##  $ week_day                  : chr [1:940] "Tuesday" "Wednesday" "Thursday" "Friday" ...

5- Cleaned hourlySteps_merged data Notes: Cleaning up the name format of
the columns and changing the date format to YYYY-MM-DD-HH-MM

``` r
cleaned_hourlySteps <- clean_names(hourSteps)

cleaned_hourlySteps$activity_hour <- mdy_hms(cleaned_hourlySteps$activity_hour)
```

6- Cleaned sleep_Total data and renamed ‘sleep_day’ to ‘date’

``` r
cleaned_sleep_Total <- clean_names(sleep_Total) %>% 
  rename(date = sleep_day)

cleaned_sleep_Total$date <- mdy_hms(cleaned_sleep_Total$date)
```

7- Cleaned hourlyCalories data and changed the date format to
YYYY-MM-DD-HH-MM

``` r
cleaned_hourly_Calories <- clean_names(hourlyCalories)

cleaned_hourly_Calories$activity_hour <- mdy_hms(cleaned_hourly_Calories$activity_hour)
```

Since all the data frames have ‘id’ in common, let’s check how many
unique participants and rows are in each dataframe:

``` r
n_distinct(cleaned_daily_fitness$id)
```

    ## [1] 33

``` r
n_distinct(cleaned_sleep_Total$id)
```

    ## [1] 24

``` r
nrow(cleaned_sleep_Total)
```

    ## [1] 413

``` r
nrow(unique(cleaned_sleep_Total))
```

    ## [1] 410

8- Removed duplicate rows found in ‘cleaned_sleep_total’

``` r
cleaned_sleep_Total <- unique(cleaned_sleep_Total)

nrow(cleaned_sleep_Total)
```

    ## [1] 410

9- Note: Not all participants logged sleep data, as such, merge data
frames by ‘id’ and leave out the participants that did not record sleep
data for this merge.

``` r
combined_merge <- merge(cleaned_daily_fitness, cleaned_sleep_Total)

n_distinct(combined_merge$id)
```

    ## [1] 24

10- Merging the hourly steps and calories data frames by ‘id’ and
‘activity hour’.

``` r
combined_hourly <- cleaned_hourlySteps %>% 
  inner_join(cleaned_hourly_Calories, by = c("id", "activity_hour"))
```

11- Merging average sleep and average steps taken by participants and
creating a new data frame ‘sleep_vs_steps’.

``` r
avg_sleeping_mins <- combined_merge %>% 
  group_by(id) %>% 
  summarize(avg_sleeping_mins = mean(total_minutes_asleep))

avg_total_steps <- combined_merge %>% 
  group_by(id) %>% 
  summarize(avg_total_steps = mean(total_steps))

sleep_vs_steps <- merge(avg_sleeping_mins, avg_total_steps)
```

Looking at a summary of the combined_merge data

``` r
combined_merge %>% 
  select(total_steps,
         calories,
         total_minutes_asleep,
         total_time_in_bed) %>% 
  summary()
```

    ##   total_steps       calories    total_minutes_asleep total_time_in_bed
    ##  Min.   :   17   Min.   : 257   Min.   : 58.0        Min.   : 61.0    
    ##  1st Qu.: 5189   1st Qu.:1841   1st Qu.:361.0        1st Qu.:403.8    
    ##  Median : 8913   Median :2207   Median :432.5        Median :463.0    
    ##  Mean   : 8515   Mean   :2389   Mean   :419.2        Mean   :458.5    
    ##  3rd Qu.:11370   3rd Qu.:2920   3rd Qu.:490.0        3rd Qu.:526.0    
    ##  Max.   :22770   Max.   :4900   Max.   :796.0        Max.   :961.0

Summary of combined_hourly data

``` r
combined_hourly %>% 
  select(activity_hour,
         step_total,
         calories) %>% 
  summary()
```

    ##  activity_hour                      step_total         calories     
    ##  Min.   :2016-04-12 00:00:00.00   Min.   :    0.0   Min.   : 42.00  
    ##  1st Qu.:2016-04-19 01:00:00.00   1st Qu.:    0.0   1st Qu.: 63.00  
    ##  Median :2016-04-26 06:00:00.00   Median :   40.0   Median : 83.00  
    ##  Mean   :2016-04-26 11:46:42.58   Mean   :  320.2   Mean   : 97.39  
    ##  3rd Qu.:2016-05-03 19:00:00.00   3rd Qu.:  357.0   3rd Qu.:108.00  
    ##  Max.   :2016-05-12 15:00:00.00   Max.   :10554.0   Max.   :948.00

Summary of sleep_vs_steps data

``` r
sleep_vs_steps %>% 
  select(avg_sleeping_mins, 
         avg_total_steps) %>% 
  summary()
```

    ##  avg_sleeping_mins avg_total_steps
    ##  Min.   : 61.0     Min.   : 1490  
    ##  1st Qu.:336.3     1st Qu.: 5106  
    ##  Median :417.2     Median : 8336  
    ##  Mean   :377.4     Mean   : 7880  
    ##  3rd Qu.:449.3     3rd Qu.: 9394  
    ##  Max.   :652.0     Max.   :19079

### According to the U.S. Department of Health and Human Services, most adults need 7 or more hours of good-quality sleep each night. As such, I am filtering participants that get less than 7 hours sleeping and less than the group average in steps taken daily.

``` r
sleep_vs_steps %>%
  filter(avg_sleeping_mins <= 420,
         avg_total_steps <= 7890)
```

    ##           id avg_sleeping_mins avg_total_steps
    ## 1 1927972279          417.0000        1490.000
    ## 2 2320127002           61.0000        5079.000
    ## 3 4020332650          349.3750        6596.750
    ## 4 4445114986          385.1786        4756.179
    ## 5 6775888955          349.6667        3499.000
    ## 6 7007744171           68.5000        5115.500

## Analysis

… what hours they can use a bump in motivation from a Bellabeat
membership and perhaps a reminder on their smart device- if nothing
wrong, give some mindfulness, stress relief from app) also which day of
the week is more active perhaps?? OR who slept more burnt more calories
in the day for plot??? FILTER OUT PARTICIPANTS THAT HAVE LESS THAN 8HRS
OF SLEEP AND RECOMMEND IN MY ANALYSIS TO USE APP TO HELP THEM SLEEP,
WELLNESS SIDE OF APP

### Now that we have some data cleaned and merged, we can begin some exploratory analysis

#### Figure 1

Checking out if more sleep on average, leads to more average total steps
per day

``` r
ggplot(data = sleep_vs_steps, mapping = aes(x = avg_sleeping_mins, y = avg_total_steps)) +
  geom_jitter(color = "brown") + 
  geom_smooth() +
  labs(title = "Overall Average Mins Asleep vs Average Total Steps", subtitle = "Per participant", x = "Average Mins Asleep", y = "Average Total Steps")
```

    ## `geom_smooth()` using method = 'loess' and formula = 'y ~ x'

![](google_capstone_project_bellabeat_files/figure-gfm/unnamed-chunk-25-1.png)<!-- -->

As shown, it does NOT seem, at least for these 30 women, that more sleep
on average, leads to more average total steps. Running a correlation
test seems to confirm this.

``` r
cor(sleep_vs_steps$avg_sleeping_mins, sleep_vs_steps$avg_total_steps)
```

    ## [1] -0.06633235

#### Figure 2

What days of the week are participants most likely to burn the most
calories?

``` r
week_dayz <- factor(combined_merge$week_day, levels = c("Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday", "Sunday"))

ggplot(data = combined_merge, mapping = aes(x = week_dayz, y = calories, color = week_dayz)) +
  geom_point(position = "jitter", stat = "identity") +
  labs(title = "Calories burnt throughout the week") +
  ylab("Calories") +
  scale_x_discrete("Week Day", guide = guide_axis(angle = 90)) +
  scale_color_discrete("Legend")
```

![](google_capstone_project_bellabeat_files/figure-gfm/unnamed-chunk-27-1.png)<!-- -->

Thursdays and Saturdays seem like the days participants are burning the
most calories, with Fridays and Sundays being the least.

#### Figure 3

Does more time asleep lead to a more active day? Comparing the total
minutes asleep and total steps taken to see how they vary during the
week.

``` r
ggplot(data = combined_merge, mapping = aes(x = total_minutes_asleep, y = total_steps, color = week_dayz, size = total_time_in_bed)) +
  geom_point(alpha = 0.5) +
  labs(title = "Total Minutes Asleep vs Total Steps", subtitle = "Per Weekday", x = "Total Minutes Asleep", y = "Total Steps") +
  theme(legend.position = "right") +
  scale_color_discrete("Week Day")
```

![](google_capstone_project_bellabeat_files/figure-gfm/unnamed-chunk-28-1.png)<!-- -->

#### Figure 4

Hourly Activity: what hours are the participants most active?

``` r
twenty_four_hrs <- format(as.POSIXct(combined_hourly$activity_hour), format = "%H")

ggplot(data = combined_hourly, aes(x = twenty_four_hrs, y = step_total, fill = twenty_four_hrs)) +
  geom_bar(stat = "identity") +
  labs(title = "Hourly Steps", x = "Activity Hour", y = "Step Total")
```

![](google_capstone_project_bellabeat_files/figure-gfm/unnamed-chunk-29-1.png)<!-- -->

``` r
combined_merge %>% 
  group_by(id) %>% 
  summarize(sumz = sum(total_steps))
```

    ## # A tibble: 24 × 2
    ##            id   sumz
    ##         <dbl>  <dbl>
    ##  1 1503960366 310142
    ##  2 1644430081  31871
    ##  3 1844505072  10431
    ##  4 1927972279   7450
    ##  5 2026352035 157323
    ##  6 2320127002   5079
    ##  7 2347167796 127998
    ##  8 3977333714 314104
    ##  9 4020332650  52774
    ## 10 4319703577 185261
    ## # … with 14 more rows

This graph shows that the participants are most active at 6-7pm in the
weekdays. Graph also shows there’s room for improvement at the 3pm time
slot as there is considerable decline at that time.

### Recommendations

1.  The Bellabeat Membership (app) should notify it’s users when they
    ‘fall’ out of the average inactivity period during certain times of
    the day. One way to monitor activity is by steps taken per hour and
    if the app calculates that the user would not make their recommended
    steps per day, give them notifications and reminders.

2.  Bellabeat should incorporate the Bellabeat membership into everyday
    living and activity monitoring. If for some reason a user has been
    in bed but spending less time sleeping for a couple days, someone
    from Bellabeat could do a quick check in on the user, either via
    text or video.

3.  Wellness sessions can also take place right from the Bellabeat
    membership app\* that allows users to unwind on a stressful day,
    while also incorporating light exercises, for example yoga. To
    capitalize on this, looking into exercise equipment, like an
    stationary bike, could be a new revenue stream.

4.  Users of the Bellabeat membership app\* should have access to chefs’
    personalized menu, that can cater meals that heal, nurture and
    rejuvenate users for another day.

#### Note: Data collection was not done in an unbiased why and there needs to be a larger sample size than the 30 users that participated in this study. Furthermore, a longer data collection time period needs to be adheard to, than the 2 months given with this data-set.
