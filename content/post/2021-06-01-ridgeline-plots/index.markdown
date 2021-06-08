---
title: Ridgeline Plots
author: R package build
date: '2021-06-04'
slug: ridgeline-plots
categories: []
tags: []
---


## Let's have fun with your Strava data!

This blog post is intended to provide an additional example of the ways personal data can be used. Our final product will be a ridgeline plot a la Joy Division's *Unknown Pleasures*. Slap it on a t-shirt, throw it on a mug, and fool your friends! Or most likely strangers! Because your friends probably already know your musical preferences!

Them: Oh, you're a Joy Division fan? 

You: **No!** I just love cycling and data!



## Alright friends

First things first, open up RStudio and start a brand new R Markdown file. We're going to use quite a few packages, most of which have been introduced in our other blog posts (link here). Here's what we'll need:


```r
knitr::opts_chunk$set(echo = TRUE)

library(tidyverse)
```

```
## ── Attaching packages ─────────────────────────────────────── tidyverse 1.3.0 ──
```

```
## ✓ ggplot2 3.3.3     ✓ purrr   0.3.4
## ✓ tibble  3.1.2     ✓ dplyr   1.0.6
## ✓ tidyr   1.1.2     ✓ stringr 1.4.0
## ✓ readr   1.4.0     ✓ forcats 0.5.0
```

```
## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
## x dplyr::filter() masks stats::filter()
## x dplyr::lag()    masks stats::lag()
```

```r
library(purrr)
library(repurrrsive)
library(here)
```

```
## here() starts at /Users/dfainstein/Desktop/R/finalproject_653
```

```r
library(janitor)
```

```
## 
## Attaching package: 'janitor'
```

```
## The following objects are masked from 'package:stats':
## 
##     chisq.test, fisher.test
```

```r
library(lubridate)
```

```
## 
## Attaching package: 'lubridate'
```

```
## The following objects are masked from 'package:base':
## 
##     date, intersect, setdiff, union
```

```r
library(rStrava)
library(plyr)
```

```
## ------------------------------------------------------------------------------
```

```
## You have loaded plyr after dplyr - this is likely to cause problems.
## If you need functions from both plyr and dplyr, please load plyr first, then dplyr:
## library(plyr); library(dplyr)
```

```
## ------------------------------------------------------------------------------
```

```
## 
## Attaching package: 'plyr'
```

```
## The following object is masked from 'package:here':
## 
##     here
```

```
## The following objects are masked from 'package:dplyr':
## 
##     arrange, count, desc, failwith, id, mutate, rename, summarise,
##     summarize
```

```
## The following object is masked from 'package:purrr':
## 
##     compact
```

We're going to do a bit of jumping ahead, by assuming you have already loaded in your Strava data and created a tidy dataframe with it. We'll further modify and tidy, but if you aren't quite to this point see this previous post which will walk you through loading and cleaning: (link here).

This is how your data frame should look assuming you followed the directions in the previous post:


```r
# rmarkdown::paged_table(tidyfunc)
```

## Step 1

We'll start by beginning to pare down the data frame. We want to select the activities we do the most, so we don't end up with strange incomplete lines. We can use the function created in [earlier post] to do this:



## Step 2A

In order to create lines that mimic the "ridge" effect, we'll also need to split our data frames, by activity type and dates. The peaks and valleys of our ridgeline plot will reflect the high and low elevation points reached during an individual activity, while the length of the line will be created by collapsing activity across date spans. Keep in mind this will require a moderate to large dataset with a lot of points to manipulate. If that's not the case, we can simulate the data, which kind of makes you a liar. But I'll show you how to do it anyway. *[decide if this step is something you want to include and if this method works for creating the plot - ask DA]*

*If you have a large enough data set [how many points should I be aiming for? Idk!] skip ahead to step 2B.*


```r
# here I'll need a small data set
# Maybe one option is to way pare down current data set to use as an example
# If you do that, don't include data chunk

# Then will walk through creation of a function to simulate additional data 
```

## Step 2B

In our example data frame, we'll split into the following activity types: 


```r
# need function for picking out activities
# ex <-
# tidyfunc %>% 
#   filter(type == "Ride") %>% 
#   select(distance, elapsed_time, elev_high, elev_low, date)
```

Next, we'll split up our date column into 3 separate columns: year, month, and day. This will allow us to 

## Step 3
