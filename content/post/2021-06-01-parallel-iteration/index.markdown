---
title: Parallel Iteration
author: R package build
date: '2021-06-03'
slug: parallel-iteration
categories: []
tags: []
---








Earlier in our blog series, you can read a post on how we used API calls to bring in data, and then another post on how to tidy and wrangle Strava data specifically. In this post, we'll jump ahead a bit and use a few parallel iteration tactics to gather some fundamental data visualizations that you can customize.



And so, let's get started on some of the tricky stuff! In order to display weekly trends, we will need to convert the standard date format into days of the week. A function is used to accomplish that. This conversion will come in handy when nesting data later on. 


```r
daysoftheweek <- function(x) format(as.Date(x), "%A")
```

Before creating a visualization for each activity, let's start with one to test it out. The activity "Hike" will be used as the single example. 


```r
#one plot displaying a count of hikes by days of the week
oneplot <- tidy_data %>%
  mutate(weekday = daysoftheweek(date)) %>% 
  filter(act_type == "Hike") %>%
  ggplot(aes(weekday)) +
  geom_bar(stat = "count") +
  guides(fill = "none") +
      labs(title = "Amount of Hikes over the week",
           subtitle = "Data displays trends found within the week",
           x = "",
           y = "Number of Hikes")
oneplot
```

<img src="{{< blogdown/postref >}}index_files/figure-html/one plot-1.png" width="672" />

Now we will create a parallel iteration of the data by grouping by activity and nesting. This will create a data frame with individual frames nested within it categorized by activity. By doing this, we are able to produce separate plots displaying weekly data for each activity type.


```r
#multiple plots based on activity 
final_plots <-
  weeklydata <- tidy_data %>%
  mutate(weekday = daysoftheweek(date)) %>% 
  group_by(act_type) %>% 
  nest() %>% 
  mutate(plots = map2(data, act_type, ~{
    ggplot(.x, aes(weekday)) +
      geom_bar(stat = "count") +
      labs(title = glue("Amount of {.y} activity over the week"),
           subtitle = "Data displays trends found within the week",
          x = "",
          y = glue("Number of {.y}"))
  }))

#display a plot. To change which activity you are able to view, change the number within the brackets. 
weeklydata$plots[[2]]
```

<img src="{{< blogdown/postref >}}index_files/figure-html/multiple plots-1.png" width="672" />

```r
weeklydata$plots[[4]]
```

<img src="{{< blogdown/postref >}}index_files/figure-html/multiple plots-2.png" width="672" />

The final step is to save all of the plots. We will do this in three steps:
1. create a directory within your machine's files
2. create file paths for the plots to follow
3. use the walk2 function to save .jpeg files of your plots


```r
# 1. create a directory
fs::dir_create(here::here("plots", "act_type"))

# 2. create file paths
activity <- str_replace_all(tolower(final_plots$act_type), " ", "-")
paths <- here::here("plots", "act_type", glue("{activity}.png"))


#save final plots
walk2(paths, final_plots$plots, ggsave,
      width = 9.5, 
      height = 6.5,
      dpi = 500)
```
