---
title: "Reproducible Research: Peer Assessment 1"
output: 
  html_document:
    keep_md: true
---


## Loading and preprocessing the data

```r
library(knitr)
library(ggplot2)
library(lattice)

DatasetURL <- "https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2Factivity.zip"
DatasetZip <- "repdata_data_activity.zip"
DatasetFile <- "activity.csv" 

# Check if the dataset already exists / downloaded. If not, then download the file
if (!file.exists(DatasetFile)) {
        download.file(DatasetURL, DatasetZip)
        unzip(DatasetZip)
}

activity_data <- read.csv(DatasetFile)
```


## What is mean total number of steps taken per day?

```r
#1. Calculate the total number of steps taken per day, exclude the dates with missing values (i.e. NA's)
daily_total <- tapply(activity_data$steps, activity_data$date, sum, na.rm=TRUE)

#2. Make a histogram of the total number of steps taken each day
hist(daily_total, main = "Steps per day", col = "gray")
```

![](PA1_template_files/figure-html/MeanValue-1.png)<!-- -->

```r
#3. Calculate and report the mean and median of the total number of steps taken per day
MeanValue <- mean(daily_total)
MedianValue <- median(daily_total)
```
The answer is - Mean: 9354.2295082 & median: 10395 steps.


## What is the average daily activity pattern?

```r
#1. Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)
        
steps_interval <- aggregate(steps~interval, data=activity_data, mean, na.rm=TRUE)
plot(steps~interval, data=steps_interval, type="l", col="magenta", xlab = "5 min Interval")
```

![](PA1_template_files/figure-html/AvgDaily-1.png)<!-- -->

```r
#2. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?

int <- steps_interval[which.max(steps_interval$steps), ]$interval
```
It is the **835th** interval, which contains the maximum number of steps.


## Imputing missing values

```r
#1. Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)
missing_values <- sum(is.na(activity_data$steps))

#2. Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.

# Using the strategy for replacing the missing steps values with the mean value. 
# Now there count be 2 scearios here. 
#  A. There could be few missing values in a day - Here replace the missing values with mean for that day
#  B. Missing values for all intervals in that day - Here, relace with the overall mean

daily_mean <- aggregate(steps ~ date, activity_data, mean, na.rm=TRUE)

# Calculate the overall mean value for steps. This will be used in case there is no steps data available for a given date. (E.g. 2012-10-01 has no steps data). In that case, the steps value will be replaced with the overall mean. 

overall_mean <- round(mean(activity_data$steps, na.rm = TRUE))

#3. Create a new dataset that is equal to the original dataset but with the missing data filled in.

#Create a copy of the activity data first, for replacing the missing values in the the new copy
new_activity_data <- activity_data
count1 <- 0     # Counts for replacing missing values with mean for that day, if available
count2 <- 0     # Counts for replacing missing values with overall mean value i.e. no data available for any interval in that day

for(i in 1:nrow(new_activity_data)){
        if(is.na(new_activity_data[i, ]$steps)) {
                date1 <- new_activity_data[i, ]$date
                rec1 <- subset(daily_mean, daily_mean$date == date1)
                if (nrow(rec1) > 0) {
                        new_activity_data[i, ]$steps <- rec1$steps
                        count1 <- count1 + 1
                } else {
                        new_activity_data[i, ]$steps <- overall_mean
                        count2 <- count2 + 1
                }
        }
}

#new_activity_data now has all the missing values replaced as per the devised strategy

#4. Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?

# Calculate the total number of steps taken per day
new_daily_total <- tapply(new_activity_data$steps, new_activity_data$date, sum, na.rm=TRUE)

# Make a histogram of the total number of steps taken each day
hist(new_daily_total, main = "Revised Steps per day after replacing Missing values", col="gray")
```

![](PA1_template_files/figure-html/MisingValues-1.png)<!-- -->

```r
# Calculate and report the mean and median of the total number of steps taken per day

new_MeanValue <- mean(new_daily_total)
new_MedianValue <- median(new_daily_total)

if (new_MeanValue == MeanValue) {
        print("No change to the mean value")
} else {
        print("Mean values changed after filling the missing values")
}
```

```
## [1] "Mean values changed after filling the missing values"
```

```r
if (new_MedianValue == MedianValue) {
        print("No change to the median value")
} else {
        print("Median values changed after filling the missing values")
}
```

```
## [1] "Median values changed after filling the missing values"
```
Total 2304 rows have missing values for steps.

Total 0 values were filled with mean value for that day.

Total 2304 values were filled with overall mean value.

New Mean of steps per day is: 1.0751738\times 10^{4} & median: 1.0656\times 10^{4} steps.


## Are there differences in activity patterns between weekdays and weekends?

```r
#1. Create a new factor variable in the dataset with two levels – “weekday” and “weekend” indicating whether a given date is a weekday or weekend day.

new_activity_data$day_type <- ifelse(as.POSIXlt(as.Date(new_activity_data$date))$wday%%6==0, "weekend", "weekday")

#2. Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). See the README file in the GitHub repository to see an example of what this plot should look like using simulated data.

new_stepsinterval <- aggregate(steps ~ interval + day_type, new_activity_data, mean)

xyplot(steps ~ interval | factor(day_type), data = new_stepsinterval, aspect=1/2, type="l", xlab = "5 min Interval")
```

![](PA1_template_files/figure-html/ActivityPatterns-1.png)<!-- -->
