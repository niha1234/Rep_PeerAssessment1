---
title: "Reproducible Research: Peer Assessment 1"
output: 
  html_document:
    keep_md: true
---


## Loading and preprocessing the data

Load the data (i.e. read.csv()) 
Process/transform the data (if necessary) into a format suitable for your analysis


```r
library(dplyr)
library(datasets)

  if(!file.exists("repdata-data-activity.zip")) {
     temp <- tempfile()
     setInternet2(use=TRUE)
     download.file("https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2Factivity.zip",temp,mode="wb")
     file <- unzip(temp)
     unlink(temp)
}

    numberofSteps<-read.csv(file)
```

## What is mean total number of steps taken per day?

1. Calculate the total number of steps taken per day

```r
    sumSteps<-tapply(numberofSteps$steps,numberofSteps$date,sum,na.rm=TRUE)
```
2. Make a histogram of the total number of steps taken each day
![plot of chunk unnamed-chunk-3](figure/unnamed-chunk-3-1.png) 
3. Calculate and report the mean and median of the total number of steps taken per day


```r
   mean<-mean(sumSteps)
   median<-median(sumSteps)
```

## What is the average daily activity pattern?

1. Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) 
and the average number of steps taken, averaged across all days (y-axis)
![plot of chunk unnamed-chunk-5](figure/unnamed-chunk-5-1.png) 
2. Which 5-minute interval, on average across all the days in the dataset,
contains the maximum number of steps?

```r
for(i in 1:288)
{
  if(meanSteps[i]==max(meanSteps))
  {
    name<- names(meanSteps[i])
    print(name)
    print(max(meanSteps))
  }
  
}
```

```
## [1] "835"
## [1] 206.1698
```


## Imputing missing values

1. Calculate and report the total number of missing values in the dataset
(i.e. the total number of rows with NAs)

```r
    length(which(is.na(numberofSteps$steps)))
```

```
## [1] 2304
```
2. Devise a strategy for filling in all of the missing values in the dataset.
 The strategy does not need to be sophisticated. For example, you could use 
 the mean/median for that day,or the mean for that 5-minute interval, etc.



```r
    mean1<-data.frame(steps=meanSteps,interval=names(meanSteps))

    temp<-merge(numberofSteps,mean1,by.x="interval",by.y="interval")

    temp <- within(temp,imputedSteps <- ifelse(!is.na(steps.x),steps.x,steps.y))
```

3. Create a new dataset that is equal to the original dataset but with the missing data filled in.


```r
    imputedData<-select(temp,interval,date,imputedSteps)

    converttoOriginal<-imputedData[order(as.Date(imputedData$date)),]
```
4. Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?
![plot of chunk unnamed-chunk-10](figure/unnamed-chunk-10-1.png) 
-Imputations using mean data is probably the easiest way for replacing missing values. In this plot frequency of number of steps have reduced significantly as compared plot with missing values after imputing mean data  towards the begining of the day.Mean imputation distorts relationships between variables by pulling" estimates of the correlation toward zero[Reference](http://www.stat.columbia.edu/~gelman/arm/missing.pdf)

## Are there differences in activity patterns between weekdays and weekends?
For this part the weekdays() function may be of some help here. Use the dataset with the filled-in missing values for this part. 

1. Create a new factor variable in the dataset with two levels - "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.

```r
    day<-ifelse(weekdays(as.Date(converttoOriginal$date)) %in% c('Saturday','Sunday'), "weekend", "weekday")
    ConvertedData<-data.frame(converttoOriginal,day)
```
2. Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). See the README file in the GitHub repository to see an example of what this plot should look like using simulated data.
![plot of chunk unnamed-chunk-12](figure/unnamed-chunk-12-1.png) 
