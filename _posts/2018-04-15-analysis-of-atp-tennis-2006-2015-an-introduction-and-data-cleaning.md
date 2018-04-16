---
layout: post
title: "Analysis of ATP Tennis, 2006 - 2015: An Introduction and Data Cleaning"
author: "Ethan Wicker"
categories: journal
tags: [tennis, data analysis, data cleaning]
image: mountains.jpg
---

## Introduction

This post will be the first in a short series covering an analysis of ATP (Association of Tennis Professionals) Tennis matches.  

Thanks to Jeff Sackmann (insert link to github here), I was able to get my hands on a plethora of professional tennis data.  In short, 
Jeff has player and match statistics on just about every ATP match going back to 1968.  He has also data on many WTA (Woman's Tennis Association) matches as well as many Challenger level matches.

For my analysis, I looked at the subset of ATP matches covering the 10 year span from 2006 to 2015.  In future posts, I may expand on this
to cover more years, and especially more recent years.

In this post, I will briefly explain the structure of data, and then I will explain my methodology and rationale for wrangling and cleaning the data.  In future posts, I will reference the cleaned data from this post, as well as the R code.

During this post and subsequent posts, I will attempt to walk the line between breivty and clear explanations.  That is, 
I will try to interest those who are interested more in my methods, as well as those who are interested more in my results.

As a last comment, I have attempted to my make code incredibly readable and easy to follow.  I have leaned on the coding mantra that
clear code is greater than clever code, in hopes that anyone can easily follow my analysis.  In places, this has lead to some rather long
(but clear!) variable names.

For anyone interested, my entire R project for this analysis can be found here. (INSERT LINK)

## Loading Packages and Reading in Data

This portion of the analysis is pretty straight forward.

* I used `read_csv` from the `readr` package to read in the CSV files.  For reproducibility, I read in the files directly from their source URL.

 (REWORD THIS)* The `matchnum` variable was read in as a integer type for years 2013-2015, while it was read in as a character type for years 2006-2012.  In order to combine the data frames into the larger `atp` data frame, I coerced `matchnum` to a charcter type for the 2013-2015 years, so that it was the same as the other data frames.  The `matchnum` variable should actually be an integer type (it details the particular number of a match for each tournament), but since I end up deleting the variable entirely, it doesn't actually matter.

* Using `bind_rows` from the `dplyr` package, I created the `atp` data frame.

* Lastly, as a bit of personal preference, I used the remove function `rm` to remove the now unnecessary data frames from the workspace.
This step is not necessary, but it allows for a cleaner working environment.

```r
library(tidyverse)        # dplyr, ggplot2, purrr, readr
library(lubridate)
library(data.table)
library(directlabels)     # used for labeling plots with ggplot

# Reading data from source
atp_matches_2006 <- read_csv("https://raw.githubusercontent.com/JeffSackmann/tennis_atp/master/atp_matches_2006.csv")
atp_matches_2007 <- read_csv("https://raw.githubusercontent.com/JeffSackmann/tennis_atp/master/atp_matches_2007.csv")
atp_matches_2008 <- read_csv("https://raw.githubusercontent.com/JeffSackmann/tennis_atp/master/atp_matches_2008.csv")
atp_matches_2009 <- read_csv("https://raw.githubusercontent.com/JeffSackmann/tennis_atp/master/atp_matches_2009.csv")
atp_matches_2010 <- read_csv("https://raw.githubusercontent.com/JeffSackmann/tennis_atp/master/atp_matches_2010.csv")
atp_matches_2011 <- read_csv("https://raw.githubusercontent.com/JeffSackmann/tennis_atp/master/atp_matches_2011.csv")
atp_matches_2012 <- read_csv("https://raw.githubusercontent.com/JeffSackmann/tennis_atp/master/atp_matches_2012.csv")
atp_matches_2013 <- read_csv("https://raw.githubusercontent.com/JeffSackmann/tennis_atp/master/atp_matches_2013.csv")
atp_matches_2014 <- read_csv("https://raw.githubusercontent.com/JeffSackmann/tennis_atp/master/atp_matches_2014.csv")
atp_matches_2015 <- read_csv("https://raw.githubusercontent.com/JeffSackmann/tennis_atp/master/atp_matches_2015.csv")



# Making copies of original data - preserve integrity of data

atp_2006 <- atp_matches_2006
atp_2007 <- atp_matches_2007
atp_2008 <- atp_matches_2008
atp_2009 <- atp_matches_2009
atp_2010 <- atp_matches_2010
atp_2011 <- atp_matches_2011 
atp_2012 <- atp_matches_2012
atp_2013 <- atp_matches_2013
atp_2014 <- atp_matches_2014
atp_2015 <- atp_matches_2015

atp_2013$match_num <- as.character(atp_2013$match_num)
atp_2014$match_num <- as.character(atp_2014$match_num)
atp_2015$match_num <- as.character(atp_2015$match_num)



# Combining dataframes

atp <- bind_rows(atp_2006, atp_2007, atp_2008, atp_2009, atp_2010,
                 atp_2011, atp_2012, atp_2013, atp_2014, atp_2015)


# Removing some data frames for clarity and simplicity of workspace
rm(atp_matches_2006, atp_matches_2007, atp_matches_2008, atp_matches_2009, atp_matches_2010,
   atp_matches_2011, atp_matches_2012, atp_matches_2013, atp_matches_2014, atp_matches_2015)

rm(atp_2006, atp_2007, atp_2008, atp_2009, atp_2010,
   atp_2011, atp_2012, atp_2013, atp_2014, atp_2015)
  
```

## Structure of the data

INCLUDE GLIMPSE OF OUTPUT HERE

```r
atp
```


## Data Cleaning

The next portion of the analysis involves actually cleaning the data.  Clearly, there was quite a bit of exploratory data analysis involved throughout this process (as well as the rest of the the project).  The below code and explanation is the result of refining my code to make it presentable. 



```r
# tourney_date read in as an integer type
atp$tourney_date <- ymd(atp$tourney_date)
```
