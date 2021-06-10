---
title: Importing Strava Data
author: R package build
date: '2021-06-01'
slug: importing-strava-data
categories: []
tags: []
---
<script src="{{< blogdown/postref >}}index_files/clipboard/clipboard.min.js"></script>
<link href="{{< blogdown/postref >}}index_files/xaringanExtra-clipboard/xaringanExtra-clipboard.css" rel="stylesheet" />
<script src="{{< blogdown/postref >}}index_files/xaringanExtra-clipboard/xaringanExtra-clipboard.js"></script>
<script>window.xaringanExtraClipboard(null, {"button":"Copy Code","success":"Copied!","error":"Press Ctrl+C to Copy"})</script>





# Importing Strava data for use with RStudio

This blog post will guide you the somewhat complicated process of bringing in your own personal Strava data for use within the RStudio environment. To begin, you will need to have two things: an active Strava account and an RStudio .Rmd ready to use.

This post will cover two distinct two ways to download your data.

[The first method: complex and limited (but small!)](#the-first-method-complex-and-limited-but-small) is somewhat complex, and uses API calls to return a dataframe containing a fairly limited set of information: Distance, speed, duration, elevation, location, and time.

[The second method: quick and easy, but can be a very large download!] (just threaten to delete your account and they will give you an option to download all data they have collected!) and returns a much larger set of information...all of the preceding information, plus entire route maps for each activity, as well any photos you uploaded to Strava with those activities.

## The first method: complex and limited (but small!) {#the-first-method-complex-and-limited-but-small}

First, visit <https://www.strava.com/settings/api>.

If you are not logged in, it will prompt you to enter your login credentials. Once you are logged in, you will be taken to the Strava page to create an API Application. 

<center>

![initial API screen](img/shot1.png)

</center>

You will need to enter some basic information to complete this process.

First, come up with an **application name**. This can be anything you like, but keep it simple.

Next, choose an **application type**. For our example, we selected "data importer" because that is our primary purpose here.

The **club** field can be left empty.

Enter whatever **website** you like in the relevant field. Your github repo or Strava profile page would work fine, as no one besides you will likely be looking at this information.

The **application description** can be whatever you like.

For the **authorization callback domain**, enter *developers.strava.com.*

When you are done, you should have it filled out something like this:

<center>

![completed API creation form](img/shot2.png)

</center>

When you click on Create, it will ask you to update your app icon. You can add any image file you like, and then it will take you to a screen listing your information.

<center>

![click to reveal the secret!](img/shot4.png)

</center>

For the next step, you will need to show your **Client Secret**, which will display a long alphanumeric string that is unique to you. This allows RStudio to talk to Strava for the initial handshake, verifying your identity and allowing RStudio to pull your data.

In your .Rmd, add the following lines of code:

    
    ```r
    library(rStrava)
    library(plyr)
    
    app_name <- "ENTER_YOUR_APP_NAME_HERE "
    app_client_id <- "ENTER_YOUR_CLIENT_ID_HERE"
    app_secret <- "ENTER_YOUR_CLIENT_SECRET_HERE"
    
    #create the authentication token (only once)
    #stoken <- httr::config(token = strava_oauth(app_name, app_client_id, app_secret,
    #app_scope="activity:read_all", cache=TRUE))
    
    #retrieve local token
    stoken <- httr::config(token = readRDS('.httr-oauth')[[1]])
    filename_raw <- "./data_raw.Rda"
    filename_df <- "./data_df.Rda"
    if (file.exists(filename_df)) {
       cat("….. download last week")
       load("./data_df.Rda")       
    # create empty data frame with same amount of columns as existing data, 
    # otherwise column mismatch may occur                          
       df_empty <- df_activities[0,]
                                        
    # define last date minus 1 week for corrections 
       last_date <- as.Date(max(df_activities$start_date))-7             
    # get new activities and place in data frame   
       new_activities <- get_activity_list(stoken, after = last_date)
       df_new_activities <- compile_activities(new_activities, units="metric")
       df_new_activities <- rbind.fill(df_empty,df_new_activities)
    # replace existing records with updated ones, ignore the warnings 
    suppressWarnings(df_activities[df_activities$id %in% df_new_activities$id, ] <- df_new_activities)
    # combine dataframes
      df_activities <- rbind.fill(df_activities,df_new_activities)    
      df_activities <- unique(df_activities)    
    } 
     {
       cat("….. Downloading from 2004, this takes some time")
       last_date <- as.Date("2004-01-01")
       activities <- get_activity_list(stoken, after = last_date)
       df_activities <- compile_activities(activities, units="metric")
     }
    # store dataframe
    save(df_activities, file="data_df.Rda")
    ```

Some things to note: be sure to enter your own **Application Name**, **Client ID**, and **Client Secret** in the correct spots. After running that code chunk, you should connect to the Strava website for authorization.

Check both boxes, and then click Authorize to link Strava with your data.

<center>

![click to authorize](img/shot5.png)

</center>

After the Authorization is complete, you will have a file called *stoken* appear in your working environment. Once that file is in place, you can comment out the two lines of code directly under the spot where you entered your Client Secret. You will see a little note there that it only needs to happen once:

\#create the authentication token (only once)

Once you run that chunk, it will download your data into a dataframe called "**data_df.Rda**" which a simple click will add to RStudio for your continued exploration.

<br> <br>

## The second method: quick and easy, but can be a very large download!

This method is pretty simple. Simply visit <https://www.strava.com/athlete/delete_your_account> and scroll down a bit to the *Download Request* section.

<center>

![But I don't want to delete my account!](img/shot7.png)

</center>

Click on the Request Your Archive button, and they will prepare your archive and email you a download link to the email they have on file. Make sure your email address is up to date!

<center>

![Seven days to get this done!](img/shot8.png)

</center>

Soon, you will get a download link in your email. Click to download, and you will soon have a folder full of everything that Strava has collected on you as an athlete.

The most useful files for Rstudio will the .csv that houses all of your activity data, and the .gpx files that contain a fairly complete map every individual activity.

To add the .csv file, make sure that activities.csv file is housed in a **data** folder in your project directory, and then run the following code:


```r
library(readr)
activities <- read_csv("data/activities.csv",
    col_types = cols_only(`Activity ID` = col_number(),
                          `Activity Date` =col_character(),
                          `Activity Name` =col_character(),
                          `Activity Type` =col_character(),
                          `Elapsed Time` =col_number(),
                          `Distance` =col_number(),
                          `Elevation Gain` =col_number()))
View(activities)
```

Once you have done that, you should have a .csv file with all of your activities. If you would also like to work with the .gpx files for detailed maps of everything you've done, the following code will do the trick:


```r
gpxdata <- process_data("data/activities_gpx")
#gpxdata <- slice(gpxdata, -c(63744, 63745, 63746)) YOU MIGHT NEED TO USE SOMETHINNG LIKE THIS LINE IF YOU BRING IN IN ANY BAD DATA...I DID
```

You will have to wait a while, as this is quite a large file to bring in...but once it is in, you will have quite a few options for manipulating your data! As a first step, you will want to tidy your imported data. See David's informative blog post for more details on how to do that.
