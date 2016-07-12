---
output: html_document
---

## Loading and preprocessing the data
```{r setoptions,echo=FALSE}
library(knitr)
opts_chunk$set(echo = TRUE)
```
1.load the data 
```{r, results='asis'}
library(dplyr)
library(ggplot2)
setwd("C:/Users/liuzhi/Documents/R/work-directory")
act <- read.csv("activity.csv")
act <- tbl_df(act)
str(act)
```

2.Process/transform the data (if necessary) into a format suitable for your analysis
```{r}
act$date <- as.Date(act$date)
str(act)
```

##  What is mean total number of steps taken per day?
3.Mean and median number of steps taken each day
```{r}
act1 <- group_by(act,date)
act2 <- mutate(act1,d_steps = sum(steps))
sub1 <- act2[,c(2,4)]
sub2 <- unique(sub1)
mean_missed <- mean(sub2$d_steps,na.rm = TRUE)
median_missed <- median(sub2$d_steps,na.rm = TRUE)
print(mean_missed)
print(median_missed)
```
4.Time series plot of the average number of steps taken
```{r}
qplot(sub2$d_steps)
```  

## What is the average daily activity pattern? 
5.The 5-minute interval that, on average, contains the maximum number of steps
```{r}
act2_1 <- group_by(act,interval)
act2_2 <- mutate(act2_1,i_steps=mean(steps,na.rm = TRUE))
sub2_1 <- act2_2[,c(3,4)]
sub2_2 <- unique(sub2_1)
ggplot(data=sub2_2,aes(interval,i_steps))+geom_line()
filter(sub2_2,i_steps == max(sub2_2$i_steps))
```

## Imputing missing values

```{r}
na_num <- sum(is.na(act[,1]))
print(na_num)
```
```
miss value exist in the 1st colume,then can count the amount of total number of missing values in the dataset.it's `r na_num`.
```
6.filling in all of the missing values by mean for that 5-minute interval
```{r}
act3 = act
nrow <- length(act$steps)
nrow
for(i in 1:nrow){
  if (is.na(act[i,1]))
    act3[i,1] <- act2_2[i,4]
}
act3
```

7.Histogram of the total number of steps taken each day after missing values are imputed
```{r}
act3_1 <- group_by(act3,date)
act3_2 <- mutate(act3_1,d_steps = sum(steps))
act3_3 <- act3_2[,c(2,4)]
sub3 <- unique(act3_3)
mean_filled <- mean(sub3$d_steps,na.rm = TRUE)
median_filled <- median(sub3$d_steps,na.rm = TRUE)

qplot(sub3$d_steps)
mean_filled
median_filled
```
```
mean value become from `r mean_missed` to `r mean_filled`;
median value become from `r median_missed` to `r median_filled`.
the mean and median both increased slightly after fill miss values.
```


## Are there differences in activity patterns between weekdays and weekends?
8.Panel plot comparing the average number of steps taken per 5-minute interval across weekdays and weekends
```{r}
act4 <- act
act4$weekday <- weekdays(act3$date) 
for (j in 1:length(act[,1])) {
  if (act4[j,4]=="ÐÇÆÚÁù" || act4[j,4]=="ÐÇÆÚÈÕ")
    act4$day_end[j] = "weekend"
  else 
    act4$day_end[j] = "weekday"
}

act4_1 <- group_by(act4,interval,day_end)
act4_2 <- mutate(act4_1,i_steps=mean(steps,na.rm = TRUE))
act4_3 <- act4_2[,c(3,5,6)]
act4_4 <- unique(act4_3)
ggplot(data=act4_4,aes(x=interval,y=i_steps)) +geom_line() + facet_grid(day_end~.)
```
