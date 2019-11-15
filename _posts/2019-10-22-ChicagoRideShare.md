---
layout: archive
title: "Chicago Ride-share Data Analysis"
modified: 2019-10-22
comments: true
share: true
---


I just have written a recent R code to analyze "rideshare trips" data from the Chicago data portal. Data contains individuals’ trips from origin coordinates to destination coordinates along with travel time and cost of the trip and some other information. The goal of using this data is for travel demand modeling. In travel demand modeling, we need to know zone-to-zone interaction. I am going to count how many trips has been made within the zones. However, there are some issues with the data set:

(1) Almost for each trip, census trac information is given. But in order to have a complete data set, missing census tracts are needed to be found. So we need to download the Chicago census tracts polygon data to find missing tracts for trips. Luckily, this Census tract data is also available in the Chicago data portal. As a result, we need to do a spatial match between Census tracts and missing data points in the trips data set.

(2) Downloaded data is about 19GB which has around 73M rows. My computer's memory is 16GB. If I want to analyze this data either I have to rent an AWS or Google Cloud resources or split the data to the chunks and analyze individually. I could also use big data frameworks for this analysis, but it is not that big to rent Hadoop resources as well. I have to use my computer. I developed the following R code to handle this big data and analysis on the fly.

Source for the trip data: [link](https://data.cityofchicago.org/Transportation/Transportation-Network-Providers-Trips/m6dm-c72p){:target="_blank"}

Source for the census tracts data: [link](https://data.cityofchicago.org/Facilities-Geographic-Boundaries/Boundaries-Census-Tracts-2010/5jrd-6zik){:target="_blank"}

I had experience with all of the data handling libraries. For big data sets, I can suggest "data.table" library. For my spatial analysis, I used to analyze with PostgreSQL and PostGIS but there is a recent great library by Edzer Pebesma et. al (2019) for R which is "sf". It is fast and easy to use. I don't have to write spatial SQL code anymore.

Here is the code:
This code also can be found in my github page: [link](https://github.com/olcaysah/Chicago-Ride-share-Trips-Analysis){:target="_blank"}

```
#' To see how long all process will take time...
startTime_FullProcess <- Sys.time()

library(data.table)
library(sf)
library(stringr)
library(dplyr)
library(readr)
library(fasttime)
library(mapview)

options(scipen=999)
#Read for the column names for the rest of readings.
TNP_ColNames <- fread('Transportation_Network_Providers_-_Trips (1).csv', nrows = 0)
#Column names has gaps. These gaps sometimes make problems. Thats why I added "_" if there is a gap.
setnames(TNP_ColNames, str_replace_all(names(TNP_ColNames), pattern = " ", replacement = "_"))

#' I also wanted write the results to the disc to not repeat the same process again.
#' For this purpose I can append to whenever the chunk of the data is analyzed.
#' But first I need to prepare a base empty file for appending
#' I am also adding 2 additional columns for newly found census tracts
TNP_ColNames_Base <- TNP_ColNames %>% mutate(pickup_census_tract=NA,dropoff_census_tract=NA)
fwrite(TNP_ColNames_Base,'TNP_Pickup_Dropoff.csv',append = F)

#' Read the census tracts shape file:
censusTracs <- read_sf("./geo_export_a33b8e6a-8cde-49fa-bca2-5446460ee02b.shp")
#' Fix the datum here. Transformation is also needed for the spatial analysis.
censusTracs <- st_transform(censusTracs, 4326)

#' Lets see the census tracts in our map.
mapview(censusTracs)

#' Let's start the fun part.
#' First set the number of chunks for each read.
nChunks = 1e6
#' I know we have around 73M of rows.
#' Let's set the chunks to read from data.
chunks <- seq(0,73e6,nChunks)
#' There are many ways to write this for loop. But I like to lapply which is faster.
#' I use bind_rows fcuntion from dplyr. This should be ok for this work.

mergedData <- bind_rows(lapply(chunks, function(chunk){
  #' I just want to see how many minutes or seconds to take to process and analyze the chunk of the data.
  startTime <- Sys.time()
  #' Read given amount of rows:
  #' I use fread function from data.table library. This is very fast.
  #' "chunk" variable passed through lapply function
  TNP_Part <- fread('Transportation_Network_Providers_-_Trips (1).csv', skip = (chunk+1),nrows = nChunks, header = F)

  #' I disabled the reading header names because we are not reading the always from top of the file.
  #' That's why we need to add header names.
  #' setnames is fucntion from data.table
  setnames(TNP_Part, names(TNP_ColNames))
  #' Just check the if there is a duplicated trips. According to description there should not be duplicated trip.
  #length(unique(TNP_Part$Trip_ID))

  #' Let's start with the spatial match with census tracts.
  #' Lets start with the missing pickups.
  TNP_Pickup <- TNP_Part[,.(Pickup_Centroid_Longitude,Pickup_Centroid_Latitude)]

  #' Convert the points to geospatial object
  TNP_Pickup <- sf::st_as_sf(TNP_Pickup, coords=c("Pickup_Centroid_Longitude","Pickup_Centroid_Latitude"), crs=4326, na.fail = F)

  #' Let's use the st_intersects from sf package.
  #' Results returns a list. I want to convert to data.frame. Thanks to dplyr for pipe function.
  #' This function returns 2 value. One is index value for pickups and other one is index vvalue for the census tracts.
  #' I could filter out the ones already have census tracts but I want to match with the given one and found one.
  pickup_CT <- sf::st_intersects(TNP_Pickup, censusTracs, sparse = T) %>% as.data.frame()

  #' Set a new column name for new census pickup tracts
  TNP_Part$pickup_census_tract <- NA
  #' Now lets update the values.
  TNP_Part$pickup_census_tract[pickup_CT$row.id] <- censusTracs$geoid10[pickup_CT$col.id]  

  #' Below is same process for drop offs
  TNP_Dropoff <- TNP_Part[,.(Dropoff_Centroid_Longitude,Dropoff_Centroid_Latitude)]
  TNP_Dropoff <- sf::st_as_sf(TNP_Dropoff, coords=c("Dropoff_Centroid_Longitude","Dropoff_Centroid_Latitude"), crs=4326, na.fail = F)

  dropoff_CT <- sf::st_intersects(TNP_Dropoff, censusTracs, sparse = T) %>% as.data.frame()

  TNP_Part$dropoff_census_tract <- NA
  TNP_Part$dropoff_census_tract[dropoff_CT$row.id] <- censusTracs$geoid10[dropoff_CT$col.id]  

  #' Lets start the data analysis now.
  #' Never use the for loop. If you select to use, you will wait forever.
  #' Use the advantage of data.table process.
  #' Time stamps format are need to be converted from text to timestamp.
  TNP_Part[, `:=` (Trip_Start_Timestamp_formatted = as.POSIXct(Trip_Start_Timestamp, format = "%m/%d/%Y %I:%M:%S %p", tz = "UTC"),
                   Trip_End_Timestamp_formatted = as.POSIXct(Trip_Start_Timestamp, format = "%m/%d/%Y %I:%M:%S %p", tz = "UTC"))]
  #' Add dates and times for grouping purposes.
  TNP_Part[, `:=` (Trip_Start_Date = as.IDate(Trip_Start_Timestamp_formatted), # Extract the trip start date
                   Trip_End_Date = as.IDate(Trip_End_Timestamp_formatted), # Extract the trip end date
                   start_hour_of_day = hour(Trip_Start_Timestamp_formatted), # Extract the trip start hour
                   end_hour_of_day = hour(Trip_End_Timestamp_formatted), # Extract the trip end hour
                   wday_trip_start = wday(Trip_Start_Timestamp_formatted), # Extract the trip start weekday
                   wday_trip_end = wday(Trip_End_Timestamp_formatted))]  # Extract the trip end weekday

  #' Thanks data.table. I want to buy a cup of coffee.
  #' This is increadbly fast.
  #' Now get the number of trips for each census tracts depending on the what resolution you need.
  TNP_Summary <- TNP_Part[,  list(nTrip=.N, #Number of trips
                                  aveTrip_TT_sec=mean(Trip_Seconds)), # AVerage travel time
                          by = c('Trip_End_Date',
                                 'end_hour_of_day',
                                 'pickup_census_tract',
                                 'dropoff_census_tract')] %>% na.omit()

  print(Sys.time() - startTime)

  #' If you need to write this file to local disk you can use the append.
  #' fwrite is a function from data.table lib.
  #fwrite(TNP_Part,'TNP_Pickup_Dropoff.csv',append = T)

  #' That's it.
  #' Now it is time to return the analysed chunk of data set to bind_rows fucntion for collecting all the chunks in one data frame.
  #' My memory now can handle this
  return(TNP_Summary)

}))

#' Let see how long it will take to finish all analysis
print(Sys.time() - startTime_FullProcess)
#' Time difference of 1.033505 hours
#' Of course if I filtered out the ones already have the census tracts, it would take way much less time.

#' Now let's make the final analysis. Then move the second part of the analysis.
finalMergedData <- mergedData[, list(sum=sum(nTrip)), by = c('Trip_End_Date', 'end_hour_of_day', 'pickup_census_tract','dropoff_census_tract')]

#' As a final word, this script is memory friendly and fast.
#' I can also do this process in parallel mode, but source data is big. Therfore, it could crash.
#' It is kind of still slow because spatial analysis make it slow.
#' In my computer (Windows 10 Intel Xeon CPU E5-2687W v2 @ 340GHz) RStudio consumes average 3.6GB memory.
```
