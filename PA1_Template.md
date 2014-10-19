Assigment 1

This is a R markdown in my computer for assigment 1

Loading and preprocessing the data

Data_Raw <- read.csv("activity.csv", header=TRUE)
Data_Clean <- Data_Raw[which(Data_Raw$steps != "NA"), ]
library(plyr)
Data_day <- ddply(Data_Clean, .(date), summarise, steps=sum(steps))
What is mean total number of steps taken per day?

a. Histogram of Steps per day

hist(Data_day$steps, main="Number of Steps", 
     xlab="Total number of steps taken each day", col="blue")


b. Calculation of the mean and median total number of steps taken per day

mean(Data_day$steps)
## [1] 10766.19
median(Data_day$steps)
## [1] 10765
What is the average daily activity pattern?

a. A time series plot (i.e. type = “l”) of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)

Average_interval <- ddply(Data_Clean, .(interval), summarise, steps=mean(steps))
plot(Average_interval$interval, Average_interval$steps, type="l", 
     col="Black",
     xlab="5-minute Interval", 
     ylab="Average Number of Steps Taken",
     main="Pattern of Average Activity per Day")


b. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?

Average_interval[Average_interval$steps==max(Average_interval$steps),]
##     interval    steps
## 104      835 206.1698
Imputing missing values

a. Reporting total number of missing values in the dataset

sum(is.na(Data_Raw$steps))
## [1] 2304
b. Filling all of the missing values in the dataset. We fill the values with the avarage of the values presented.

colnames(Average_interval)[2] <- "Avg_interval"

Data_missfill <- arrange(join(Data_Raw, Average_interval), interval)
## Joining by: interval
c. New dataset that is equal to the original dataset but with the missing data filled in.

Data_missfill$steps[is.na(Data_missfill$steps)] <- Data_missfill$Avg_interval[is.na(Data_missfill$steps)]
d.1. A histogram of the total number of steps taken each day

New_data_hist <- ddply(Data_missfill, .(date), summarise, steps=sum(steps))
hist(New_data_hist$steps, main="Number of Steps (missing values fill in)", 
     xlab="Total number of steps per day", col="blue",)


d.2. Report the mean and median total number of steps taken per day

mean(New_data_hist$steps)
## [1] 10766.19
median(New_data_hist$steps)
## [1] 10766.19
d.3. The impact of imputing missing data on the estimates of the total daily number of steps? (Using sum as operation of difference)

sum_steps_data <- sum(Data_Clean$steps)
sum_steps_data_missfillin  <- sum(Data_missfill$steps)
total_diff <- sum_steps_data_missfillin -sum_steps_data []
total_diff
## [1] 86129.51
Are there differences in activity patterns between weekdays and weekends?

a. New factor variable in the dataset with two levels - “weekday” and “weekend” indicating whether a given date is a weekday or weekend day.

week_day <- weekdays(as.Date(Data_missfill$date))
## Warning in strptime(xx, f <- "%Y-%m-%d", tz = "GMT"): unable to identify current timezone 'H':
## please set environment variable 'TZ'
## Warning in strptime(xx, f <- "%Y-%m-%d", tz = "GMT"): unknown timezone
## 'localtime'
data_weekday <- transform(Data_missfill, day=weekdays)
## Error in data.frame(structure(list(steps = c(1.71698113207547, 0, 0, 47, : arguments imply differing number of rows: 17568, 0
data__weekday$week <- ifelse(data_weekday$day %in% c("Saturday", "Sunday"),"weekend", "weekday")
## Error in match(x, table, nomatch = 0L): objeto 'data_weekday' no encontrado
Average_interval_week <- ddply(data_weekday, .(interval, week), summarise, steps=mean(steps))
## Error in empty(.data): objeto 'data_weekday' no encontrado
b. panel plot containing a time series plot (i.e. type = “l”) of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). The plot should look something like the following, which was creating using simulated data:

library(lattice)
xyplot(steps ~ interval | wk, data = Average_interval_week, layout = c(1, 2), type="l")
## Error in eval(substitute(groups), data, environment(x)): objeto 'Average_interval_week' no encontrado
