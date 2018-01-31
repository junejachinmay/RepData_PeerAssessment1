\# Course Project 1
===================

### Read the CSV File

    data<-read.csv("activity.csv")

### What is mean total number of steps taken per day?

1.  Calculate the total number of steps taken per day

<!-- -->

    gooddata <- data[complete.cases(data),]
    stepsperday <- tapply(gooddata$steps, gooddata$date, sum, na.rm=TRUE)

1.  If you do not understand the difference between a histogram and a
    barplot, research the difference between them. Make a histogram of
    the total number of steps taken each day

<!-- -->

    hist<- hist(stepsperday, xlab = "Steps Per Day", main = "Number of Steps Per Day")

![](PA1_template_files/figure-markdown_strict/Question1%20Part%202-1.png)

1.  Calculate and report the mean and median of the total number of
    steps taken per day

<!-- -->

    mean(stepsperday, na.rm= TRUE)

    ## [1] 10766.19

    median(stepsperday, na.rm= TRUE)

    ## [1] 10765

### What is the average daily activity pattern?

1.  Make a time series plot (i.e. 𝚝𝚢𝚙𝚎 = "𝚕") of the 5-minute interval
    (x-axis) and the average number of steps taken, averaged across all
    days (y-axis)

<!-- -->

    gooddata <- data[complete.cases(data),]
    avgsteps <- tapply(gooddata$steps, gooddata$interval, mean, na.rm=TRUE)


    plot(names(avgsteps),avgsteps,xlab="5-minute interval", ylab="Average Number of Steps", main="Average number of steps by Interval", type="l")

![](PA1_template_files/figure-markdown_strict/Question2%20Part%201-1.png)

1.  Which 5-minute interval, on average across all the days in the
    dataset, contains the maximum number of steps?

<!-- -->

    maxsteps<-which.max(avgsteps)
    names(maxsteps)

    ## [1] "835"

### Imputing missing values

Note that there are a number of days/intervals where there are missing
values (coded as 𝙽𝙰). The presence of missing days may introduce bias
into some calculations or summaries of the data.

1.  Calculate and report the total number of missing values in the
    dataset (i.e. the total number of rows with 𝙽𝙰s)

<!-- -->

    missingvalues <- sum(!(complete.cases(data)))
    missingvalues

    ## [1] 2304

1.  Devise a strategy for filling in all of the missing values in the
    dataset. The strategy does not need to be sophisticated. For
    example, you could use the mean/median for that day, or the mean for
    that 5-minute interval, etc.

2.  Create a new dataset that is equal to the original dataset but with
    the missing data filled in.

<!-- -->

    ##Part 2 and 3 combined
    newdata <- data
    for (i in 1:nrow(newdata)){
        if (is.na(newdata$steps[i])){
            newdata$steps[i] <- mean(newdata$steps[newdata$interval == newdata$interval[i]], na.rm=TRUE)
        }
    }

1.  Make a histogram of the total number of steps taken each day and
    Calculate and report the mean and median total number of steps taken
    per day. Do these values differ from the estimates from the first
    part of the assignment? What is the impact of imputing missing data
    on the estimates of the total daily number of steps?

<!-- -->

    newstepsperday <- tapply(newdata$steps, newdata$date, sum)
    hist(newstepsperday,xlab = "Steps Per Day", main = "Average number of Steps per Day including Missing Values")

![](PA1_template_files/figure-markdown_strict/Question%203%20Part%204-1.png)

### Are there differences in activity patterns between weekdays and weekends?

For this part the 𝚠𝚎𝚎𝚔𝚍𝚊𝚢𝚜() function may be of some help here. Use the
dataset with the filled-in missing values for this part.

1.  Create a new factor variable in the dataset with two levels –
    “weekday” and “weekend” indicating whether a given date is a weekday
    or weekend day.

<!-- -->

    w <- weekdays(strptime(newdata$date, format = "%Y-%m-%d"))
    for (i in 1:length(w)){
        if (w[i] == "Saturday" | w[i] == "Sunday"){
            w[i] <- "weekend"
        } 
        else {
            w[i] <- "weekday"
        }
    }
    w <- as.factor(w)
    newdata$w <- w

1.  Make a panel plot containing a time series plot (i.e. 𝚝𝚢𝚙𝚎 = "𝚕") of
    the 5-minute interval (x-axis) and the average number of steps
    taken, averaged across all weekday days or weekend days (y-axis).
    See the README file in the GitHub repository to see an example of
    what this plot should look like using simulated data.

<!-- -->

    finaldata <- aggregate(steps ~ interval + w, data = newdata, mean)
    library(lattice)

    xyplot(steps ~ interval | w, data = finaldata, type = "l", layout = c(1, 2), xlab = "Interval", ylab = "Number of Steps")

![](PA1_template_files/figure-markdown_strict/Question%204%20Part%202-1.png)
