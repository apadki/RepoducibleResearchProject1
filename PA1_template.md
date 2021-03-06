# Reproducible Research Assignment 1
<br>


###About this Document 
This is a literate statistical programming output, it contains the documentations as well as code.
It is created using Rmd and convereted to html using knitr Package.

###What does this document contain?

**This assignmnet performs the following main tasks**    
     1.Loading and preprocessing data from a personal activity monitoring device  
     2.Imputing missing values   
     3.Representing differences in activity patterns between weekdays and weekends <br>
 <a href="https://class.coursera.org/repdata-016/human_grading/view/courses/973760/assessments/3/submissions/">Please Visit for full description of the assignment</a>
 
 
 
###1.Loading and Preprocessing of Data             
####1.1 The data is a csv file activity.csv which is downloaded from the given URL.


```r
df = read.csv("activity.csv")
summary(df)
```

```
##      steps                date          interval     
##  Min.   :  0.00   2012-10-01:  288   Min.   :   0.0  
##  1st Qu.:  0.00   2012-10-02:  288   1st Qu.: 588.8  
##  Median :  0.00   2012-10-03:  288   Median :1177.5  
##  Mean   : 37.38   2012-10-04:  288   Mean   :1177.5  
##  3rd Qu.: 12.00   2012-10-05:  288   3rd Qu.:1766.2  
##  Max.   :806.00   2012-10-06:  288   Max.   :2355.0  
##  NA's   :2304     (Other)   :15840
```

####1.2 Tranforming the data to answer: what is mean total number of steps taken per day?

```r
steps_per_day = aggregate(steps ~ date, df, sum, na.rm=T)
hist(steps_per_day$steps, main="Total Steps Every Day", col="green" , xlab="Number of Steps")
```

![](./PA1_template_files/figure-html/1.2-1.png) 

```r
stepsMean=round(mean(steps_per_day$steps))

stepsMedian=round(median(steps_per_day$steps))

print( c('Mean for the steps' , stepsMean))
```

```
## [1] "Mean for the steps" "10766"
```

```r
print( c('Median for the steps' , stepsMedian))
```

```
## [1] "Median for the steps" "10765"
```

####1.3 Tranforming the data to answer:What is the average daily activity pattern?

```r
avg_steps_interval= aggregate(steps~interval, df, mean)

plot(avg_steps_interval$interval, avg_steps_interval$steps, type="l", xlab='Interval', ylab="Average Steps",
     main="Average Steps For Each Interval", col="blue")
```

![](./PA1_template_files/figure-html/1.3-1.png) 

```r
interval_with_max_steps = avg_steps_interval[which.max(avg_steps_interval$steps),1]
print( c('Interval with maximum steps' , interval_with_max_steps))
```

```
## [1] "Interval with maximum steps" "835"
```

###2.Imputing missing values
####2.1 The total number of missing values in the dataset

```r
total_missing = sum(is.na(df))
print( c('Missing values' , total_missing))
```

```
## [1] "Missing values" "2304"
```
####2.2 New dataset that is equal to the original dataset but with the missing data filled in.
The na values fr steps are filled in with the avag steps for the corresponding interval calcualted before    

```r
df1 = df

n = nrow(df)

for (k in 1:n)
{if (is.na(df[k,"steps"])) {
    a = avg_steps_interval[which(avg_steps_interval$interval==df[k,"interval"]),];
    
    df1[k,"steps"]= round(a[1,"steps"])
}
}
summary(df1)
```

```
##      steps                date          interval     
##  Min.   :  0.00   2012-10-01:  288   Min.   :   0.0  
##  1st Qu.:  0.00   2012-10-02:  288   1st Qu.: 588.8  
##  Median :  0.00   2012-10-03:  288   Median :1177.5  
##  Mean   : 37.38   2012-10-04:  288   Mean   :1177.5  
##  3rd Qu.: 27.00   2012-10-05:  288   3rd Qu.:1766.2  
##  Max.   :806.00   2012-10-06:  288   Max.   :2355.0  
##                   (Other)   :15840
```
####2.3 Histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps

```r
steps_per_day1 = aggregate(steps ~ date, df1, sum)
hist(steps_per_day1$steps, main="Total Steps Every Day", col="green" , xlab="Number of Steps")
```

![](./PA1_template_files/figure-html/2.3-1.png) 

```r
stepsMean1=round(mean(steps_per_day1$steps))

stepsMedian1=round(median(steps_per_day1$steps))

print( c('Mean for the steps' , stepsMean1))
```

```
## [1] "Mean for the steps" "10766"
```

```r
print( c('Median for the steps' , stepsMedian1))
```

```
## [1] "Median for the steps" "10762"
```
##### Effect of imputting : The median is slightly differnt, but mean did not change much, this is because with assigning values to na, the number of rows in dataframe also increased.

###3.Representing differences in activity patterns between weekdays and weekends
####3.1 Creating a two leverl factor variable for imuted data

```r
weekdays <- c("Monday", "Tuesday", "Wednesday", "Thursday", 
              "Friday")
df1$dow = as.factor(ifelse(is.element(weekdays(as.Date(df1$date)),weekdays), "Weekday", "Weekend"))
```
####3.2 Make a plot to shwo the activity pattern difference

```r
week_day_intervals <- aggregate(steps ~ interval + dow, df1, mean)

library(lattice)
```

```
## Warning: package 'lattice' was built under R version 3.1.2
```

```r
xyplot(week_day_intervals$steps ~ week_day_intervals$interval|week_day_intervals$dow, main="Average Steps per Day by Interval",xlab="Interval", ylab="Steps",layout=c(1,2), type="l")
```

![](./PA1_template_files/figure-html/3.2-1.png) 
