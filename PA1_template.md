---
title: "Reproducible Research: Peer Assessment 1"
output: 
  html_document:
    keep_md: true
---


## Loading and preprocessing the data

```r
library(knitr)

DatasetURL <- "https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2Factivity.zip"
DatasetZip <- "repdata_data_activity.zip"
DatasetFile <- "activity.csv" 

# Check if the dataset already exists / downloaded. If not, then download the file
if (!file.exists(DatasetFile)) {
        download.file(DatasetURL, DatasetZip)
        unzip(DatasetZip)
} 

activity_data <- read.csv(DatasetFile)
dim(activity_data)
```

```
## [1] 17568     3
```

```r
print(head(activity_data), type="html")
```

```
##   steps       date interval
## 1    NA 2012-10-01        0
## 2    NA 2012-10-01        5
## 3    NA 2012-10-01       10
## 4    NA 2012-10-01       15
## 5    NA 2012-10-01       20
## 6    NA 2012-10-01       25
```


## What is mean total number of steps taken per day?



## What is the average daily activity pattern?



## Imputing missing values



## Are there differences in activity patterns between weekdays and weekends?
