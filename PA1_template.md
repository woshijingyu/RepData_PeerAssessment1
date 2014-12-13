# Reproducible Research: Peer Assessment 1


## Loading and preprocessing the data


Show any code that is needed to
Load the data (i.e. read.csv())
Process/transform the data (if necessary) into a format suitable for your analysis


```r
options(scipen=99999)
data <- read.csv('activity.csv')
data$date <- as.POSIXct(data$date, format="%Y-%m-%d")
sumStepsByDate <- aggregate(steps~date, data, FUN=sum, na.rm=TRUE)
```

## What is mean total number of steps taken per day?

For this part of the assignment, you can ignore the missing values in the dataset.

Make a histogram of the total number of steps taken each day


![](PA1_template_files/figure-html/unnamed-chunk-2-1.png) 

Calculate and report the mean and median total number of steps taken per day

The mean of total number of steps taken per day is **10766.1886792** and The median of total number of steps taken per day is **10765**


## What is the average daily activity pattern?

Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)
![](PA1_template_files/figure-html/unnamed-chunk-3-1.png) 

Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?

Answer: **835**


## Imputing missing values

Note that there are a number of days/intervals where there are missing values (coded as NA). The presence of missing days may introduce bias into some calculations or summaries of the data.

Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)

Answer: **2304**

Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.
Create a new dataset that is equal to the original dataset but with the missing data filled in.

Using the average steps for 5-minute interval as the NA values and create the new dataset:

```r
avgStepsByInterval <- aggregate(steps ~ interval, data, FUN=mean, na.rm=TRUE)

naRow <- which(is.na(data$steps))
naInterval <- data$interval[naRow]

dataNew <- data
for(i in 1:length(naRow))
{
  rowVal <- naRow[i]
  interval <- data$interval[rowVal]
  avgSteps <- avgStepsByInterval[which(avgStepsByInterval$interval == interval),2]
  dataNew$steps[rowVal] <- avgSteps
}
```

Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?


```r
sumStepsByDate <- aggregate(steps~date, dataNew, FUN=sum, na.rm=TRUE)
qplot(date, steps, data=sumStepsByDate, geom="bar", stat="identity")
```

![](PA1_template_files/figure-html/unnamed-chunk-5-1.png) 

```r
meanStepsPerDay <- mean(sumStepsByDate$steps)
medianStepsPerDay <- median(sumStepsByDate$steps)
```
The mean of total number of steps taken per day is **10766.1886792** and The median of total number of steps taken per day is **10766.1886792**

Now the mean and median values are different from the previous version. The impact is that in the histogram there is no date with empty values and now the mean and median of the total steps are the same. 


## Are there differences in activity patterns between weekdays and weekends?

For this part the weekdays() function may be of some help here. Use the dataset with the filled-in missing values for this part.

Create a new factor variable in the dataset with two levels - "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.




```r
dataNew$weekday <- weekdays(dataNew$date)
dataNew$weekday <- gsub("Monday", "weekday", dataNew$weekday)
dataNew$weekday <- gsub("Tuesday", "weekday", dataNew$weekday)
dataNew$weekday <- gsub("Wednesday", "weekday", dataNew$weekday)
dataNew$weekday <- gsub("Thursday", "weekday", dataNew$weekday)
dataNew$weekday <- gsub("Friday", "weekday", dataNew$weekday)
dataNew$weekday <- gsub("Saturday", "weekend", dataNew$weekday)
dataNew$weekday <- gsub("Sunday", "weekend", dataNew$weekday)
dataNew$weekday <- as.factor(dataNew$weekday)
```

Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). See the README file in the GitHub repository to see an example of what this plot should look like using simulated data.


```r
sumStepsByWeekday <- aggregate(steps~date+interval+weekday, dataNew, FUN=sum, na.rm=TRUE)
avgStepsByWeekday <- aggregate(steps~weekday+interval, sumStepsByWeekday, FUN=mean, na.rm=TRUE)
qplot(interval, steps, data=avgStepsByWeekday, geom="line", stat="identity", color=weekday)
```

![](PA1_template_files/figure-html/unnamed-chunk-7-1.png) 
