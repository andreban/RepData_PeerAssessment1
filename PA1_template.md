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

Lets take the mean of each 5 minute interval and replace the missing data with the corresponding mean.

Firsts, aggregate the data so as to take the mean for each interval.

```r
meanStepsByTimeOfday <- aggregate(steps ~ time, data, mean)
```

Now, merge the aggregated data with the original data, by time 

```r
merged <- merge(data, meanStepsByTimeOfday, by="time")
```

Fill the NA data by copying the values from the merged field

```r
merged$steps.x[is.na(merged$steps.x)] = merged$steps.y[is.na(merged$steps.x)]
```

Now, create a new data frame with the correct data and the same column names

```r
newdata <- data.frame(merged$time, merged$steps.x, merged$date, merged$interval)
colnames(newdata) <- c("time", "steps", "date", "interval")
```

Colculate the steps per day using the new data and plot the histogram

```r
stepsByDay <- aggregate(steps ~ date, newdata, sum)
hist(stepsByDay$steps, breaks=20,main="Steps Per Day By Frequency",xlab="Daily Steps")
```

![plot of chunk unnamed-chunk-16](./PA1_template_files/figure-html/unnamed-chunk-16.png) 

Now, take the mean and the median

```r
mean(stepsByDay$steps, na.rm=T)
```

```
## [1] 10766
```

```r
median(stepsByDay$steps, na.rm=T)
```

```
## [1] 10766
```

We can see that the mean is still the same as before and the median is very close, but the frequency of days on the mean is much larger. While, on the original data, it is close to 10, on the modified data it is larger than 15.

## Are there differences in activity patterns between weekdays and weekends?

```r
    par(mfrow = c(2, 1))
    par(mar=c(4,5,4,5))
    aggregated <- aggregate(steps ~ time + isWeekday, data, mean)
    weekDays <- aggregated[aggregated$isWeekday == T,]
    weekendDays <- aggregated[aggregated$isWeekday == F,]
    plot(weekDays$time, weekDays$steps, type="l", xlab="", ylab="avg steps", main="Week Days");
    plot(weekendDays$time, weekendDays$steps, type="l", xlab="Hour of Day", ylab="avg steps", main="Weekends");
```

![plot of chunk unnamed-chunk-18](./PA1_template_files/figure-html/unnamed-chunk-18.png) 
