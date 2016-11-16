# Reproducible Research: Peer Assessment 1
Descamps Mathieu  
15 novembre 2016  




## Loading and preprocessing the data


```r
activity = read.csv("activity.csv")
```

## What is mean total number of steps taken per day?

Total of steps taken per day:

```r
stepPerDay = aggregate(steps~date,activity,sum)
```

Histogram of the total number of steps taken each day :

```r
hist(stepPerDay$steps,nclass=10)
```

![](PA1_template_files/figure-html/unnamed-chunk-3-1.png)<!-- -->

Mean and median of the total number of steps taken per day :

```r
mean(stepPerDay$steps)
```

```
## [1] 10766.19
```

```r
median(stepPerDay$steps)
```

```
## [1] 10765
```

## What is the average daily activity pattern?

Time series plot :

```r
stepPerInterval = aggregate(steps~interval,activity,mean)
plot(stepPerInterval, type="l")
```

![](PA1_template_files/figure-html/unnamed-chunk-5-1.png)<!-- -->

The 5-minute interval, on average accros all days in the dataset, that contains the maximum number of steps :

```r
stepPerInterval$interval[which(stepPerInterval$steps == max(stepPerInterval$steps))]
```

```
## [1] 835
```

## Imputing missing values

Total number of missing values :

```r
sum(is.na(activity$steps))
```

```
## [1] 2304
```

My strategy to fill in all the missing values is just to replace the missing values by the global mean accross all interval and all days.

```r
activityNew = activity
globalMean = mean(stepPerInterval$steps)
for (x in 1:length(activity$steps)) {
	if(is.na(activity$steps[x])){
		activityNew$steps[x]=globalMean } }
```

Histogram, mean and median of the new total number of steps :

```r
stepPerDayNew = aggregate(steps~date,activityNew,sum)
hist(stepPerDayNew$steps,nclass=10)
```

![](PA1_template_files/figure-html/unnamed-chunk-9-1.png)<!-- -->

```r
mean(stepPerDayNew$steps)
```

```
## [1] 10766.19
```

```r
median(stepPerDayNew$steps)
```

```
## [1] 10766.19
```

We can see that the mean doesn't differ compare to the one computed in the beginning. Seems legit as we replace the NA by the global mean, mean will remain the same. However, the median differs a bit.


## Are there differences in activity patterns between weekdays and weekends?

R code in order to create the new factor variable, which one will be "var":

```r
activityNew$day = weekdays(as.Date(activityNew$date))
activityNew$var = "weekday"
for (x in 1:length(activityNew$day)){
	if(activityNew$day[x] %in% c("samedi","dimanche")){
		activityNew$var[x] = "weekend"
	}
}

activityNew$var=factor(activityNew$var,levels=c("weekday","weekend"))
```

Panel plot :

```r
stepPerIntVar=aggregate(steps~interval+var,activityNew,mean)
library(lattice)
xyplot(steps~interval|var,data=stepPerIntVar,aspect=1/2,type="l")
```

![](PA1_template_files/figure-html/unnamed-chunk-11-1.png)<!-- -->
