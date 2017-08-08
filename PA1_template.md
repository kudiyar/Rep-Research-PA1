---
output: html_document
---

Peer Assessment 1
======================================

Kudiyar Orazymbetov

Tuesday, August 8, 2017

### Introduction ###
It is now possible to collect a large amount of data about personal movement using activity monitoring devices such as a [Fitbit] [1], [Nike Fuelband] [2], or [Jawbone Up] [3]. These type of devices are part of the “quantified self” movement – a group of enthusiasts who take measurements about themselves regularly to improve their health, to find patterns in their behavior, or because they are tech geeks. But these data remain under-utilized both because the raw data are hard to obtain and there is a lack of statistical methods and software for processing and interpreting the data.

This assignment makes use of data from a personal activity monitoring device. This device collects data at 5 minute intervals through out the day. The data consists of two months of data from an anonymous individual collected during the months of October and November, 2012 and include the number of steps taken in 5 minute intervals each day.

[1]: https://www.fitbit.com/home "Fitbit"
[2]: http://www.nike.com/us/en_us/c/nikeplus-fuelband "Nike Fuelband"
[3]: https://jawbone.com/up "Jawbone Up"

### Data ###
The data for this assignment can be downloaded from the course web site:

- Dataset: [Activity monitoring data] [4] [52K]

[4]: https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2Factivity.zip "Activity monitoring data" 

The variables included in this dataset are:

- **steps**: Number of steps taking in a 5-minute interval (missing values are coded as NA)
- **date**: The date on which the measurement was taken in YYYY-MM-DD format
- **interval**: Identifier for the 5-minute interval in which measurement was taken

The dataset is stored in a comma-separated-value (CSV) file and there are a total of 17,568 observations in this dataset.

### Assignment ###
This assignment will be described in multiple parts. You will need to write a report that answers the questions detailed below. Ultimately, you will need to complete the entire assignment in a **single R markdown** document that can be processed by **knitr** and be transformed into an HTML file.

Throughout your report make sure you always include the code that you used to generate the output you present. When writing code chunks in the R markdown document, always use echo = TRUE so that someone else will be able to read the code. **This assignment will be evaluated via peer assessment so it is essential that your peer evaluators be able to review the code for your analysis.**

For the plotting aspects of this assignment, feel free to use any plotting system in R (i.e., base, lattice, ggplot2)

Fork/clone the [GitHub repository created for this assignment] [5]. You will submit this assignment by pushing your completed files into your forked repository on GitHub. The assignment submission will consist of the URL to your GitHub repository and the SHA-1 commit ID for your repository state.

[5]: http://github.com/rdpeng/RepData_PeerAssessment1 "GitHub repository created for this assignment"

NOTE: The GitHub repository also contains the dataset for the assignment so you do not have to download the data separately.

### Loading and preprocessing the data ###
Show any code that is needed to

1. Load the data (i.e. read.csv())

```{r}
# Clear the workspace
rm(list = ls())

# Load the raw activity data
raw_data <- read.csv("/home/assel/Desktop/Reproducible Research/activity.csv")
```

2. Process/transform the data (if necessary) into a format suitable for your analysis

```{r}
# Transform the date attribute to an actual date format
raw_data$date <- as.POSIXct(raw_data$date, format = "%Y-%m-%d")

# Compute the weekdays from the date attribute
raw_data <- data.frame(date = raw_data$date,
                           weekday = tolower(weekdays(raw_data$date)), 
                           steps = raw_data$steps, 
                           interval = raw_data$interval)

# Compute the day type (weekend or weekday)
raw_data <- cbind(raw_data, 
                      daytype = ifelse(raw_data$weekday == "saturday" | 
                                        raw_data$weekday == "sunday", "weekend", "weekday"))

# Create the final data.frame
data <- data.frame(date = raw_data$date, 
                       weekday = raw_data$weekday, 
                       daytype = raw_data$daytype,
                       interval = raw_data$interval, 
                       steps = raw_data$steps)

# Clear the workspace
rm(raw_data)
```

We display the first few rows of the activity data frame:

```{r}
head(data)
```

### What is mean total number of steps taken per day? ###
For this part of the assignment, you can ignore the missing values in the dataset.

1. Calculate the total number of steps taken per day (NA values removed)

```{r}
sum_data <- aggregate(data$steps, by = list(data$date), FUN = sum, na.rm = TRUE)

# Rename the attributes
names(sum_data) <- c("date", "total")
```

We display the first few rows of the sum_data data frame:

```{r}
head(sum_data)
```

2. Make a histogram of the total number of steps taken each day

```{r}
hist(sum_data$total, 
     breaks = seq(from = 0, to = 25000, by = 2500), 
     col = "blue", 
     xlab = "Total number of steps", 
     ylim = c(0,20), 
     main = "Histogram of the total number of steps taken each day\n(NA removed)")
```

![plot of chunk 1](https://github.com/kudiyar/Rep-Research-PA1/blob/master/1.png)

3. Calculate and report the **mean** and **median** of the total number of steps taken per day

```{r}
total_steps_each_day_mean <- mean(sum_data$total)
total_steps_each_day_median <- median(sum_data$total)
```

*mean*

```{r, echo = FALSE}
total_steps_each_day_mean
```

*median*

```{r, echo = FALSE}
total_steps_each_day_median
```

### What is the average daily activity pattern? ###

1. Make a time series plot (i.e. 𝚝𝚢𝚙𝚎 = "𝚕") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)

```{r}
# Clear the workspace
rm(sum_data)

# Compute the means of steps across all days for each interval
mean_data <- aggregate(data$steps, 
                       by = list(data$interval), 
                       FUN = mean, 
                       na.rm = TRUE)

# Rename the attributes 
names(mean_data) <- c("interval", "mean")
```

We display the first few rows of the mean_data data frame:

```{r}
head(mean_data)
```

The time serie plot is created by the following lines of code

```{r}
# Compute the time series plot
plot(mean_data$interval, 
     mean_data$mean, 
     type = "l", 
     col = "black",
     lwd = 2, 
     xlab = "Interval [minutes]", 
     ylab = "Average number of steps", 
     main = "Time-series of the average number of steps per intervals\n(NA removed)")
```

2. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?

```{r}
# We find the position of the maximum mean
max_pos <- which(mean_data$mean == max(mean_data$mean))

# We lookup the value of interval at this position
max_interval <- mean_data[max_pos, 1]

# Clear the workspace
rm(max_pos, mean_data)
```

The 5-minute interval that contains the maximum of steps, on average across all days, is 

```{r}
max_interval
```

### Imputing missing values ###

Note that there are a number of days/intervals where there are missing values (coded as 𝙽𝙰). The presence of missing days may introduce bias into some calculations or summaries of the data.

1. Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with 𝙽𝙰s)

```{r}
# Clear the workspace
rm(max_interval)

# We use the trick that a TRUE boolean value is equivalent to 1 and a FALSE to 0.
NA_count <- sum(is.na(data$steps))

# The number of NAs
NA_count
```

2. Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.

```{r}
# Clear the workspace
rm(NA_count)

# Find the NA positions
na_pos <- which(is.na(data$steps))

# Create a vector of means
mean_vec <- rep(mean(data$steps, na.rm=TRUE), times=length(na_pos))
```

We use the strategy to replace each NA value by the mean of the **steps** attribute.

3. Create a new dataset that is equal to the original dataset but with the missing data filled in.

```{r}
# Replace the NAs by the means
data[na_pos, "steps"] <- mean_vec

# Clear the workspace
rm(mean_vec, na_pos)
```

We display the first few rows of the new activity data frame:

```{r}
head(data)
```

4. Make a histogram of the total number of steps taken each day and Calculate and report the **mean** and **median** total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?

```{r}
# Compute the total number of steps each day (NA values removed)
sum_data <- aggregate(data$steps, by=list(data$date), FUN=sum)

# Rename the attributes
names(sum_data) <- c("date", "total")

# Compute the histogram of the total number of steps each day
hist(sum_data$total, 
     breaks=seq(from=0, to=25000, by=2500),
     col="green", 
     xlab="Total number of steps", 
     ylim=c(0, 30), 
     main="Histogram of the total number of steps taken each day\n(NA replaced by mean value)")
```

The mean and median are computed like

```{r}
total_steps_each_day_mean <- mean(sum_data$total)
total_steps_each_day_median <- median(sum_data$total)
```

*mean*

```{r, echo = FALSE}
total_steps_each_day_mean
```

*median*

```{r, echo = FALSE}
total_steps_each_day_median
```

These values differ greatly from the estimates from the first part of the assignment. The impact of imputing the missing values is to have more data, hence to obtain a bigger mean and median value.

### Are there differences in activity patterns between weekdays and weekends? ###
For this part the weekdays() function may be of some help here. Use the dataset with the filled-in missing values for this part.

1. Create a new factor variable in the dataset with two levels – “weekday” and “weekend” indicating whether a given date is a weekday or weekend day.

```{r}
# The new factor variable "daytype" was already in the data frame
head(data)
```

2. Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis).

```{r}
# Clear the workspace
rm(sum_data)

# Load the lattice graphical library
library(lattice)

# Compute the average number of steps taken, averaged across all daytype variable
mean_data <- aggregate(data$steps, 
                       by=list(data$daytype, 
                               data$weekday, data$interval), mean)

# Rename the attributes
names(mean_data) <- c("daytype", "weekday", "interval", "mean")
```

We display the first few rows of the mean_data data frame:

```{r}
head(mean_data)
```

The time series plot take the following form:

```{r}
xyplot(mean ~ interval | daytype, mean_data, 
       type = "l", 
       lwd = 1, 
       xlab = "Interval", 
       ylab = "Number of steps", 
       layout = c(1, 2))
```

```{r}
# Clear the workspace
rm(mean_data)
```
