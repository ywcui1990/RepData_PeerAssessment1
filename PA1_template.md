# Reproducible Research: Peer Assessment 1
Author: "Yuwei Cui"
Date: "July 14, 2014"

## Loading and preprocessing the data


```r
data<- read.csv("activity.csv", sep=",",header = TRUE)
data$date <- as.Date(data$date, "%Y-%m-%d") 
```

## What is mean total number of steps taken per day?


```r
TotalStepPerDay =  tapply(data$steps, as.factor(data$date), sum, na.rm = TRUE)
MeanStepPerDay = mean(TotalStepPerDay)
MedianStepPerDay = mean(TotalStepPerDay)
hist(TotalStepPerDay, main="Histogram of Total Step", xlab="Total Step")
```

![plot of chunk unnamed-chunk-2](figure/unnamed-chunk-2.png) 

The mean of total number of steps is 9354.2295.
The median of total number of steps is 9354.2295.

## What is the average daily activity pattern?


```r
intervalvalues = unique(data$interval)
ActivityDuringDay =  tapply(data$steps, as.factor(data$interval), mean, na.rm = TRUE)
plot(intervalvalues, ActivityDuringDay, type="l", main="Average Daily Activity Pattern",
     xlab=" time (minuites) ", ylab = " average number of steps")
```

![plot of chunk unnamed-chunk-3](figure/unnamed-chunk-3.png) 

```r
maxNumberStep = which(ActivityDuringDay %in% max(ActivityDuringDay))
```

The 104th 5 minuite interval contains the maximal number of steps

## Imputing missing values

### Calculate and report the total number of missing values in the dataset:


```r
missingValues = is.na(data$steps)
totalNA = sum(missingValues)
```

The total number of missing values in the dataset is 2304

### Filling in all of the missing values in the dataset.

We use the mean for that 5-minute interval to fill in the missing values

```r
newData = data # make a copy of the data
for(i in 1:nrow(newData)){
    if(missingValues[i]){
        # identify the 5-minute interval of the missing data
        currentInterval = newData$interval[i]        
        # use average of that 5-minute interval to fill in the missing values
        j = which(intervalvalues %in% currentInterval)
        newData$steps[i] = ActivityDuringDay[j]
    }
}
```

### recalculate histogram of total number of steps taken each day


```r
TotalStepPerDayNew =  tapply(newData$steps, as.factor(newData$date), sum, na.rm = TRUE)
hist(TotalStepPerDayNew, main="Histogram of Total Step after filling in missing values", xlab="Total Step")
```

![plot of chunk unnamed-chunk-6](figure/unnamed-chunk-6.png) 

Recalculate the mean and median of total number of steps taken each day

```r
MeanStepPerDayNew = mean(TotalStepPerDayNew)
MedianStepPerDayNew = mean(TotalStepPerDayNew)
```
After imputing missing data, the mean of total number of steps is 1.0766 &times; 10<sup>4</sup>.
The median of total number of steps is 1.0766 &times; 10<sup>4</sup>.

## Are there differences in activity patterns between weekdays and weekends?


```r
DayOfWeek = weekdays(data$date) # extract day of week
wkend = (DayOfWeek %in% c("Sunday", "Saturday"))
# create a new factor value with levels "weekday" and "weekend"
DayOfWeek[wkend] = "weekend"
DayOfWeek[!wkend] = "weekday"
DayOfWeek = as.factor(DayOfWeek)
```

Make a panel plot containing a time series plot of the 5-minute interval (x-axis)
and the average number of steps taken, averaged across all weekday days or weekend days (y-axis).


```r
# select weekday data
weekdayData = newData[DayOfWeek=="weekday",]
ActivityWeekday =  tapply(weekdayData$steps, as.factor(weekdayData$interval), mean, na.rm = TRUE)

# select weekend data
weekendData = newData[DayOfWeek=="weekend",]
ActivityWeekend =  tapply(weekendData$steps, as.factor(weekendData$interval), mean, na.rm = TRUE)

par(mfrow=c(2,1))
par(mar = c(1,4,1,4)) # set margins properly
par(oma=c(1,3,3,1))  # all sides have 3 lines of space 
plot(intervalvalues, ActivityWeekday, type="l", main="weekday",
     xlab=" Interval ", ylab = "", xaxt="n")
plot(intervalvalues, ActivityWeekend, type="l", main="weekend",
     xlab=" Interval ", ylab = "")

mtext("Number of steps", side = 2, line=1, outer=TRUE)
```

![plot of chunk unnamed-chunk-9](figure/unnamed-chunk-9.png) 
