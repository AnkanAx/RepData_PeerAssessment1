---
title: "Reproducible Research: Peer Assessment 1"
output: 
  html_document:
    keep_md: true
---


## Loading and preprocessing the data


```r
act<-read.csv("/Users/ankan/Documents/data/activity.csv", header=TRUE)
```

ändra till act=read.csv('activity.csv', header=T) i git????

## What is mean total number of steps taken per day?

#### Histogram of the total number of steps taken each day.


```r
library(dplyr)
```

```
## 
## Attaching package: 'dplyr'
```

```
## The following objects are masked from 'package:stats':
## 
##     filter, lag
```

```
## The following objects are masked from 'package:base':
## 
##     intersect, setdiff, setequal, union
```

```r
act2 <- act %>%
        select(steps, date) %>%
        group_by(date) %>%
        summarize_each(list(sum))
hist(act2$steps, xlab="steps", main="total number of steps a day")
```

![](PA1_template_files/figure-html/unnamed-chunk-2-1.png)<!-- -->

#### The mean and median total number of steps taken per day.


```r
mean(act2$steps, na.rm = TRUE)
```

```
## [1] 10766.19
```

```r
median(act2$steps, na.rm = TRUE)
```

```
## [1] 10765
```

## What is the average daily activity pattern?

#### Time series plot of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis).


```r
actnaomit<-na.omit(act)
act3 <- actnaomit %>%
        select(steps, interval) %>%
        group_by(interval) %>%
        summarize_each(list(mean))
plot(factor(act3$interval),act3$steps, type="l", xlab="Interval", ylab="Mean Steps")
```

![](PA1_template_files/figure-html/unnamed-chunk-4-1.png)<!-- -->

#### The 5-minute interval, on average across all the days in the dataset, that contains the maximum number of steps is 0835-0840.


```r
act4 <- act3 %>%
        filter(steps==max(act3$steps))
act4
```

```
## # A tibble: 1 x 2
##   interval steps
##      <int> <dbl>
## 1      835  206.
```

## Imputing missing values

#### The total number of missing values in the dataset (i.e. the total number of rows with NAs) is 2304.


```r
length(act$steps)-length(actnaomit$steps)
```

```
## [1] 2304
```

#### New dataset that is equal to the original dataset but with the missing data filled in as the mean value for all non missing observations.


```r
act<-mutate(act,nasteps=mean(steps,na.rm=TRUE))
actnaomit<-na.omit(act)
act5<-subset(act, is.na(steps))
act5$steps<-act5$nasteps
act6<-rbind(act5,actnaomit)
```

#### Histogram of the total number of steps taken each day with the new dataset.


```r
act7 <- act6 %>%
        select(steps, date) %>%
        group_by(date) %>%
        summarize_each(list(sum))
hist(act7$steps, xlab="steps", main="total number of steps a day")
```

![](PA1_template_files/figure-html/unnamed-chunk-8-1.png)<!-- -->

#### The mean and median total number of steps taken per day in the new dataset.


```r
mean(act7$steps)
```

```
## [1] 10766.19
```

```r
median(act7$steps)
```

```
## [1] 10766.19
```

The values from the manipulted dataset does not differ from the original. By manipulating the data with the mean value for missing values does not give any impact on the estimates.

## Are there differences in activity patterns between weekdays and weekends?

#### Create a new factor variable in the dataset with two levels -- "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.


```r
library(lubridate)
```

```
## 
## Attaching package: 'lubridate'
```

```
## The following object is masked from 'package:base':
## 
##     date
```

```r
act6$weekday<-wday(act6$date)
act6$weekday<-replace(act6$weekday,act6$weekday==1|act6$weekday==7,'weekend')
act6$weekday<-replace(act6$weekday,act6$weekday!='weekend','weekday')
```

#### Panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis).


```r
act8 <- act6 %>%
        select(steps, interval, weekday) %>%
        group_by(interval,weekday) %>%
        summarize_each(list(mean))
library(lattice)
xyplot(steps ~ interval | weekday, data = act8, type="l", layout = c(1, 2))
```

![](PA1_template_files/figure-html/unnamed-chunk-11-1.png)<!-- -->
