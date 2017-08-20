# RepData_PeerAssessment1
Peer Assessment 1 for Reproducible Research

There are chinese characters in names of weekdays.

8.Panel plot comparing the average number of steps taken per 5-minute interval across weekdays and weekends
```{r}
act4 <- act
act4$weekday <- weekdays(act3$date) 
print(unique(act4$weekday))
for (j in 1:length(act[,1])) {
  if (act4[j,4]=="星期六" || act4[j,4]=="星期日")
    act4$day_end[j] = "weekend"
  else 
    act4$day_end[j] = "weekday"
}
```
I saved codes by encoding ruls "UTF-8".
