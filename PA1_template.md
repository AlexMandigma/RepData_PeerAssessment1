---
title: 'Reproducible Research: Peer Assessment 1'
output: 
  html_document:
    keep_md: true
---
Loading and preprocessing the data
```{r}
library(ggplot2)
library(plyr)
library(lattice) 
library(knitr)
Activity <- read.csv("Activity.csv")
Activity$day <- weekdays(as.Date(activity$date))
Activity$DateTime<- as.POSIXct(activity$date, format="%Y-%m-%d")
removeNa <- Activity[!is.na(Activity$steps),]
```

What is mean total number of steps taken per day?
Calculate the total number of steps taken per day
```{r}
meanSteps <- aggregate(Activity$steps ~ Activity$date, FUN=sum, )
colnames(meanSteps)<- c("Date", "Steps")
```
histogram
![186](https://github.com/AlexMandigma/RepData_PeerAssessment1/blob/master/unnamed-chunk-186-1.png)

Calculate and report the mean and median of the total number of steps taken per day
```{r}
as.integer(mean(meanSteps$Steps))
as.integer(median(meanSteps$Steps))
```
10766 steps-average

10765 steps-meadian.


What is the average daily activity pattern?

Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)
![188](https://github.com/AlexMandigma/RepData_PeerAssessment1/blob/master/unnamed-chunk-188-1.png)
Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?
```{r}
maxSteps <- max(averageSteps$Avg)
averageSteps[averageSteps$Avg==maxSteps,1]
```
835th interval


Imputing missing values
Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)
```{r}
nrow(Activity[is.na(Activity$steps),])
```
2304 missing values

Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. 
```{r}
averageMeanSteps <- ddply(removeNa, .(interval, day), summarize, Avg = mean(steps))
nullData<- Activity[is.na(Activity$steps),]
fillData<-merge(nullData, averageMeanSteps, by=c("interval", "day"))
```
substituted with the average and mean steps of the five-minute interval


Create a new dataset that is equal to the original dataset but with the missing data filled in.
```{r}
fillData2<- fillData[,c(6,4,1,2,5)]
colnames(fillData2)<- c("steps", "date", "interval", "day", "DateTime")
merged <- rbind(removeNa, fillData2)
meanSteps2 <- aggregate(merged$steps ~ merged$date, FUN=sum, )
colnames(meanSteps2)<- c("Date", "Steps")
as.integer(mean(meanSteps2$Steps))
```
mean -10821
```{r}
as.integer(median(meanSteps2$Steps))
```
median - 11015

Only minor impact but does not distort the data

Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day.
![194](https://github.com/AlexMandigma/RepData_PeerAssessment1/blob/master/unnamed-chunk-194-1.png)

Are there differences in activity patterns between weekdays and weekends?

Create a new factor variable in the dataset with two levels - "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.
```{r}
merged$DayCategory <- ifelse(merged$day %in% c("Saturday", "Sunday"), "Weekend", "Weekday")
```
Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis).

![196](https://github.com/AlexMandigma/RepData_PeerAssessment1/blob/master/unnamed-chunk-196-1.png)
people tend to be more active on weekdays

