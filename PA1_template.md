---
title: "An Analysis of Personal Activity Monitoring Data"
author: "ctballentine"
date: "10/19/2014"
output: html_document
---

This initial code reads in the the .csv data and produces an alternate dataframe stripped of NA values


```r
Activity<-read.csv("~/RepData_PeerAssessment1/activity.csv")
NActivity<-Activity[!is.na(Activity[,1]),]
```

Next we will take the sum of each day's data and store it in a variable called SumData


```r
SumData<-by(Activity$steps,Activity$date,sum)
```

Which we then use to make a histogram of all the daily totals

![plot of chunk unnamed-chunk-3](figure/unnamed-chunk-3.png) 

We will now print out the mean and median of the daily sum of steps


```r
MeanData<-mean(SumData,na.rm=TRUE)
MeanData
```

```
## [1] 10766
```

```r
MedianData<-median(SumData,na.rm=TRUE)
MedianData
```

```
## [1] 10765
```

We will take the mean measure of steps taken by time interval across all days and store it in a variable called MeanData1

```r
MeanData1<-by(NActivity$steps,NActivity$interval,mean)
```
which is plotted out here

![plot of chunk unnamed-chunk-6](figure/unnamed-chunk-6.png) 

We will use the following code to determine which time interval has the maximum average number of steps

```r
MeanData1[MeanData1==max(MeanData1)]
```

```
##   835 
## 206.2
```

We will use this code to determine the  number of NA values in the 'steps' column of the original data frame

```r
length(Activity[is.na(Activity[,1]),1])
```

```
## [1] 2304
```


This next block of code will

-Locate values in the Steps variable with NA values

-Determine the mean value for the time interval accosciate with the missing data

-Replace the NA value with the mean value for the time interval

-Recalculate the daily sum totals of steps taken


```r
IntervalKey<-(Activity[is.na(Activity[,1]),3])
Replacement<-MeanData1[IntervalKey==names(MeanData1)]
Replacement<-Replacement[!is.na(Replacement)]
Activity[is.na(Activity[,1]),1]<-Replacement
SumData<-by(Activity$steps,Activity$date,sum)
```

We will now draw another histogram, with the NA values replaced
![plot of chunk unnamed-chunk-10](figure/unnamed-chunk-10.png) 

As we can see, the general shape of the histogram has changed very little and, as the code below demonstrates, neither have the mean or median values


```r
MeanData<-mean(SumData)
MeanData
```

```
## [1] 10766
```

```r
MedianData<-median(SumData)
MedianData
```

```
## 2012-11-04 
##      10766
```


The next chunk of code will determine the weekday of each day in the data frame, the build a seperate column to determine whether each day listed is a weekend or a weekday

```r
#Discovers weekdays of dataset
ActivityDay<-weekdays(strptime(Activity[,2], format = "%Y-%m-%d"))
#Creates index of weekend days which is labeled WeekIndex
WeekIndex<-ActivityDay==c("Saturday","Sunday")
Weekend<-WeekIndex
#Adds Column of Weekday/Weeknd to Data Frame
Weekend[WeekIndex]<-"Weekend"
Weekend[!WeekIndex]<-"Weekday"
Activity[,4]<-Weekend
#Seperates dataframe by Weekday/Weekend
WeekendActivity<-Activity[WeekIndex,]
WeekdayActivity<-Activity[!WeekIndex,]
MeanDataWE<-by(WeekendActivity$steps,WeekendActivity$interval,mean)
MeanDataWD<-by(WeekdayActivity$steps,WeekdayActivity$interval,mean)
```

We will then do a split frame plot of the mean number of steps along each time interval for both Weekends (MeanDataWE) and Weekdays (MeanDataWD)
![plot of chunk unnamed-chunk-13](figure/unnamed-chunk-13.png) 

