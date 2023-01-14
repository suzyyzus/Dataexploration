# Data Exploration
In this project, I use R to wrangling and visualize Toronto COVID 19 data.

:briefcase::briefcase:
Code Highlight

```{r cases_dw, warning = FALSE, echo=TRUE}
#To replace all NAs in numeric columns, only work on columns
reported <- reported_raw %>%
  mutate_if(is.numeric, replace_na, replace = 0)
#make sure the reported date into date format
reported$reported_date <- date(reported$reported_date)
#very important, increasing # of rows, decreasing # of cols
new_reported <- reported %>% 
  pivot_longer(-c(reported_date),
               names_to = "status", values_to = "number")
#change the string to sentence case, which means the first letter is capitalized
new_reported$status <- str_to_sentence(new_reported$status)
#factor the variable "status"
new_reported$status <- factor(new_reported$status, 
                              levels = c("Active", "Recovered", "Deceased"))
```

\newpage
## Data visualization

```{r cases_vis, warning = FALSE, echo=TRUE}
new_reported %>%
  ggplot(aes(x = reported_date, y = number, fill = status)) +
  scale_x_date(breaks = "3 months", date_labels = "%d %b %y")+
#stat = "identity" means I tell ggplot to skip aggregation, state that I will provide y values
  geom_bar(stat = "identity") +
# theme_minimal() means no background annotations
  theme_minimal() +
#add title, subtitle..caption explanation by using lab() in ggplot
  labs(title = "Cases reported by day in Toronto, Canada",
       subtitle = "Confirmed and probable cases",
       x = "Date",
       y = "Case count",
       caption = str_c("Created by: <Zishu Zhu> for STA303/1002, U of T\n", 
"Source: Ontario Ministry of Health, Integrated Public Health Information System and CORES\n", 
format(Sys.time(), "Data as of %B %d, %Y"))) +
#no legend title and self-define the legend postion
  theme(legend.title=element_blank(), legend.position=c(0.15, 0.8)) +
#fill the bar chart by self-defined color, not automatically
  scale_fill_manual(values=c("#003F5C", "#86BCB6", "#B9CA5D"),
                    breaks=c("Active", "Recovered", "Deceased"))
```
