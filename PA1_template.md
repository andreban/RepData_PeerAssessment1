# Reproducible Research: Peer Assessment 1


## Loading and preprocessing the data

Unzip the data

```
## [1] "en_US.UTF-8"
```

now load the CSV

```r
    data <- read.csv('activity.csv')
    data$date <- as.Date(data$date)
    data$time <- as.POSIXct(strptime(sprintf("%04d",data$interval), format="%H%M"))
    data$dayOfWeek <- weekdays(data$date)
    data$isWeekday <- data$day != 'Saturday' & data$day != 'Sunday'
```

## What is mean total number of steps taken per day?
Lets aggregate the number of steps by date first:

```r
stepsByDay <- aggregate(steps ~ date, data, sum)
```


Now, lets plot the Histogram.

```r
hist(stepsByDay$steps, breaks=20,main="Steps Per Day By Frequency",xlab="Daily Steps")
```

![plot of chunk unnamed-chunk-4](./PA1_template_files/figure-html/unnamed-chunk-4.png) 

This is the Mean:

```r
mean(stepsByDay$steps)
```

```
## [1] 10766
```

And the Median

```r
median(stepsByDay$steps)
```

```
## [1] 10765
```


## What is the average daily activity pattern?
Aggregate steps by Time of Day, and take the mean number of steps

```r
meanStepsByTimeOfday <- aggregate(steps ~ time, data, mean)
```

Plot the chart

```r
    plot(meanStepsByTimeOfday$time, meanStepsByTimeOfday$steps, type="l", xlab="Time of Day", ylab="Mean Number of steps")
    title("Mean number of steps by Time of Day")
```

![plot of chunk unnamed-chunk-8](./PA1_template_files/figure-html/unnamed-chunk-8.png) 

The hour with the biggest average number of steps is

```r
format(meanStepsByTimeOfday$time[which.max(meanStepsByTimeOfday$steps)], "%H:%M")
```

```
## [1] "08:35"
```

which has, on average

```r
max(meanStepsByTimeOfday$steps)
```

```
## [1] 206.2
```


## Imputing missing values
Finding the Number of NA's on the original Datasets.

```r
colSums(is.na(data))
```

```
##     steps      date  interval      time dayOfWeek isWeekday 
##      2304         0         0         0         0         0
```
Whe observe that the only column that has NA's is the "steps" column.

## Are there differences in activity patterns between weekdays and weekends?

```r
    #aggregated <- aggregate(steps ~ interval + isWeekDay, data, mean)
    #qplot(interval, steps, data=aggregated, geom="line", color=isWeekDay);
```
