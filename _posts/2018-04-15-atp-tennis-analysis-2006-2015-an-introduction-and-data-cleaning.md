---
layout: post
title: "ATP Tennis Analysis, 2006 - 2015: An Introduction and Data Cleaning"
author: "Ethan Wicker"
categories: journal
tags: [tennis, data analysis, data cleaning]
image: tennis-stadium-us-open.jpg
---

## Introduction

This post will be the first in a short series covering an analysis of ATP (Association of Tennis Professionals) Tennis matches.  

Thanks to [Jeff Sackmann](https://github.com/JeffSackmann), I was able to get my hands on a plethora of professional tennis data.  In short, Jeff has player and match statistics on just about every ATP match going back to 1968.  He has also data on many WTA (Woman's Tennis Association) matches as well as many Challenger Level matches.

For my analysis, I looked at the subset of ATP matches covering the 10 year span from 2006 to 2015.  In future posts, I may expand on this
to cover more years, and especially more recent years.

In this post, I will briefly explain the structure of the data, and then I will explain my methodology and rationale for wrangling and cleaning the data.  In future posts, I will reference the cleaned data from this post, as well as the R code.

During this post and subsequent posts, I will attempt to walk the line between breivty and clear explanations.  That is, 
I will try to interest those who are interested more in my methods, as well as those who are interested more in my results.  I want to provide my readers with a sense of the process, but to not bog them down too heavy in the technical details.

As a last comment, I have attempted to my make code incredibly readable and easy to follow.  I have leaned on the coding mantra that
clear code is greater than clever code, in hopes that anyone can easily follow my analysis.  In places, this has lead to some rather long
(but clear!) variable names.

For anyone interested, my entire R project for this analysis can be found here. (INSERT LINK)

---

## Loading Packages and Reading in Data

As with any analysis, the first step is to load any required packages and to read in the data.

I used `read_csv` from the `readr` package to read in the CSV files.  For reproducibility, I read in the files directly from their source URL.

The `matchnum` variable was read in as a integer type for years 2013-2015, while it was read in as a character type for years 2006-2012.  In order to combine the data frames into the larger `atp` data frame, I coerced `matchnum` to a charcter type for the 2013-2015 years, so that it was the same as the other data frames.  The `matchnum` variable should actually be an integer (it details the particular number of a match for each tournament), but since I end up deleting the variable entirely, it doesn't make a difference either way.

Next, I used `bind_rows` from the `dplyr` package to create the `atp` data frame.

Lastly, as a bit of personal preference, I used the remove function `rm` to remove the now unnecessary data frames from the workspace.
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

---

## Structure of the data

Before diving into data cleaning, I wanted to provide you with a sense of the structure of the data.  Below is an printout of the 
structure of the data using `glimpse` from Hadley Wickham's `dplyr` package.  This function is analgous to the function `str` which shows the structure of a data frame provided in base R, but `glimpse` fills up the entire console with as much of the data as will fit, which can be handy.

```r
glimpse(atp)

Observations: 30,524
Variables: 49
$ tourney_id         <chr> "2006-520", "2006-520", "2006-520", "2006-520", "2006-520", "2006-520", "2006-520", "2006-52...
$ tourney_name       <chr> "Roland Garros", "Roland Garros", "Roland Garros", "Roland Garros", "Roland Garros", "Roland...
$ surface            <chr> "Clay", "Clay", "Clay", "Clay", "Clay", "Clay", "Clay", "Clay", "Clay", "Clay", "Clay", "Cla...
$ draw_size          <int> 128, 128, 128, 128, 128, 128, 128, 128, 128, 128, 128, 128, 128, 128, 128, 128, 128, 128, 12...
$ tourney_level      <chr> "G", "G", "G", "G", "G", "G", "G", "G", "G", "G", "G", "G", "G", "G", "G", "G", "G", "G", "G...
$ tourney_date       <int> 20060529, 20060529, 20060529, 20060529, 20060529, 20060529, 20060529, 20060529, 20060529, 20...
$ match_num          <chr> "001", "002", "003", "004", "005", "006", "007", "008", "009", "010", "011", "012", "013", "...
$ winner_id          <int> 103819, 104268, 103018, 103454, 104607, 103835, 102967, 103017, 104339, 104154, 102999, 1036...
$ winner_seed        <int> 1, NA, NA, 32, 20, NA, NA, 13, 12, NA, NA, NA, 26, NA, NA, 7, 3, NA, NA, 31, 21, NA, NA, NA,...
$ winner_entry       <chr> NA, "LL", NA, NA, NA, NA, "WC", NA, NA, NA, "Q", NA, NA, NA, "Q", NA, NA, NA, NA, NA, NA, "Q...
$ winner_name        <chr> "Roger Federer", "Alejandro Falla", "Max Mirnyi", "Nicolas Massu", "Tomas Berdych", "Filippo...
$ winner_hand        <chr> "R", "L", "R", "R", "R", "R", "R", "R", "R", "R", "R", "R", "R", "R", "R", "R", "R", "R", "R...
$ winner_ht          <int> 185, 185, 196, 183, 196, 183, 188, 183, 196, 190, 178, 175, 190, 185, 193, 180, 180, 185, 18...
$ winner_ioc         <chr> "SUI", "COL", "BLR", "CHI", "CZE", "ITA", "FRA", "GER", "CRO", "CHI", "ITA", "ESP", "ARG", "...
$ winner_age         <dbl> 24.80493, 22.53799, 28.89528, 26.63381, 20.69541, 24.72827, 29.16359, 28.89802, 22.16290, 23...
$ winner_rank        <int> 1, 139, 49, 35, 20, 55, 115, 13, 12, 125, 158, 105, 29, 86, 160, 7, 3, 40, 71, 34, 21, 181, ...
$ winner_rank_points <int> 7010, 309, 785, 885, 1390, 716, 378, 1590, 1710, 355, 264, 411, 1090, 465, 262, 2020, 3065, ...
$ loser_id           <int> 103701, 102925, 104180, 103598, 103852, 103709, 103035, 103781, 103516, 102257, 104252, 1040...
$ loser_seed         <int> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, 17, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, ...
$ loser_entry        <chr> "Q", NA, NA, NA, NA, "Q", NA, NA, "WC", NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, ...
$ loser_name         <chr> "Diego Hartfield", "Justin Gimelstob", "Gilles Muller", "Xavier Malisse", "Feliciano Lopez",...
$ loser_hand         <chr> "R", "R", "L", "R", "L", "R", "L", "L", "R", "L", "R", "R", "R", "R", "R", "R", "R", "R", "L...
$ loser_ht           <int> 185, 196, 193, 185, 188, 188, 185, 183, 180, 193, 190, 183, 178, 193, 180, 178, 183, 178, 19...
$ loser_ioc          <chr> "ARG", "USA", "LUX", "BEL", "ESP", "DEN", "ITA", "AUT", "AUS", "GBR", "GER", "USA", "FRA", "...
$ loser_age          <dbl> 25.32238, 29.33607, 23.05544, 25.85900, 24.68720, 25.29774, 28.80219, 25.01848, 26.25051, 32...
$ loser_rank         <int> 157, 92, 66, 37, 48, 143, 81, 70, 195, 42, 78, 17, 51, 36, 64, 109, 57, 91, 112, 101, 84, 58...
$ loser_rank_points  <int> 265, 445, 605, 856, 790, 299, 509, 580, 205, 830, 538, 1475, 777, 867, 612, 399, 683, 450, 3...
$ score              <chr> "7-5 7-6(2) 6-2", "6-4 6-4 6-2", "6-3 6-1 7-6(3)", "6-1 7-5 1-6 4-6 9-7", "6-2 6-4 6-3", "6-...
$ best_of            <int> 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5,...
$ round              <chr> "R128", "R128", "R128", "R128", "R128", "R128", "R128", "R128", "R128", "R128", "R128", "R12...
$ minutes            <int> 156, 113, 103, 265, 124, 127, 156, 131, 196, 175, 205, 147, 237, 209, 107, 121, 173, 112, 16...
$ w_ace              <int> 5, 3, 6, 6, 7, 0, 3, 9, 10, 2, 4, 6, 10, 6, 15, 6, 0, 12, 7, 6, 7, 5, 5, 6, 1, 5, 12, 5, 2, ...
$ w_df               <int> 2, 1, 2, 5, 2, 2, 0, 2, 0, 3, 9, 2, 3, 5, 3, 2, 4, 1, 4, 2, 0, 0, 2, 2, 1, 8, 3, 2, 2, 4, 2,...
$ w_svpt             <int> 106, 83, 77, 180, 86, 104, 111, 85, 135, 141, 157, 119, 161, 183, 86, 85, 112, 79, 125, 83, ...
$ w_1stIn            <int> 70, 66, 43, 99, 47, 81, 70, 47, 79, 77, 104, 73, 91, 127, 53, 64, 60, 45, 86, 47, 46, 63, 14...
$ w_1stWon           <int> 48, 44, 38, 61, 35, 50, 52, 36, 54, 55, 71, 53, 70, 81, 44, 46, 41, 39, 65, 40, 36, 40, 110,...
$ w_2ndWon           <int> 22, 8, 24, 44, 28, 13, 24, 26, 41, 33, 25, 27, 30, 28, 17, 11, 28, 20, 21, 18, 22, 11, 13, 2...
$ w_SvGms            <int> 16, 14, 14, 26, 14, 16, 17, 14, 20, 19, 24, 20, 26, 25, 14, 13, 15, 14, 20, 13, 13, 12, 27, ...
$ w_bpSaved          <int> 6, 3, 0, 16, 6, 9, 6, 1, 12, 8, 6, 5, 5, 14, 4, 1, 6, 3, 6, 6, 6, 4, 8, 3, 5, 3, 2, 15, 3, 1...
$ w_bpFaced          <int> 9, 6, 0, 23, 6, 14, 8, 1, 13, 11, 12, 8, 11, 21, 5, 2, 8, 4, 8, 6, 7, 5, 10, 6, 5, 8, 2, 19,...
$ l_ace              <int> 0, 2, 8, 12, 4, 2, 2, 8, 5, 6, 3, 1, 6, 11, 4, 1, 6, 3, 4, 1, 1, 0, 13, 2, 3, 6, 2, 3, 3, 1,...
$ l_df               <int> 2, 3, 3, 3, 2, 4, 0, 1, 6, 8, 3, 1, 1, 5, 2, 3, 7, 4, 1, 6, 1, 6, 5, 2, 0, 7, 0, 3, 3, 2, 2,...
$ l_svpt             <int> 133, 89, 88, 155, 82, 108, 93, 96, 116, 150, 167, 114, 168, 152, 98, 94, 127, 108, 110, 88, ...
$ l_1stIn            <int> 92, 55, 61, 90, 50, 68, 53, 65, 78, 90, 102, 76, 92, 92, 61, 57, 61, 65, 72, 52, 70, 52, 144...
$ l_1stWon           <int> 53, 33, 41, 65, 31, 39, 39, 45, 53, 55, 64, 43, 60, 66, 40, 34, 37, 34, 50, 33, 38, 22, 105,...
$ l_2ndWon           <int> 20, 12, 12, 26, 15, 16, 20, 10, 25, 28, 33, 22, 42, 28, 17, 15, 29, 24, 19, 14, 13, 19, 22, ...
$ l_SvGms            <int> 16, 14, 14, 26, 13, 16, 16, 14, 18, 19, 25, 18, 26, 24, 15, 13, 15, 15, 19, 12, 13, 11, 27, ...
$ l_bpSaved          <int> 15, 3, 8, 10, 7, 9, 0, 10, 2, 6, 11, 8, 7, 11, 4, 7, 13, 9, 3, 8, 11, 13, 6, 4, 3, 9, 13, 6,...
$ l_bpFaced          <int> 21, 10, 12, 18, 11, 16, 4, 14, 5, 12, 19, 15, 14, 18, 9, 13, 18, 15, 7, 13, 17, 20, 9, 10, 9...
```

To give a brief summary, currently the data has over 30,000 rows across 49 variables.

Every row in the data frame is a single match played, and information about the tournament name, location, playing surface, draw size and more is given.  Included on each row is also information about the winner of the match and the loser of the match, and some information about each player, such as height, age, which hand the player uses, rank, and the country the player represents from the International Olympic Committee (IOC).  This is equivalent to the country of origin for most, but not all players.  Lastly, the final entries of each row contain match statistics for each match, as well as the score of the match.  These statistics are identified by either a `w_` prefix or a `l_` prefix, such as `w_ace` or `l_svpt`, to denote statistics of the match winner and loser, respectively.  For example, here `w_ace` is the number of aces served by the winner of the match, while `l_svpt` is the number of service points served by the loser of the match.  It is important to note here that all of these statistics are given as integer values, and not percentages.  From these integer values it is possible to calculate all of the standard match statistics such as percentage of points won off a player's first serve, or percentage of break points saved, as I will describe in subsequent posts.

---

## Data Cleaning

The next portion of the analysis involved cleaning the data.  For this particular analysis, this mostly involved dropping a few unneeded columns, changing a few variable types, and changing some column names.

At the end of this section, the new `atp` data frame will be pretty similar to the `atp` data frame above, just with some aforementioned alterations.  I should point out here, that at this point the data frame does not quite meet the specifications commonly known as tidy data.  

The main concepts of tidy data, for anyone unfamiliar with it, basically boil down to organizing a data frame such that each column contains a variable, and each row contain a different observation.  For example, instead of having `winner_hand` and `loser_hand` columns, there would be just one column named `hand` and a different column, possibily named `match_result`, indicating whether a player won or lost a particular match.  The `spread` and `gather` functions from the `tidyr` package are commonly utilized to preform this data frame manipulation.  With my data frame organized in this manner, it would be possibly to see who won or lost any particular match, and whether that player played left-handed or right-handed.  I elected to go about my analysis in a slightly different manner (as you'll notice in subsequent posts), but I wanted to include a brief description of tidy data for anyone learning from my analysis unfamiliar the idea.  I liked the one match per row structure of the data set, and I was able to get equivalent results as I would have gotten from a tidy data frame.

For the remainder of this post, I will be describing the various steps I took to organize and clean the `atp` data frame.

As a first step, I changed the `tourney_date` variable, which was read into R as an integer type into a date type.  I did this using the fantastic `lubridate` package, which make class conversions of this kind a breeze.  For example, the `tourney_date` for the the 2015 US Open was originally read in as `20150831`.  The `ymd` function from the `lubridate` package quickly converts this to `2015-08-31`, and changes variable type to date as well.

```r
# tourney_date read in as an integer type
atp$tourney_date <- ymd(atp$tourney_date)
```
Next, I deleted various subsets of tournaments from the data set.  In particular, I deleted:

* Davis Cup matches
* Challenger Level matches
* Olmypic matches

I elected to delete Davis Cup matches simply because they do not match the rest of the data, and I did not want their influence to effect my analysis.  For anyone unfamiliar, the Davis Cup is a yearly "knock-out" style tournament held each year between teams from countries all over the world.  Play takes place between two teams in round robin fashion.  Although it is a fun and interesting tennis event, it is not of my interest in this analysis.

The Challenger Level matches were deleted as they are not ATP World level, They are the second highest tier of play in Men's Professional Tennis.  Since these matches were only spartically included in a couple years, they were likely included by mistake.

Lastly, I debated with myself for a good while about whether to include the Olmypic matches in my analysis or not.  In the end I decided not to, partially because the data was a bit incomplete and partially because the Olympic matches are not ATP sanctioned events. 

```r
# Removing Davis Cup - almost incomplete, and Davis Cup play doesn't match the rest of the data
index_davis_cup <- which(str_detect(atp$tourney_name, "Davis Cup"))
atp <- atp[-index_davis_cup, ]

# Removing Challenger level matches - shouldn't be in this data set, possibly a mistake (only in some years)
index_challenger_level <- which(str_detect(atp$tourney_level, "C"))
atp <- atp[-index_challenger_level, ]

# Removing London Olmpyics
index_london_olmpyics <- which(str_detect(atp$tourney_name, "London"))
atp <- atp[-index_london_olmpyics, ]

# Removing Beijing Olmpyics
index_beijing_olmpyics <- which(str_detect(atp$tourney_name, "Beijing"))
atp <- atp[-index_beijing_olmpyics, ]
```

Here I just reformatted one particular tournament name that was slightly different for one year of matches.  Notice the unnecessary apostrophe.

```r
atp$tourney_name <- recode(atp$tourney_name, 
                           "'s-Hertogenbosch" = "s-Hertogenbosch")                          
```                           

Next, there were 168 rows in my data frame with entirely incompletely match statistics, spread across various tournaments and years, which had little value to my analysis.  To delete them, I indexed the rows by finding which rows had missing data for both the `w_ace` and `l_ace` columns.

```r
# Removing rows with entirely incomplete data
index_empty_rows <- which(is.na(atp$w_ace) & is.na(atp$l_ace))
atp <- atp[-index_empty_rows, ]
```

After deleting rows without any match statistics, I cleaned up `atp` by deleting columns that were not necessary for my analysis. There columns included `match_num`, `winner_entry`, `loser_entry`, `winner_rank_points`, `loser_rank_points`, `winner_seed`, and `loser_seed`.  Of note here, in leiu of winner and loser seed at a particular tournament, I keep `winner_rank` and `loser_rank`, which contains information on the winner and loser's ATP ranking at the time of the match.

```r
# Deleting unnecessary columns
atp <- subset(atp, select = -c(match_num, winner_entry, loser_entry, winner_rank_points, loser_rank_points))

# Deleting winner_seed/loser_seed in leiu of world winner_rank/loser_rank
atp <- subset(atp, select = -c(winner_seed, loser_seed))
```

Below I filtered out all matches where a player either retired, or where a walkover occured.  

For anyone unfamiliar with these concepts, a player retires when that player is unable to finish the match, typically due to injury.  In contrast, a walkover occurs when a player is unable to even begin the match.  At the professional level, this is rare and is typically the result of a nagging injury recieved in a previous match.

These observations where deleted because their data is either incomplete, as in the case of a walkover, or the data does not represent an entire match, as in the case of a retirement.  Furthermore, match data where a player retired likely indicates the losing player was injuried, and match statistics from this situation could be influenced by this.

```r
# Filtering out rows where a player retired
atp <- atp %>% filter(!str_detect(score, "RET"))


# Filtering out walkover's - i.e. a foreit, where the losing player doesn't make it on court
atp <- atp %>% filter(score != "W/O")
```

Next, I renamed the abbreviations in the `tourney_level`, `winner_hand`, and `loser_hand` columns to be a bit more clear using the `recode` function.

I also renamed the `winner_ht` and `loser_ht` columns to `winner_height` and `loser_height`.

```r
# Recoding tourney_levels, hand, and renaming height columns
atp$tourney_level <- recode(atp$tourney_level, 
                                   A = "ATP Tour 250/500 Series", 
                                   M = "Masters", 
                                   G = "Grand Slam", 
                                   F = "Tour Final")

atp$winner_hand <- recode(atp$winner_hand, 
                               L = "Left",
                               R = "Right",
                               U = "Unknown")

atp$loser_hand <- recode(atp$loser_hand, 
                               L = "Left",
                               R = "Right",
                               U = "Unknown")

setnames(atp, old = "winner_ht", new = "winner_height")
setnames(atp, old = "loser_ht", new = "loser_height")
```

Lastly, I redefined the variable type of `round`, `best_of`, `draw_size`, and `tourney_level`.  Each of these variables were read in as character or integer types, when they are really factors. 

I also defined the levels of `round`, since the rounds of a tournament have an inherit ordering to them.  To clarify here, the last level of `round` is `RR`.  This stands for round-robin, and indicates matches that were played in a round-robin style tournament.  The only matches that meet that specification are those that take place each year during the ATP World Tour Finals.

```r
# Defining best_of, draw_size, tourney_level to be factors 
# Ordering levels of "round"
atp$round <- factor(atp$round, levels = c("R128", "R64", "R32", "R16", "QF", "SF", "F", "RR"))

atp$best_of <- as.factor(atp$best_of)

atp$draw_size <- as.factor(atp$draw_size)

atp$tourney_level <- as.factor(atp$tourney_level)
```

---

## Conclusion

The structure of the clean `atp` data frame is provided below.  In my next post in this series, I'll use this data frame to create two very useful data frames: `atp_stats_by_player_by_year` and `atp_stats_overall_by_player`, each of which contain player statistics.

```r
> glimpse(atp)

Observations: 25,693
Variables: 42
$ tourney_id    <chr> "2006-520", "2006-520", "2006-520", "2006-520", "2006-520", "2006-520", "2006-520", "2006-520", "...
$ tourney_name  <chr> "Roland Garros", "Roland Garros", "Roland Garros", "Roland Garros", "Roland Garros", "Roland Garr...
$ surface       <chr> "Clay", "Clay", "Clay", "Clay", "Clay", "Clay", "Clay", "Clay", "Clay", "Clay", "Clay", "Clay", "...
$ draw_size     <fct> 128, 128, 128, 128, 128, 128, 128, 128, 128, 128, 128, 128, 128, 128, 128, 128, 128, 128, 128, 12...
$ tourney_level <fct> Grand Slam, Grand Slam, Grand Slam, Grand Slam, Grand Slam, Grand Slam, Grand Slam, Grand Slam, G...
$ tourney_date  <date> 2006-05-29, 2006-05-29, 2006-05-29, 2006-05-29, 2006-05-29, 2006-05-29, 2006-05-29, 2006-05-29, ...
$ winner_id     <int> 103819, 104268, 103018, 103454, 104607, 103835, 102967, 103017, 104339, 104154, 102999, 103656, 1...
$ winner_name   <chr> "Roger Federer", "Alejandro Falla", "Max Mirnyi", "Nicolas Massu", "Tomas Berdych", "Filippo Vola...
$ winner_hand   <chr> "Right", "Left", "Right", "Right", "Right", "Right", "Right", "Right", "Right", "Right", "Right",...
$ winner_height <int> 185, 185, 196, 183, 196, 183, 188, 183, 196, 190, 178, 175, 190, 185, 193, 180, 180, 185, 185, 18...
$ winner_ioc    <chr> "SUI", "COL", "BLR", "CHI", "CZE", "ITA", "FRA", "GER", "CRO", "CHI", "ITA", "ESP", "ARG", "CZE",...
$ winner_age    <dbl> 24.80493, 22.53799, 28.89528, 26.63381, 20.69541, 24.72827, 29.16359, 28.89802, 22.16290, 23.1567...
$ winner_rank   <int> 1, 139, 49, 35, 20, 55, 115, 13, 12, 125, 158, 105, 29, 86, 160, 7, 3, 40, 71, 34, 21, 181, 129, ...
$ loser_id      <int> 103701, 102925, 104180, 103598, 103852, 103709, 103035, 103781, 103516, 102257, 104252, 104068, 1...
$ loser_name    <chr> "Diego Hartfield", "Justin Gimelstob", "Gilles Muller", "Xavier Malisse", "Feliciano Lopez", "Kri...
$ loser_hand    <chr> "Right", "Right", "Left", "Right", "Left", "Right", "Left", "Left", "Right", "Left", "Right", "Ri...
$ loser_height  <int> 185, 196, 193, 185, 188, 188, 185, 183, 180, 193, 190, 183, 178, 193, 180, 178, 183, 178, 190, 19...
$ loser_ioc     <chr> "ARG", "USA", "LUX", "BEL", "ESP", "DEN", "ITA", "AUT", "AUS", "GBR", "GER", "USA", "FRA", "BEL",...
$ loser_age     <dbl> 25.32238, 29.33607, 23.05544, 25.85900, 24.68720, 25.29774, 28.80219, 25.01848, 26.25051, 32.7255...
$ loser_rank    <int> 157, 92, 66, 37, 48, 143, 81, 70, 195, 42, 78, 17, 51, 36, 64, 109, 57, 91, 112, 101, 84, 58, 74,...
$ score         <chr> "7-5 7-6(2) 6-2", "6-4 6-4 6-2", "6-3 6-1 7-6(3)", "6-1 7-5 1-6 4-6 9-7", "6-2 6-4 6-3", "6-4 7-6...
$ best_of       <fct> 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5, 5...
$ round         <fct> R128, R128, R128, R128, R128, R128, R128, R128, R128, R128, R128, R128, R128, R128, R128, R128, R...
$ minutes       <int> 156, 113, 103, 265, 124, 127, 156, 131, 196, 175, 205, 147, 237, 209, 107, 121, 173, 112, 161, 96...
$ w_ace         <int> 5, 3, 6, 6, 7, 0, 3, 9, 10, 2, 4, 6, 10, 6, 15, 6, 0, 12, 7, 6, 7, 5, 5, 1, 5, 12, 5, 2, 6, 7, 2,...
$ w_df          <int> 2, 1, 2, 5, 2, 2, 0, 2, 0, 3, 9, 2, 3, 5, 3, 2, 4, 1, 4, 2, 0, 0, 2, 1, 8, 3, 2, 2, 4, 2, 3, 2, 3...
$ w_svpt        <int> 106, 83, 77, 180, 86, 104, 111, 85, 135, 141, 157, 119, 161, 183, 86, 85, 112, 79, 125, 83, 81, 7...
$ w_1stIn       <int> 70, 66, 43, 99, 47, 81, 70, 47, 79, 77, 104, 73, 91, 127, 53, 64, 60, 45, 86, 47, 46, 63, 140, 52...
$ w_1stWon      <int> 48, 44, 38, 61, 35, 50, 52, 36, 54, 55, 71, 53, 70, 81, 44, 46, 41, 39, 65, 40, 36, 40, 110, 39, ...
$ w_2ndWon      <int> 22, 8, 24, 44, 28, 13, 24, 26, 41, 33, 25, 27, 30, 28, 17, 11, 28, 20, 21, 18, 22, 11, 13, 15, 32...
$ w_SvGms       <int> 16, 14, 14, 26, 14, 16, 17, 14, 20, 19, 24, 20, 26, 25, 14, 13, 15, 14, 20, 13, 13, 12, 27, 12, 2...
$ w_bpSaved     <int> 6, 3, 0, 16, 6, 9, 6, 1, 12, 8, 6, 5, 5, 14, 4, 1, 6, 3, 6, 6, 6, 4, 8, 5, 3, 2, 15, 3, 10, 20, 7...
$ w_bpFaced     <int> 9, 6, 0, 23, 6, 14, 8, 1, 13, 11, 12, 8, 11, 21, 5, 2, 8, 4, 8, 6, 7, 5, 10, 5, 8, 2, 19, 4, 15, ...
$ l_ace         <int> 0, 2, 8, 12, 4, 2, 2, 8, 5, 6, 3, 1, 6, 11, 4, 1, 6, 3, 4, 1, 1, 0, 13, 3, 6, 2, 3, 3, 1, 1, 6, 1...
$ l_df          <int> 2, 3, 3, 3, 2, 4, 0, 1, 6, 8, 3, 1, 1, 5, 2, 3, 7, 4, 1, 6, 1, 6, 5, 0, 7, 0, 3, 3, 2, 2, 8, 3, 1...
$ l_svpt        <int> 133, 89, 88, 155, 82, 108, 93, 96, 116, 150, 167, 114, 168, 152, 98, 94, 127, 108, 110, 88, 100, ...
$ l_1stIn       <int> 92, 55, 61, 90, 50, 68, 53, 65, 78, 90, 102, 76, 92, 92, 61, 57, 61, 65, 72, 52, 70, 52, 144, 43,...
$ l_1stWon      <int> 53, 33, 41, 65, 31, 39, 39, 45, 53, 55, 64, 43, 60, 66, 40, 34, 37, 34, 50, 33, 38, 22, 105, 26, ...
$ l_2ndWon      <int> 20, 12, 12, 26, 15, 16, 20, 10, 25, 28, 33, 22, 42, 28, 17, 15, 29, 24, 19, 14, 13, 19, 22, 9, 22...
$ l_SvGms       <int> 16, 14, 14, 26, 13, 16, 16, 14, 18, 19, 25, 18, 26, 24, 15, 13, 15, 15, 19, 12, 13, 11, 27, 12, 2...
$ l_bpSaved     <int> 15, 3, 8, 10, 7, 9, 0, 10, 2, 6, 11, 8, 7, 11, 4, 7, 13, 9, 3, 8, 11, 13, 6, 3, 9, 13, 6, 9, 2, 7...
$ l_bpFaced     <int> 21, 10, 12, 18, 11, 16, 4, 14, 5, 12, 19, 15, 14, 18, 9, 13, 18, 15, 7, 13, 17, 20, 9, 9, 15, 18,...
```
