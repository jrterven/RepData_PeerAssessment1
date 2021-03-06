---
title: "Peer Assessment 1"
author: "Juan R. Terven"
date: "15 de noviembre de 2014"
output: html_document
---

##Introduction
It is now possible to collect a large amount of data about personal movement using activity monitoring devices such as a Fitbit, Nike Fuelband, or Jawbone Up. These type of devices are part of the "quantified self"" movement, a group of enthusiasts who take measurements about themselves regularly to improve their health, to find patterns in their behavior, or because they are tech geeks. But these data remain under-utilized both because the raw data are hard to obtain and there is a lack of statistical methods and software for processing and interpreting the data.

This assignment makes use of data from a personal activity monitoring device. This device collects data at 5 minute intervals through out the day. The data consists of two months of data from an anonymous individual collected during the months of October and November, 2012 and include the number of steps taken in 5 minute intervals each day.

##Data

The data for this assignment can be downloaded from the course web site:
Dataset: [Activity monitoring data](https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2Factivity.zip)
The variables included in this dataset are:

**steps:** Number of steps taking in a 5-minute interval (missing values are coded as NA)

**date:** The date on which the measurement was taken in YYYY-MM-DD format

**interval:** Identifier for the 5-minute interval in which measurement was taken


The dataset is stored in a comma-separated-value (CSV) file and there are a total of 17,568 observations in this dataset.

##Assignment
###Loading and preprocessing the data
Load the data:


```r
data <- read.csv("activity.csv");
```

###What is mean total number of steps taken per day?

Calculate and report the mean and median total number of steps taken per day


```r
means <- aggregate(cbind(steps) ~ date, data, mean)
median <- aggregate(cbind(steps) ~ date, data, median)
```

```
## Error: el objeto 'FUN' de modo 'function' no fue encontrado
```

The following is a histogram of the total number of steps taken each day

![plot of chunk unnamed-chunk-3](figure/unnamed-chunk-3.png) 

###What is the average daily activity pattern?

Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)

```r
avgPer5min <- aggregate(cbind(steps) ~ interval, data, mean)
plot(avgPer5min$interval,avgPer5min$steps,type="l",
     xlab="5-min intervals",ylab="Avg Steps",col="blue")
```

![plot of chunk unnamed-chunk-4](figure/unnamed-chunk-4.png) 

Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?


```r
avgPer5min[which.max(avgPer5min$steps),"interval"]
```

```
## [1] 835
```

###Inputing missing values

Note that there are a number of days/intervals where there are missing values (coded as NA).
The presence of missing days may introduce bias into some calculations or summaries of the data.

#### 1. Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)

```r
length(which(is.na(data$steps)))
```

```
## [1] 2304
```

#### 2. Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated and 
#### 3. Create a new dataset that is equal to the original dataset but with the missing data filled in.

```r
data2 <- data
nas <- which(is.na(data$steps)) # rows with NAs in steps
int <- data2[nas,"interval"]  # intervals where steps are NAs

# averages of 5 min intervals of each selected interval
avgs <- avgPer5min[which(avgPer5min$interval == int),"steps"] 
# Fill the NAs with mean of 5-min interval 
data2[nas,"steps"] <- avgs
```

#### 4. Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?

```r
means2 <- aggregate(cbind(steps) ~ date, data2, mean)
#median2 <- aggregate(cbind(steps) ~ date, data2, median)
hist(means2$steps)
```

![plot of chunk unnamed-chunk-8](figure/unnamed-chunk-8.png) 


### Are there differences in activity patterns between weekdays and weekends?
Create a new factor variable in the dataset with two levels -- 
"weekday" and "weekend" indicating whether a given date is a weekday or weekend day.


```r
data2$day <- weekdays(as.Date(data2$date))
data2[which(data2$day %in% c("Monday","Tuesday","Wednesday","Thursday","Friday")),"day"] <- "Weekday"
data2[which(data2$day %in% c("Saturday","Sunday")),"day"] <- "Weekend"
WE <- aggregate(cbind(steps) ~ interval, subset(data2, day == "Weekend"), mean)
WE$day <- "Weekend"
WD <- aggregate(cbind(steps) ~ interval, subset(data2, day == "Weekday"), mean)
WD$day <- "Weekday"
data3 <- rbind(WD,WE)
data3 <- transform(data3, day = factor(day))
```

Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval 
(x-axis) and the average number of steps taken, averaged across all weekday days or 
weekend days (y-axis). 


```r
library(datasets)
library(lattice)
xyplot(steps ~ interval | day, data = data3, t="l", layout = c(1,2), xlab = "Interval",
       ylab = "Number of steps")
```

![plot of chunk unnamed-chunk-10](figure/unnamed-chunk-10.png) 




