---
title: "Peer Assessment 1"
author: "Sophia Melone"
date: "September 18, 2015"
output: html_document
---

###Introduction

This assignment uses data from from a personal activity monitoring device.  The device collects data at 5 minute intervals throughout the day based on the users movement. The data consists of two months of data from anonymouse individuals collected during the months of October and November, 2012 and include the number of steps taken in the 5 min intervals each day.  

The data for this assignment can be downloaded [here] (https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2Factivity.zip) at the course website.

###Assignment

**Loading and processing data**
Dowload the data from link above and set working directory using setwd().  Next read in the data: 


```r
#read in data
activity <- read.csv("activity.csv")
```

**Total number of steps taken per day?**


```r
#Aggregate based on steps and date, ommiting NAs
daily.totals <-aggregate(data=activity[is.na(activity$steps)!="NA",],steps~date, FUN=sum)

#Create histogram based on number of steps per day
hist(daily.totals$steps, xlab="Total Steps", main="Average Number of Steps per Day")
```

![plot of chunk unnamed-chunk-2](figure/unnamed-chunk-2-1.png) 

```r
#Calculate mean
mean(daily.totals$steps, na.rm=TRUE)
```

```
## [1] 10766.19
```

```r
#Calculate median
median(daily.totals$steps, na.rm=TRUE)
```

```
## [1] 10765
```


**What is the average daily pattern?**


```r
#Aggregate based on interval and date, ommiting NAs
daily.interval <-aggregate(data=activity[is.na(activity$interval)!="NA",],steps~interval, FUN=mean)

#Create time series plot
plot(daily.interval$interval, daily.interval$steps, type="l", main= "Average Number of Steps per Day accross Intervals")
```

![plot of chunk unnamed-chunk-3](figure/unnamed-chunk-3-1.png) 

```r
#Find which interval contains the maximum number of steps
daily.interval[which.max(daily.interval$steps),]$interval
```

```
## [1] 835
```

**Input missing values**

```r
#Find the number of missing values
sum(complete.cases(activity)==FALSE)
```

```
## [1] 2304
```

```r
#Create a new dataset and replace the missing values with average number os steps
newactivity <- activity
newactivity$steps[is.na(newactivity$steps)]<-mean(newactivity$steps,na.rm=T)

#Aggregate based on steps and date
daily.avgsteps <-aggregate(data=newactivity,steps~date, FUN=sum)

#Create a histrogram with this new dataset to show Average Number of Steps per Day
hist(daily.avgsteps$steps, xlab="Total Steps", main="Average Number of Steps per Day")
```

![plot of chunk unnamed-chunk-4](figure/unnamed-chunk-4-1.png) 

```r
#Calculate the mean
mean(daily.avgsteps$steps)
```

```
## [1] 10766.19
```

```r
#Calcualte the median
median(daily.avgsteps$steps)
```

```
## [1] 10766.19
```

- What is the impact of imputing missing data on the estimates of the total daily number of steps?

Adding these average values for number of steps makes our data less accurate.  This is especially obvious when looking at our mean and median for this data set, which are now equal.  This was not the case in the original data set, where NAs were omitted. 

***Are there differences in activity patterns between weekdays and weekends?***

```r
#create new column showing day of week
newactivity$weekday<-weekdays(as.Date(newactivity$date))

#now we can subset based on weekend vs weekday
weekend<-newactivity[(grep("Saturday|Sunday", newactivity$weekday)),]
weekday<-newactivity[-(grep("Saturday|Sunday", newactivity$weekday)),]

#aggregate based on the intervals during weekends/weekdays
weekendagg<-aggregate(data=weekend, steps~interval, FUN=mean)
weekdayagg<-aggregate(data=weekday, steps~interval, FUN=mean)

#plot the data 
par(mfrow=c(2,1))
plot(weekendagg$interval, weekendagg$steps, type="l", main="Average Steps Taken Accross All Weekends")
plot(weekdayagg$interval, weekdayagg$steps, type="l", main="Average Steps Taken Accross All Weekdays")
```

![plot of chunk unnamed-chunk-5](figure/unnamed-chunk-5-1.png) 

