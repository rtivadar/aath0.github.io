---
layout: post
title: "ATP Tennis Analysis, 2006 - 2015: Constructing Useful Data Frames"
author: "Ethan Wicker"
categories: journal
tags: [tennis, data analysis, data frames]
image: tennis-djokovic.jpg
---

## Introduction

This post is the second in a series covering an analysis of ATP (Association of Tennis Professionals) tennis matches.  

My first post in this series covered an introduction to the data I would be working with, as well as some cleaning and organizing of the data.  This post will cover how I constructed some more useful data structures from the `atp` data frame I described in the earlier post.  

You can find my first post in this series by following this link: [ATP Tennis Analysis, 2006 - 2015: An Introduction and Data Cleaning](https://ethanwicker.github.io/journal/atp-tennis-analysis-2006-2015-an-introduction-and-data-cleaning.html).

Also, if you would like to see my entire R project, you can find it here.  (INCLUDE LINKS)

The rest of this post references the `atp` data frame.  For clarity and completeness, this data frame has 25,693 observations across 42 different variables.  The structure of the data frame is provided below using the `glimpse` function.  See the first post in this series for more details on how I constructed `atp`.

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

This post will likely be my longest in this series, and it will also be my most technical in terms of code.  During my analysis, the bulk of my time was spent constructing the data frames I'll describe below.

The below R code relies heavily on the `dplyr` package, inparticular the `filer`, `mutate`, and `summarize` functions.  I also make common use of the pipe `%>%` operator, originally from the `magrittr` package by Stefan Milton.  `dplyr` imports the pipe `%>%` operator when the package is loaded.

This post will be divided into three main sections.  By the end of the three sections, I will have described how I created two very useful data frames: `atp_stats_by_player_by_year` and `atp_stats_overall_by_player`.



(INCLUDE TABLE OF CONTENTS?)

To preview this post, the three sections and their purposes are below:

1. Calculating Percent of Rounds Won in Tournaments

    * In this section, I will describe a metric I created, namely determining how far a player advances in a tournament on average, as a         percent.  
    * I will create two data frames in this section.  The first data frame, `atp_pct_rounds_won_overall_by_player`, contains a percentage       for each player of how far that player advanced through tournaments on average, aggregated over all years in my dataset.  The second       data frame, `atp_pct_rounds_won_by_year_by_player`, contains a percentage for each player of how far that player advanced through         tournaments on average, per year.
  
2. Calculating Player Stats per Year

    * In this section, I will construct `atp_pct_rounds_won_overall_by_player`, a data frame that contains information on match statistics       for each player, per year.  
    * Examples of statistics that will be included are percentage of service games won, and percentage break points forced, among many           more.

3. Calculating Player Stats Overall

    * In this section, I will construct `atp_pct_rounds_won_by_year_by_player`, a data frame that contains information on match statistics       for each player.  This will be overall statistics for each player across all years of my dataset.
    * These statistics will be nearly identical to the statistics in the data frame constructed in the second section.
  
## Calculating Percent of Rounds Won in Tournaments
  
As briefly mentioned in the short preview above, in this section, I will be constructing two similiar data frames.  These data frames are `atp_pct_rounds_won_overall_by_player` and `atp_pct_rounds_won_by_year_by_player`.
  
Before I begin my discussion of how I constructed these data frames, I first need to explain the metric I am creating.  In short, I wanted  a way to quantify how far a player typically advances through a tournament, to see which players usually win more rounds than over players.  The clear problem here is that not all tournaments have the same number of rounds.  So winning a quarterfinal match in a grandslam where the draw size is 128 players isn't the same as winning a quarterfinal match in a much smaller tournament with a draw size of 32.

To quantify how far a player advanced in a tournament, I determined how many rounds of that tournament the player won, and then averaged these percentages out to get the yearly and overall percentages per player.

The metric works like this:  If a player wins no rounds in the tournament, that player advanced through 0% of the tournament.  If a player wins the entire tournament, that player advanced through 100% of the tournament.  If a player wins three rounds of a seven round tournament, but loses in the fourth round, then that player advanced through 3/7 = 42.86% of the tournament.

To create my metric, I relied on how R defines the levels in a factor variable.  Under the hood, R assigns each level in a factor a number, determined by the ordering of the levels.  In my previous post, I defined the ordering of the `round` variable.  The ordering is below, provided via the `levels` function:

```

> levels(atp$round)
[1] "R128" "R64"  "R32"  "R16"  "QF"   "SF"   "F"    "RR"  

```

For example, R assigns the `R128` level, representing the round of 128 players, the number 1.  Similar, R assigns the `R64` level the number 2, and so on.

To begin determining how far a player advances through a tournament, I first determined the last round that player won, for each tournament.  To do this, I first grouped `atp` by `tourney_name`, `tourney_date`, and `winner_name`.  This grouped the data for each combination of a tournament, the year of that tournament, and all the players who won a match in that tournament.  Since I only need the last round a player won, I used the `summarize` function and the `max` function to create a data frame of each tournament, tournamenament date, winner name, and the last round of a tournament that player won.  

Remember, I'm utilizing the under the hood numerical assignment of levels in a factor variable to do this.  Thus `number_round_of_tournament_advanced_through` variable in the below data frame is a simply a vector of integers, all between 1 and 8.

(INSERT PREVIEW OF DATA FRAME)

```r
  
  # Determining the last round a player won
atp_last_round_player_won_by_tournament <- atp %>% 
  group_by(tourney_name, tourney_date, winner_name) %>% 
  summarize(number_round_of_tournament_advanced_through = max(as.numeric(round)))

# Renaming "winner_name" to "name"
setnames(atp_last_round_player_won_by_tournament, old = "winner_name", new = "name")

```
In the `atp_last_round_player_won_by_tournament` data frame created above, I only have information on players who won at least one match per tournament.  To get the remaining players who lost in the first round of any given tournament, I used a similar method to the above, with some slight alterations.

To determine which players lost in the first round, I grouped `atp` by `tourney_name`, `tourney_date`, `loser_name`, and once again summarized using `max` to determine the last round a player played in at any given tournament.  

However, here I only want those players that lost in the first round, and not those that lost in any other round.  To determine those players, I first need to know the the number of the first round in the tournament.  Notice here that this will change depending on the tournament.  For example, the first round of the US Open is the round of 128, and R assigned the `R128` level of the round variable the number 1.  For a different sized tournament, say Acapulco with a draw size of only 32 players, the first round for this tournament is the round of 32, but R assigned the `R32` level of the round variable the number 3.

To determine the number of the first round of each tournament, I grouped the data by `tourney_name` and `tourney_date` and then filtered the data such that the resulting data frame had only those players who lost in the first round using the `min` function.

Lastly here, I used `mutate` to subtract 1 from each value in the `number_round_of_tournament_advanced_through` variable.  This needed to be done, because even though a losing player may have played in the first round of a tournament, that player did not actually win that round.  Subtracting 1 from each of these values allows me to later determine which players advances through 0 rounds of a tournament.

```r

# Creating data frame where players lost in the first round of a tournament
atp_first_round_losers_by_tournament <- atp %>% 
  group_by(tourney_name, tourney_date, loser_name) %>% 
  summarize(number_round_of_tournament_advanced_through = max(as.numeric(round))) %>% 
  group_by(tourney_name, tourney_date) %>% 
  filter(number_round_of_tournament_advanced_through == 
           min(number_round_of_tournament_advanced_through)) %>% 
  mutate(number_round_of_tournament_advanced_through = number_round_of_tournament_advanced_through - 1)

# Renaming "loser_name" to "name"
setnames(atp_first_round_losers_by_tournament, old = "loser_name", new = "name")

```

After creating these two separate data frames, one containing the last round of a tournament a player won, and the other containing any player who lost in the first round of a tournament, I used `bind_rows` from `dplyr` to combine them into one one data frame.  At this point, this data frame contains information on the number of the round a player last advanced through, for each combination of player and tournament in the original `atp` data frame.

```r

atp_pct_rounds_won_by_tournament_by_year <- bind_rows(atp_last_round_player_won_by_tournament, 
                                                      atp_first_round_losers_by_tournament)

```

Next, I determined the number of rounds that are in each tournament.  To create `atp_number_of_rounds_by_tournament`, I first grouped by the `tourney_name` and `tourney_date` variables.  I grouped by both of these variables, as opposed to just `tourney_name`, as some tournaments may change their size between years.  I then used summarize to create an index variable for the number of the first round of each tournament, as well as to determine the number of rounds in each tournament.

```r

# Determining how many rounds are in a tournament, per year
atp_number_of_rounds_by_tournament <- atp %>% 
  group_by(tourney_name, tourney_date) %>% 
  summarize(index_of_first_round_in_tournament = min(as.numeric(round)),
            number_rounds_in_tournament = max(as.numeric(round)) - min(as.numeric(round)) + 1)
            
```

After creating the above data frame which contains information on the number of rounds in each tournament, I created `atp_pct_rounds_won_by_tournament_by_year` by joining the previous two data frames together.  To do this, I used the `full_join` command available in the `dplyr` package.  

```r

atp_pct_rounds_won_by_tournament_by_year <- full_join(atp_pct_rounds_won_by_tournament_by_year, 
                                                      atp_number_of_rounds_by_tournament,  
                                                      by = c("tourney_name", "tourney_date"))

```

After joining the above data frames to create `atp_pct_rounds_won_by_tournament_by_year`, I just need a few more steps to finish off constructing one of the data frames needed for my analysis.  

First, I used `mutate` to create a new column, `pct_of_rounds_won_in_tournament`.  At this point, `pct_of_rounds_won_in_tournament` contains the percentage of rounds a player advanced through for each tournament.

Next, I used `filter` to filter out all of the matches played at the ATP World Tour Finals Tournament.  Play at these tournaments occurs in a round-robin format, and thus doesn't match typical tournament style play.

Finally, I created `atp_pct_rounds_won_overall_by_player` by grouping `atp_pct_rounds_won_by_tournament_by_year` by the player's name, and using `summarize` and `mean` to determine the average percentage of rounds won in all tournaments, per player.

```r

# Calculating percent of rounds a player won in tournament, for each tournament
atp_pct_rounds_won_by_tournament_by_year <- atp_pct_rounds_won_by_tournament_by_year %>%
  mutate(pct_of_rounds_won_in_tournament = (number_round_of_tournament_advanced_through - index_of_first_round_in_tournament + 1)/
           number_rounds_in_tournament)

# Filtering out Tour Finals matches - since round robin play doesn't fit
atp_pct_rounds_won_by_tournament_by_year <- atp_pct_rounds_won_by_tournament_by_year %>% 
  filter(tourney_name != "Tour Finals")

# Calculating average pct of rounds in a tournament a player wins, for each player
atp_pct_rounds_won_overall_by_player <- atp_pct_rounds_won_by_tournament_by_year %>% 
  group_by(name) %>% 
  summarize(avg_pct_of_rounds_won_in_all_tournaments = mean(pct_of_rounds_won_in_tournament))
  
```

So far, I have determined a player's overall average percent of rounds won in a tournament.

To determine a player's average percent of rounds won in a tournament for an entire year is simply a small modification of the above.

To determine an average for a entire year, I need to be able to group my data by year, and to group by year, I first need a year variable.  Using `separate` from the `tidyr` package, I separated the `tourney_date` variable into three new variables: `year`, `month`, and `date`.  I assigned this modified data frame to `atp_pct_rounds_won_by_tournament_by_year_ymd_separated`.

Now with `year` as a separate variable, I was able to group by `year` and `name`, and them summarize to find the average percentage of rounds a player won in all tournaments for each year that player is in my data set.

```r

atp_pct_rounds_won_by_tournament_by_year_ymd_separated <- atp_pct_rounds_won_by_tournament_by_year %>% 
  separate(tourney_date, into = c("year", "month", "date"), sep = "-")

# Calculating average pct of rounds in a tournament a player wins, for each player, per year
atp_pct_rounds_won_by_year_by_player <- atp_pct_rounds_won_by_tournament_by_year_ymd_separated %>% 
  group_by(year, name) %>% 
  summarize(avg_pct_of_rounds_won_in_all_tournaments = mean(pct_of_rounds_won_in_tournament))

```

So far in this post, I have created two data frames.  These data frames are `atp_pct_rounds_won_overall_by_player` and `atp_pct_rounds_won_by_year_by_player`.  

These data frames are very similar to each other.  They each show how far a player advances through a tournament on average, as a percent.  The first data frame shows this as an average across all years a player is in the original data set.  

(INSERT TABLE OF DATA)

The second data frame shows this as an average for each year a player is in my data frame.  As such, `atp_pct_rounds_won_by_year_by_player` contains a row for each year a player is in the data set.

(INSERT TABLE OF DATA)

These above data frames will be joined with the data frames I'll construct in the second and third sections of this post.
  
  ## Calculating Player Stats per Year
  
In this section, I'll be describing how I constructed `atp_stats_by_player_by_year`.  This data frame, as the name describes, contains match statistics by year for each player in the original `atp` data frame.

Since I'll be grouping the data by year to determine a players stats for each year, I first need a year variable.  I used the `separate` function to split `tourney_date` into three new variables: `year`, `month`, and `day`.

```r  
  
atp_ymd_separated <- atp %>% separate(tourney_date, c("year", "month", "day"), sep = "-")

```

Similar to method in the previous section, I'm going to first create two separate data frames - one with statistics of match winners, and another with statistics of match losers.  I'm then going to join them together to find player statistics.

To get started, I created the data frame for the match winners first.  After grouping by both the `year` and `winner_id` variables, I used `summarize` to create a variety of new variables.  You can see all the variables below, but some examples are `double_faults_as_winner` and `games_served_as_winner`.

To be clear here, this creates a data frame containing information on total match statistics for all matches where a player won across the entire year.  For example, `ace_as_winner` is the total number of aces served for an entire year.  These statistics are not yet percentages.

```r
atp_by_year_grouped_by_winner_id <- atp_ymd_separated %>% 
  group_by(year, winner_id) %>% 
  summarize(height_as_winner = median(winner_height, na.rm = TRUE),
            avg_rank_as_winner = mean(winner_rank, na.rm = TRUE),
            number_matches_won = n(),
            age_as_winner = mean(winner_age, na.rm = TRUE),
            ace_as_winner = sum(w_ace, na.rm = TRUE),
            double_fault_as_winner = sum(w_df, na.rm = TRUE),
            service_pts_played_as_winner = sum(w_svpt, na.rm = TRUE),
            first_serves_in_as_winner = sum(w_1stIn, na.rm = TRUE),
            points_won_off_first_serve_as_winner = sum(w_1stWon, na.rm = TRUE),
            points_won_off_second_serve_as_winner = sum(w_2ndWon, na.rm = TRUE),
            games_served_as_winner = sum(w_SvGms, na.rm = TRUE),
            break_points_saved_as_winner = sum(w_bpSaved, na.rm = TRUE),
            break_points_faced_as_winner = sum(w_bpFaced, na.rm = TRUE),
            break_points_lost_as_winner = break_points_faced_as_winner - break_points_saved_as_winner,
            break_points_converted_on_defense_as_winner = sum(l_bpFaced - l_bpSaved, na.rm = TRUE),
            break_points_forced_on_defense_as_winner = sum(l_bpFaced, na.rm = TRUE),
            return_games_on_defense_as_winner = sum(l_SvGms, na.rm = TRUE),
            minutes_as_winner = sum(minutes, na.rm = TRUE))
```

Next, I created the same data frame, but for match losers.  After grouping by `year` and `loser_id`, I utilized `summarize` again to create analogous variables as above.

```r
atp_by_year_grouped_by_loser_id <- atp_ymd_separated %>% 
  group_by(year, loser_id) %>% 
  summarize(height_as_loser = median(loser_height, na.rm = TRUE),
            avg_rank_as_loser = mean(loser_rank, na.rm = TRUE),
            number_matches_lost = n(),
            age_as_loser = mean(loser_age, na.rm = TRUE),
            ace_as_loser = sum(l_ace, na.rm = TRUE),
            double_fault_as_loser = sum(l_df, na.rm = TRUE),
            service_pts_played_as_loser = sum(l_svpt, na.rm = TRUE),
            first_serves_in_as_loser = sum(l_1stIn, na.rm = TRUE),
            points_won_off_first_serve_as_loser = sum(l_1stWon, na.rm = TRUE),
            points_won_off_second_serve_as_loser = sum(l_2ndWon, na.rm = TRUE),
            games_served_as_loser = sum(l_SvGms, na.rm = TRUE),
            break_points_saved_as_loser = sum(l_bpSaved, na.rm = TRUE),
            break_points_faced_as_loser = sum(l_bpFaced, na.rm = TRUE),
            break_points_lost_as_loser = break_points_faced_as_loser - break_points_saved_as_loser,
            break_points_converted_on_defense_as_loser = sum(w_bpFaced - w_bpSaved, na.rm = TRUE),
            break_points_forced_on_defense_as_loser = sum(w_bpFaced, na.rm = TRUE),
            return_games_on_defense_as_loser = sum(w_SvGms, na.rm = TRUE),
            minutes_as_loser = sum(minutes, na.rm = TRUE))
```

So far I have created two data frames: `atp_by_year_grouped_by_winner_id` and `atp_by_year_grouped_by_loser_id`.  These data frames only contain the `winner_id` or `loser_id` (which is just an integer string), `year`, and the various stats I defined above.  Although a player's ID number is useful for grouping, it isn't really useful for gleening information from the data.  For that, we of course want player information, such as name and country.

To get that information, I created a new data frame `atp_player_info`.  To construct this data frame, I defined `player_info_columns` as a character string of the column names I was interested in.  Since no player went undefeated, I could use the loser information to get all the player information I wanted.  After defining what columns I wanted, I subsetted the `atp_ymd_separated` data frame and assigned this new data frame to `atp_player_info`.  Since this gave me multiple rows of the same information, I used `unique` to only get one row per player.

```r
# No player went undefeated, so using loser information as player information
player_info_columns <- c("loser_id", "loser_name", "loser_hand", "loser_ioc")
atp_player_info <- atp_ymd_separated[ , player_info_columns]
atp_player_info <- unique(atp_player_info)
```

The next few lines of code show how I used joined these three data frames into one, much more useful, data frame.

First I used `full_join` to join together the `atp_by_year_grouped_by_loser_id` and `atp_player_info`.  Since no player went undefeated, this creates a data frame containing information on every player in the orignal data set, as well as their total match statistics for all matches they lost.

```r
atp_by_year_grouped_by_loser_id <- full_join(atp_by_year_grouped_by_loser_id, 
                                            atp_player_info, by = "loser_id")
```

To prepare the match winner and match loser data frames to be joined together, I did a little cleaning and renamed some variables.  Of importance, I renamed both `winner_id` and `loser_id` to `player_id`.  

I then used `full_join` once more to join together `atp_by_year_grouped_by_winner_id` and `atp_by_year_grouped_by_loser_id` on the columns `year` and `player_id`.  I assigned the resulting data frame created to `atp_by_year_grouped_by_player_id`.

```r
setnames(atp_by_year_grouped_by_winner_id, old = "winner_id", new = "player_id")
setnames(atp_by_year_grouped_by_loser_id, 
         old = c("loser_id", "loser_name", "loser_hand", "loser_ioc"), 
         new = c("player_id", "name", "hand", "ioc"))



atp_by_year_grouped_by_player_id <- full_join(atp_by_year_grouped_by_winner_id, 
                                              atp_by_year_grouped_by_loser_id, by = c("year", "player_id"))
```

`atp_by_year_grouped_by_player_id` contains information for all players in the original data set, including their total match statistics per year.  After creating this data frame, I cleaned it up just a bit more by deleting the `height_as_winner` column and renaming the `height_as_loser` column to just `height`.  

Many players in the data had not won a single match, and since no player went undefeated, the `height_as_loser` variable contains more information. 

```r
# Deleting winner_as_height column, since loser_as_height is more expansive
atp_by_year_grouped_by_player_id <- subset(atp_by_year_grouped_by_player_id, select = -height_as_winner)

setnames(atp_by_year_grouped_by_player_id, old = "height_as_loser", new = "height")
```

As just mentioned, there where many players who had not won a single match in the entire data frame.  So when joining the match winners and match losers data frames, all of those players who only lost matches had no statistics for columns in the match winners data frame.  So in short, lots of `NA`'s where introduced.

To perform my later calculations, I needed to handle this situation.  Since these players did not win any matches, they really should have 0's for all the match winner statistics.  For example, a player with only losts, should have 0 aces as the match winner (since that player was never a match winner).

To fix this issue, I created a list, and assigned each match winner statistic to be 0.  I then used the very convenient `replace_na` function from the `tidyr` package to replace all the `NA` values in the winner columns with 0.  For each of the variables I defined to be 0, this function replaced any NA's in those columns with 0.  

```
winners_columns_replace_na_with_0 <- list(avg_rank_as_winner = 0,
                                          number_matches_won = 0,
                                          ace_as_winner = 0,
                                          double_fault_as_winner = 0,
                                          service_pts_played_as_winner = 0,
                                          first_serves_in_as_winner = 0,
                                          points_won_off_first_serve_as_winner = 0,
                                          points_won_off_second_serve_as_winner = 0,
                                          games_served_as_winner = 0,
                                          break_points_saved_as_winner = 0,
                                          break_points_faced_as_winner = 0,
                                          break_points_lost_as_winner = 0,
                                          break_points_converted_on_defense_as_winner = 0,
                                          break_points_forced_on_defense_as_winner = 0,
                                          return_games_on_defense_as_winner = 0,
                                          minutes_as_winner = 0)

atp_by_year_grouped_by_player_id <-  atp_by_year_grouped_by_player_id %>% 
  replace_na(winners_columns_replace_na_with_0)
```

The next steps in my construction involved some more data organization and cleaning.

The first thing I did was `mutate` to create a new variable `avg_rank`.  This variable, as it named, is the average world ranking of a player through the course of the year.  I previously defined `avg_rank_as_winner` and `avg_rank_as_loser` to be the average world ranking of a player for the matches that player won or lost.  So `avg_rank` is the average of these two averages.

```r
# Creating a new column for average rank a player had throughout the year,
# Dropping unneeded columns
atp_by_year_grouped_by_player_id <- atp_by_year_grouped_by_player_id %>% 
  mutate(avg_rank = round((avg_rank_as_winner + avg_rank_as_loser)/2, digits = 2)) %>% 
  subset(select = -c(avg_rank_as_winner, avg_rank_as_loser))
```

Next I mutated the data frame again to create another new variable, `age`.  I used the `ifelse` function inside `mutate` to create this new variable.  If a player has an age as both a winner and loser, then I defined age to be the average of these values.  Otherwise, if the player has `NA` for `age_as_winner`, I defined age to just be `age_as_loser`.  In both instances, I used `round` to round the players age to the nearest integer.

Of note here, I was able to defined `age` partially using the `is.na` function because just above, I did not defined age_as_winner in the `winners_columns_replace_na_with_0` list.

```r
atp_by_year_grouped_by_player_id <- atp_by_year_grouped_by_player_id %>% 
  mutate(age = ifelse(!is.na(age_as_winner), round((age_as_winner + age_as_loser)/2), round(age_as_loser))) %>% 
  subset(select = -c(age_as_winner, age_as_loser))
```

To finish organizing `atp_by_year_grouped_by_player_id`, I used `select` to reorder the columns into a more appropriate order.  I also defined the `hand` variable to be a factor.

```r
# Reorder columns into more appropriate order
atp_by_year_grouped_by_player_id <- atp_by_year_grouped_by_player_id %>% 
  select(year,
         player_id, 
         name,
         ioc, 
         age, 
         hand, 
         avg_rank,
         height,
         number_matches_won,
         ace_as_winner:return_games_on_defense_as_winner,
         number_matches_lost,
         ace_as_loser:return_games_on_defense_as_loser,
         minutes_as_winner,
         minutes_as_loser)

atp_by_year_grouped_by_player_id$hand <- as.factor(atp_by_year_grouped_by_player_id$hand)
```

So far, I have constructed `atp_by_year_grouped_by_player_id`.  This data frame contains information on players, as well as various match statitics separated into match winner columns and match loser columns, for each year a player was in the original data set.

You can see the structure of the data frame below:

```r
> glimpse(atp_by_year_grouped_by_player_id)
Observations: 3,124
Variables: 38
$ year                                        <chr> "2006", "2006", "2006", "2006", "2006", "2006", "2006", "2006", "20...
$ player_id                                   <int> 101736, 101868, 101885, 101962, 102035, 102106, 102148, 102179, 102...
$ name                                        <chr> "Andre Agassi", "Dick Norman", "Wayne Arthurs", "Younes El Aynaoui"...
$ ioc                                         <chr> "USA", "BEL", "AUS", "MAR", "SWE", "ITA", "FRA", "FRA", "DEN", "ESP...
$ age                                         <dbl> 36, 35, 35, 34, 34, 34, 33, 33, 33, 33, 33, 32, 32, 33, 32, 32, 32,...
$ hand                                        <fct> Right, Left, Left, Right, Right, Right, Right, Right, Left, Right, ...
$ avg_rank                                    <dbl> 18.69, 106.61, 151.95, 223.75, 60.63, 59.11, 49.84, 160.00, 102.89,...
$ height                                      <dbl> 180, 203, 190, 193, 183, 188, 178, 185, 190, 190, 193, 185, 188, 17...
$ number_matches_won                          <dbl> 10, 3, 6, 2, 13, 11, 18, 1, 14, 2, 6, 1, 8, 1, 12, 16, 31, 4, 4, 11...
$ ace_as_winner                               <dbl> 104, 40, 107, 12, 73, 83, 73, 9, 114, 16, 52, 13, 48, 2, 47, 52, 16...
$ double_fault_as_winner                      <dbl> 26, 19, 26, 2, 42, 19, 30, 4, 22, 3, 25, 4, 27, 0, 16, 33, 57, 19, ...
$ service_pts_played_as_winner                <dbl> 968, 319, 461, 109, 1215, 1035, 1469, 93, 1037, 164, 462, 78, 649, ...
$ first_serves_in_as_winner                   <dbl> 588, 208, 268, 66, 770, 590, 868, 58, 654, 106, 271, 46, 359, 64, 5...
$ points_won_off_first_serve_as_winner        <dbl> 446, 159, 227, 53, 565, 450, 621, 43, 513, 82, 206, 36, 272, 39, 42...
$ points_won_off_second_serve_as_winner       <dbl> 208, 45, 109, 25, 232, 236, 321, 20, 212, 24, 99, 19, 138, 22, 184,...
$ games_served_as_winner                      <dbl> 151, 49, 81, 20, 195, 157, 226, 14, 173, 25, 71, 12, 101, 13, 142, ...
$ break_points_saved_as_winner                <dbl> 38, 23, 14, 3, 41, 54, 77, 6, 42, 7, 24, 4, 38, 3, 41, 79, 92, 9, 6...
$ break_points_faced_as_winner                <dbl> 57, 32, 22, 5, 78, 81, 114, 8, 59, 12, 34, 5, 57, 5, 59, 108, 131, ...
$ break_points_lost_as_winner                 <dbl> 19, 9, 8, 2, 37, 27, 37, 2, 17, 5, 10, 1, 19, 2, 18, 29, 39, 14, 3,...
$ break_points_converted_on_defense_as_winner <dbl> 43, 14, 14, 6, 66, 48, 80, 3, 45, 8, 23, 2, 34, 4, 43, 79, 113, 19,...
$ break_points_forced_on_defense_as_winner    <dbl> 99, 31, 37, 15, 139, 108, 169, 4, 101, 21, 47, 15, 75, 7, 105, 174,...
$ return_games_on_defense_as_winner           <dbl> 152, 47, 77, 20, 192, 156, 219, 14, 170, 24, 68, 12, 103, 14, 144, ...
$ number_matches_lost                         <int> 8, 9, 10, 1, 17, 24, 23, 2, 17, 3, 14, 2, 12, 2, 20, 26, 20, 11, 12...
$ ace_as_loser                                <int> 58, 92, 201, 5, 56, 85, 102, 14, 112, 22, 65, 16, 51, 2, 68, 90, 79...
$ double_fault_as_loser                       <int> 29, 62, 50, 4, 63, 61, 76, 1, 43, 4, 64, 15, 39, 3, 42, 92, 62, 45,...
$ service_pts_played_as_loser                 <int> 687, 941, 923, 65, 1253, 1868, 2045, 148, 1290, 290, 1049, 154, 856...
$ first_serves_in_as_loser                    <int> 390, 575, 517, 31, 723, 1064, 1162, 89, 776, 169, 649, 94, 437, 94,...
$ points_won_off_first_serve_as_loser         <int> 256, 381, 412, 23, 480, 697, 759, 64, 544, 115, 412, 73, 292, 66, 6...
$ points_won_off_second_serve_as_loser        <int> 154, 165, 213, 14, 226, 378, 395, 30, 239, 76, 177, 24, 177, 28, 28...
$ games_served_as_loser                       <int> 104, 132, 146, 10, 203, 281, 302, 22, 200, 43, 154, 23, 129, 23, 24...
$ break_points_saved_as_loser                 <int> 43, 72, 38, 2, 59, 104, 136, 5, 74, 20, 52, 6, 63, 9, 88, 164, 112,...
$ break_points_faced_as_loser                 <int> 71, 108, 60, 5, 134, 195, 239, 10, 126, 26, 104, 12, 116, 15, 172, ...
$ break_points_lost_as_loser                  <int> 28, 36, 22, 3, 75, 91, 103, 5, 52, 6, 52, 6, 53, 6, 84, 104, 83, 48...
$ break_points_converted_on_defense_as_loser  <int> 9, 15, 5, 1, 25, 34, 49, 2, 19, 4, 11, 0, 17, 0, 34, 52, 38, 19, 27...
$ break_points_forced_on_defense_as_loser     <int> 43, 46, 28, 10, 77, 113, 128, 11, 77, 20, 42, 8, 47, 0, 120, 177, 1...
$ return_games_on_defense_as_loser            <int> 104, 133, 147, 10, 201, 290, 297, 22, 204, 42, 155, 24, 132, 24, 25...
$ minutes_as_winner                           <dbl> 1253, 393, 575, 158, 1649, 1311, 1970, 107, 1468, 221, 598, 68, 924...
$ minutes_as_loser                            <int> 806, 1049, 1117, 97, 1701, 2322, 2630, 182, 1648, 430, 1219, 185, 1...
```

As you can see, the above data frame contains integer values spread across `_as_winner` variables and `_as_loser` variables.  Clearly, just having counts isn't particular useful, so in the remaining part of this section I'll be describing how I created the `atp_stats_by_player_by_year` data frame, which contains match statistics as percentages.

As a first step, I needed to combine all of those amounts spread across winner and loser variables into a single value.  Below, I mutated the data frame to create a variety of new variables.  In most cases, I just summed across the winner and loser columns.  Examples of columns created are `total_service_points` and `total_return_games_on_defense`.

```
# Mutating to create various player totals per year
atp_by_year_grouped_by_player_id <- atp_by_year_grouped_by_player_id %>% 
  mutate(total_matches_won = number_matches_won,
         total_matches_lost = number_matches_lost,
         total_matches_played = total_matches_won + total_matches_lost,
         total_games_served = games_served_as_winner + games_served_as_loser,
         total_aces = ace_as_winner + ace_as_loser,
         total_double_fault = double_fault_as_winner + double_fault_as_loser,
         total_service_points = service_pts_played_as_winner + service_pts_played_as_loser,
         total_first_serves_in = first_serves_in_as_winner + first_serves_in_as_loser,
         total_second_serves_in = total_service_points -total_first_serves_in - total_double_fault,
         total_serves = total_first_serves_in + 2*(total_second_serves_in + total_double_fault),
         total_points_won_off_first_serve = points_won_off_first_serve_as_winner + points_won_off_first_serve_as_loser,
         total_points_won_off_second_serve = points_won_off_second_serve_as_winner + points_won_off_second_serve_as_loser,
         total_break_points_faced = break_points_faced_as_winner + break_points_faced_as_loser,
         total_break_points_saved = break_points_saved_as_winner + break_points_saved_as_loser,
         total_break_points_lost = break_points_lost_as_winner + break_points_lost_as_loser,
         total_break_points_converted_on_defense = break_points_converted_on_defense_as_winner + 
           break_points_converted_on_defense_as_loser,
         total_break_points_forced_on_defense = break_points_forced_on_defense_as_winner +
           break_points_forced_on_defense_as_loser,
         total_return_games_on_defense = return_games_on_defense_as_winner + return_games_on_defense_as_loser,
         total_games_played = total_games_served + total_return_games_on_defense,
         total_minutes = minutes_as_winner + minutes_as_loser)
```

After creating these new columns, I subsetted the data set to create the `atp_stats_by_player_by_year` data frame.  I selected the player information columns, and the newly created columns above to be in this new data frame.  To make subsetting easier, I used the convenient `starts_with` function from `dplyr` to select all the columns that start with "total".

```r
# Subsetting to get data frame of just year totals
atp_stats_by_player_by_year <- atp_by_year_grouped_by_player_id %>% 
  select(year:height, starts_with("total"))
```

And finally, at this point, I was able to calculate player statistics as percentages for entire years.  To do this, I used `mutate` once more to create all of these new statistics from previously defined columns.  Most of these statistics are pretty standard in tennis, such as `pct_service_games_won` and `pct_break_points_converted_on_defense`.  But I defined a couple that I hadn't see to much before, such as `pct_ace_per_service_point` and `avg_minutes_per_game`.

```r
# Mutating to add columns for various player statistics, by year
atp_stats_by_player_by_year <- atp_stats_by_player_by_year %>% 
  mutate(pct_matches_won = total_matches_won/total_matches_played,
         pct_service_games_won = (total_games_served - total_break_points_lost)/
           total_games_served,
         pct_ace_per_service_point = total_aces/total_service_points,
         pct_ace_per_serve = total_aces/total_serves,                  
         pct_first_serves_in = total_first_serves_in/total_service_points,
         pct_second_serves_in = total_second_serves_in/
           (total_service_points - total_first_serves_in),
         pct_double_fault = total_double_fault/total_service_points,
         pct_points_won_off_first_serve = total_points_won_off_first_serve/
           total_first_serves_in,
         pct_points_won_off_second_serve = total_points_won_off_second_serve/
           (total_service_points - total_first_serves_in),
         pct_break_points_saved = total_break_points_saved/total_break_points_faced,
         pct_break_points_lost = total_break_points_lost/total_break_points_faced,
         pct_break_points_converted_on_defense = total_break_points_converted_on_defense/
           total_break_points_forced_on_defense,
         pct_return_games_won_on_defense = total_break_points_converted_on_defense/
           total_return_games_on_defense,
         avg_minutes_per_game = total_minutes/total_games_played)
``` 

To finish creating the `atp_stats_by_player_by_year` data frame, I joined the data frame with the `atp_pct_rounds_won_by_year_by_player` data frame created in the first section of this post.

```r
# Joining atp_stats_by_player_by_year with atp_player_pct_rounds_won_by_year_by_player
atp_stats_by_player_by_year <- atp_stats_by_player_by_year %>% 
                                  full_join(atp_pct_rounds_won_by_year_by_player, 
                                            by = c("year", "name"))
```

For clarity, below is the struture of the `atp_stats_by_player_by_year`.  It contains various player statistics, by year.  In subsequent posts, I'll use this data frame to answer some interesting questions, such as do left handed players have an advantage, and what separates the great players from everyone else.

```r
> glimpse(atp_stats_by_player_by_year)
Observations: 3,127
Variables: 43
$ year                                     <chr> "2006", "2006", "2006", "2006", "2006", "2006", "2006", "2006", "2006"...
$ player_id                                <int> 101736, 101868, 101885, 101962, 102035, 102106, 102148, 102179, 102202...
$ name                                     <chr> "Andre Agassi", "Dick Norman", "Wayne Arthurs", "Younes El Aynaoui", "...
$ ioc                                      <chr> "USA", "BEL", "AUS", "MAR", "SWE", "ITA", "FRA", "FRA", "DEN", "ESP", ...
$ age                                      <dbl> 36, 35, 35, 34, 34, 34, 33, 33, 33, 33, 33, 32, 32, 33, 32, 32, 32, 31...
$ hand                                     <fct> Right, Left, Left, Right, Right, Right, Right, Right, Left, Right, Lef...
$ avg_rank                                 <dbl> 18.69, 106.61, 151.95, 223.75, 60.63, 59.11, 49.84, 160.00, 102.89, 19...
$ height                                   <dbl> 180, 203, 190, 193, 183, 188, 178, 185, 190, 190, 193, 185, 188, 173, ...
$ total_matches_won                        <dbl> 10, 3, 6, 2, 13, 11, 18, 1, 14, 2, 6, 1, 8, 1, 12, 16, 31, 4, 4, 11, 9...
$ total_matches_lost                       <int> 8, 9, 10, 1, 17, 24, 23, 2, 17, 3, 14, 2, 12, 2, 20, 26, 20, 11, 12, 1...
$ total_matches_played                     <dbl> 18, 12, 16, 3, 30, 35, 41, 3, 31, 5, 20, 3, 20, 3, 32, 42, 51, 15, 16,...
$ total_games_served                       <dbl> 255, 181, 227, 30, 398, 438, 528, 36, 373, 68, 225, 35, 230, 36, 389, ...
$ total_aces                               <dbl> 162, 132, 308, 17, 129, 168, 175, 23, 226, 38, 117, 29, 99, 4, 115, 14...
$ total_double_fault                       <dbl> 55, 81, 76, 6, 105, 80, 106, 5, 65, 7, 89, 19, 66, 3, 58, 125, 119, 64...
$ total_service_points                     <dbl> 1655, 1260, 1384, 174, 2468, 2903, 3514, 241, 2327, 454, 1511, 232, 15...
$ total_first_serves_in                    <dbl> 978, 783, 785, 97, 1493, 1654, 2030, 147, 1430, 275, 920, 140, 796, 15...
$ total_second_serves_in                   <dbl> 622, 396, 523, 71, 870, 1169, 1378, 89, 832, 172, 502, 73, 643, 92, 87...
$ total_serves                             <dbl> 2332, 1737, 1983, 251, 3443, 4152, 4998, 335, 3224, 633, 2102, 324, 22...
$ total_points_won_off_first_serve         <dbl> 702, 540, 639, 76, 1045, 1147, 1380, 107, 1057, 197, 618, 109, 564, 10...
$ total_points_won_off_second_serve        <dbl> 362, 210, 322, 39, 458, 614, 716, 50, 451, 100, 276, 43, 315, 50, 470,...
$ total_break_points_faced                 <dbl> 128, 140, 82, 10, 212, 276, 353, 18, 185, 38, 138, 17, 173, 20, 231, 3...
$ total_break_points_saved                 <dbl> 81, 95, 52, 5, 100, 158, 213, 11, 116, 27, 76, 10, 101, 12, 129, 243, ...
$ total_break_points_lost                  <dbl> 47, 45, 30, 5, 112, 118, 140, 7, 69, 11, 62, 7, 72, 8, 102, 133, 122, ...
$ total_break_points_converted_on_defense  <dbl> 52, 29, 19, 7, 91, 82, 129, 5, 64, 12, 34, 2, 51, 4, 77, 131, 151, 38,...
$ total_break_points_forced_on_defense     <dbl> 142, 77, 65, 25, 216, 221, 297, 15, 178, 41, 89, 23, 122, 7, 225, 351,...
$ total_return_games_on_defense            <dbl> 256, 180, 224, 30, 393, 446, 516, 36, 374, 66, 223, 36, 235, 38, 394, ...
$ total_games_played                       <dbl> 511, 361, 451, 60, 791, 884, 1044, 72, 747, 134, 448, 71, 465, 74, 783...
$ total_minutes                            <dbl> 2059, 1442, 1692, 255, 3350, 3633, 4600, 289, 3116, 651, 1817, 253, 20...
$ pct_matches_won                          <dbl> 0.5555556, 0.2500000, 0.3750000, 0.6666667, 0.4333333, 0.3142857, 0.43...
$ pct_service_games_won                    <dbl> 0.8156863, 0.7513812, 0.8678414, 0.8333333, 0.7185930, 0.7305936, 0.73...
$ pct_ace_per_service_point                <dbl> 0.09788520, 0.10476190, 0.22254335, 0.09770115, 0.05226904, 0.05787117...
$ pct_ace_per_serve                        <dbl> 0.06946827, 0.07599309, 0.15532022, 0.06772908, 0.03746733, 0.04046243...
$ pct_first_serves_in                      <dbl> 0.5909366, 0.6214286, 0.5671965, 0.5574713, 0.6049433, 0.5697554, 0.57...
$ pct_second_serves_in                     <dbl> 0.9187592, 0.8301887, 0.8731219, 0.9220779, 0.8923077, 0.9359488, 0.92...
$ pct_double_fault                         <dbl> 0.03323263, 0.06428571, 0.05491329, 0.03448276, 0.04254457, 0.02755770...
$ pct_points_won_off_first_serve           <dbl> 0.7177914, 0.6896552, 0.8140127, 0.7835052, 0.6999330, 0.6934704, 0.67...
$ pct_points_won_off_second_serve          <dbl> 0.5347120, 0.4402516, 0.5375626, 0.5064935, 0.4697436, 0.4915933, 0.48...
$ pct_break_points_saved                   <dbl> 0.6328125, 0.6785714, 0.6341463, 0.5000000, 0.4716981, 0.5724638, 0.60...
$ pct_break_points_lost                    <dbl> 0.3671875, 0.3214286, 0.3658537, 0.5000000, 0.5283019, 0.4275362, 0.39...
$ pct_break_points_converted_on_defense    <dbl> 0.36619718, 0.37662338, 0.29230769, 0.28000000, 0.42129630, 0.37104072...
$ pct_return_games_won_on_defense          <dbl> 0.20312500, 0.16111111, 0.08482143, 0.23333333, 0.23155216, 0.18385650...
$ avg_minutes_per_game                     <dbl> 4.029354, 3.994460, 3.751663, 4.250000, 4.235145, 4.109729, 4.406130, ...
$ avg_pct_of_rounds_won_in_all_tournaments <dbl> 0.26530612, 0.05396825, 0.11666667, 0.20000000, 0.12941176, 0.10297619...
```
  ## Calculating Player Stats Overall
  
In the final main section of this post, I'll be detailing how I constructed the Creating `atp_stats_overall_by_player` data frame.  This data frame is extremely similar to the `atp_stats_by_player_by_year` data frame created in the previous section, except that this new data frame will contain ovearll player statistics.  In other words, each row will contain a player's averaged match stats for all years that player was in the original data frame.
  
My methods used to create the below data frame are almost identical to the methods used above.  In short, I created a data frame for all the match winners, followed by all the match losers, and then joined these data frames together.  After creating this intermediary data frame and performing a little data cleaning, I used `mutate` to determine total counts of match statistics for each player, such as `total_matches_won`.  

Finally, from these total counts, I used `mutute` once more to construct overall match percentages for each individual player.  The statistics calculated are identical to those calculated in the second section.

The main difference in constructing `atp_stats_overall_by_player` from `atp_stats_by_player_by_year` was grouping only by the player ID variable, and not both the player ID variable and `year`.

For this reasoning and to eliminate redundencies, my explanations for this section will be sparse.  For more indepth explanations of my thinking and reasoning, refer to the analogous section of the code from the second section. 

The first chunk of code below is constructing the match winner and match loser data frames.

```r
atp_grouped_by_winner_id <- atp %>% 
  group_by(winner_id) %>% 
  summarize(height_as_winner = median(winner_height, na.rm = TRUE),
            avg_rank_as_winner = mean(winner_rank, na.rm = TRUE),
            number_matches_won = n(),
            age_as_winner = mean(winner_age, na.rm = TRUE),
            ace_as_winner = sum(w_ace, na.rm = TRUE),
            double_fault_as_winner = sum(w_df, na.rm = TRUE),
            service_pts_played_as_winner = sum(w_svpt, na.rm = TRUE),
            first_serves_in_as_winner = sum(w_1stIn, na.rm = TRUE),
            points_won_off_first_serve_as_winner = sum(w_1stWon, na.rm = TRUE),
            points_won_off_second_serve_as_winner = sum(w_2ndWon, na.rm = TRUE),
            games_served_as_winner = sum(w_SvGms, na.rm = TRUE),
            break_points_saved_as_winner = sum(w_bpSaved, na.rm = TRUE),
            break_points_faced_as_winner = sum(w_bpFaced, na.rm = TRUE),
            break_points_lost_as_winner = break_points_faced_as_winner - break_points_saved_as_winner,
            break_points_converted_on_defense_as_winner = sum(l_bpFaced - l_bpSaved, na.rm = TRUE),
            break_points_forced_on_defense_as_winner = sum(l_bpFaced, na.rm = TRUE),
            return_games_on_defense_as_winner = sum(l_SvGms, na.rm = TRUE),
            minutes_as_winner = sum(minutes, na.rm = TRUE))



atp_grouped_by_loser_id <- atp %>% 
  group_by(loser_id) %>% 
  summarize(height_as_loser = median(loser_height, na.rm = TRUE),
            avg_rank_as_loser = mean(loser_rank, na.rm = TRUE),
            number_matches_lost = n(),
            age_as_loser = mean(loser_age, na.rm = TRUE),
            ace_as_loser = sum(l_ace, na.rm = TRUE),
            double_fault_as_loser = sum(l_df, na.rm = TRUE),
            service_pts_played_as_loser = sum(l_svpt, na.rm = TRUE),
            first_serves_in_as_loser = sum(l_1stIn, na.rm = TRUE),
            points_won_off_first_serve_as_loser = sum(l_1stWon, na.rm = TRUE),
            points_won_off_second_serve_as_loser = sum(l_2ndWon, na.rm = TRUE),
            games_served_as_loser = sum(l_SvGms, na.rm = TRUE),
            break_points_saved_as_loser = sum(l_bpSaved, na.rm = TRUE),
            break_points_faced_as_loser = sum(l_bpFaced, na.rm = TRUE),
            break_points_lost_as_loser = break_points_faced_as_loser - break_points_saved_as_loser,
            break_points_converted_on_defense_as_loser = sum(w_bpFaced - w_bpSaved, na.rm = TRUE),
            break_points_forced_on_defense_as_loser = sum(w_bpFaced, na.rm = TRUE),
            return_games_on_defense_as_loser = sum(w_SvGms, na.rm = TRUE),
            minutes_as_loser = sum(minutes, na.rm = TRUE))
```

The next bit of code below is constructing a data frame that holds player information.

```r
# No player went undefeated, so using loser information as player information
player_info_columns <- c("loser_id", "loser_name", "loser_hand", "loser_ioc")
atp_player_info <- atp[ , player_info_columns]
atp_player_info <- unique(atp_player_info)
```

The below code is joinig the match loser and player information data frames together, on the `loser_id` variable.  Then I used the `setnames` function to change some volumn names, and lastly joined the match winner and match loser data frames together to create `atp_grouped_by_player_id`.

```
atp_grouped_by_loser_id <- full_join(atp_grouped_by_loser_id, atp_player_info, by = "loser_id")

setnames(atp_grouped_by_winner_id, old = "winner_id", new = "player_id")
setnames(atp_grouped_by_loser_id, 
         old = c("loser_id", "loser_name", "loser_hand", "loser_ioc"), 
         new = c("player_id", "name", "hand", "ioc"))



atp_grouped_by_player_id <- full_join(atp_grouped_by_winner_id, atp_grouped_by_loser_id, by = "player_id")
```

Below is a little data cleaning to organize player height's more clearly.

```r
# Deleting winner_as_height column, since loser_as_height is more expansive
atp_grouped_by_player_id <- subset(atp_grouped_by_player_id, select = -height_as_winner)

setnames(atp_grouped_by_player_id, old = "height_as_loser", new = "height")
```

When joining the match winner and match loser data frames, lots of `NA`'s where introduced for players who only lost matches.  This problem is being handled below.

```r
winners_columns_replace_na_with_0 <- list(avg_rank_as_winner = 0,
                                          number_matches_won = 0,
                                          ace_as_winner = 0,
                                          double_fault_as_winner = 0,
                                          service_pts_played_as_winner = 0,
                                          first_serves_in_as_winner = 0,
                                          points_won_off_first_serve_as_winner = 0,
                                          points_won_off_second_serve_as_winner = 0,
                                          games_served_as_winner = 0,
                                          break_points_saved_as_winner = 0,
                                          break_points_faced_as_winner = 0,
                                          break_points_lost_as_winner = 0,
                                          break_points_converted_on_defense_as_winner = 0,
                                          break_points_forced_on_defense_as_winner = 0,
                                          return_games_on_defense_as_winner = 0,
                                          minutes_as_winner = 0)

atp_grouped_by_player_id <-  atp_grouped_by_player_id %>% replace_na(winners_columns_replace_na_with_0)
```

Constructing the `avg_rank` and `age` variables.

```r
atp_grouped_by_player_id <- atp_grouped_by_player_id %>% 
  mutate(avg_rank = round((avg_rank_as_winner + avg_rank_as_loser)/2, digits = 2)) %>% 
  subset(select = -c(avg_rank_as_winner, avg_rank_as_loser))



atp_grouped_by_player_id <- atp_grouped_by_player_id %>% 
  mutate(age = ifelse(!is.na(age_as_winner), round((age_as_winner + age_as_loser)/2), round(age_as_loser))) %>% 
  subset(select = -c(age_as_winner, age_as_loser))
```

Here I am reordering columns into a more appropriate order, and also defining the `hand` variable to be a factor.

```r
atp_grouped_by_player_id <- atp_grouped_by_player_id %>% select(player_id, 
                                                                name,
                                                                ioc, 
                                                                age, 
                                                                hand, 
                                                                avg_rank,
                                                                height,
                                                                number_matches_won,
                                                                ace_as_winner:return_games_on_defense_as_winner,
                                                                number_matches_lost,
                                                                ace_as_loser:return_games_on_defense_as_loser,
                                                                minutes_as_winner,
                                                                minutes_as_loser)

atp_grouped_by_player_id$hand <- as.factor(atp_grouped_by_player_id$hand)
```

Below I am using `mutate` to find the total counts of various statistics for each player.

```r
atp_grouped_by_player_id <- atp_grouped_by_player_id %>% 
  mutate(total_matches_won = number_matches_won,
         total_matches_lost = number_matches_lost,
         total_matches_played = total_matches_won + total_matches_lost,
         total_games_served = games_served_as_winner + games_served_as_loser,
         total_aces = ace_as_winner + ace_as_loser,
         total_double_fault = double_fault_as_winner + double_fault_as_loser,
         total_service_points = service_pts_played_as_winner + service_pts_played_as_loser,
         total_first_serves_in = first_serves_in_as_winner + first_serves_in_as_loser,
         total_second_serves_in = total_service_points -total_first_serves_in - total_double_fault,
         total_serves = total_first_serves_in + 2*(total_second_serves_in + total_double_fault),
         total_points_won_off_first_serve = points_won_off_first_serve_as_winner + points_won_off_first_serve_as_loser,
         total_points_won_off_second_serve = points_won_off_second_serve_as_winner + points_won_off_second_serve_as_loser,
         total_break_points_faced = break_points_faced_as_winner + break_points_faced_as_loser,
         total_break_points_saved = break_points_saved_as_winner + break_points_saved_as_loser,
         total_break_points_lost = break_points_lost_as_winner + break_points_lost_as_loser,
         total_break_points_converted_on_defense = break_points_converted_on_defense_as_winner + 
           break_points_converted_on_defense_as_loser,
         total_break_points_forced_on_defense = break_points_forced_on_defense_as_winner +
           break_points_forced_on_defense_as_loser,
         total_return_games_on_defense = return_games_on_defense_as_winner + return_games_on_defense_as_loser,
         total_games_played = total_games_served + total_return_games_on_defense,
         total_minutes = minutes_as_winner + minutes_as_loser)
```

Subsetting the `atp_grouped_by_player_id` data frame to get just the player information columns and the overall total statistics columns.  I assigned the resulting data frame to `atp_stats_overall_by_player`.

```r
# Subsetting to get data frame of just overall totals
atp_stats_overall_by_player <- atp_grouped_by_player_id %>% select(player_id:height, starts_with("total"))
````

Finally, I am using `mutate` once more to calculate a players overall statistics in terms of percentages.  You can see below how each percentage was calculated.

```r
# Mutating to add columns for various overall player stats
atp_stats_overall_by_player <- atp_stats_overall_by_player %>% 
                     mutate(pct_matches_won = total_matches_won/total_matches_played,
                            pct_service_games_won = (total_games_served - total_break_points_lost)/
                              total_games_served,
                            pct_ace_per_service_point = total_aces/total_service_points,
                            pct_ace_per_serve = total_aces/total_serves,                  
                            pct_first_serves_in = total_first_serves_in/total_service_points,
                            pct_second_serves_in = total_second_serves_in/
                              (total_service_points - total_first_serves_in),
                            pct_double_fault = total_double_fault/total_service_points,
                            pct_points_won_off_first_serve = total_points_won_off_first_serve/
                              total_first_serves_in,
                            pct_points_won_off_second_serve = total_points_won_off_second_serve/
                              (total_service_points - total_first_serves_in),
                            pct_break_points_saved = total_break_points_saved/total_break_points_faced,
                            pct_break_points_lost = total_break_points_lost/total_break_points_faced,
                            pct_break_points_converted_on_defense = total_break_points_converted_on_defense/
                              total_break_points_forced_on_defense,
                            pct_return_games_won_on_defense = total_break_points_converted_on_defense/
                              total_return_games_on_defense,
                            avg_minutes_per_game = total_minutes/total_games_played)
```

And lastly, I am joining the `atp_stats_overall_by_player` data frame with the `atp_pct_rounds_won_overall_by_player` data frame created in the first section of this post.  At this point, the `atp_stats_overall_by_player` data frame is complete.  It contains information on overall player statistics for all years that player was in the original data set.

```r
atp_stats_overall_by_player <- atp_stats_overall_by_player %>% 
                                  full_join(atp_pct_rounds_won_overall_by_player, 
                                            by = "name")                  
```

For clarity and completeness, I've provided the structure of the `atp_stats_overall_by_player` data frame below.

```r
> glimpse(atp_stats_overall_by_player)
Observations: 832
Variables: 42
$ player_id                                <int> 100644, 101736, 101868, 101885, 101962, 102035, 102106, 102148, 102179...
$ name                                     <chr> "Alexander Zverev", "Andre Agassi", "Dick Norman", "Wayne Arthurs", "Y...
$ ioc                                      <chr> "GER", "USA", "BEL", "AUS", "MAR", "SWE", "ITA", "FRA", "FRA", "DEN", ...
$ age                                      <dbl> 18, 36, 36, 35, 36, 35, 34, 35, 34, 33, 33, 33, 32, 33, 33, 33, 33, 33...
$ hand                                     <fct> Right, Right, Left, Left, Right, Right, Right, Right, Right, Left, Rig...
$ avg_rank                                 <dbl> 159.26, 18.69, 134.59, 160.78, 222.29, 64.12, 71.16, 50.10, 252.50, 10...
$ height                                   <dbl> NA, 180, 203, 190, 193, 183, 188, 178, 185, 190, 190, 193, 185, 188, 1...
$ total_matches_won                        <dbl> 18, 10, 7, 10, 9, 37, 16, 72, 5, 14, 2, 6, 1, 9, 1, 24, 7, 54, 35, 1, ...
$ total_matches_lost                       <int> 24, 8, 11, 11, 6, 49, 29, 78, 4, 18, 3, 14, 2, 14, 2, 40, 23, 82, 32, ...
$ total_matches_played                     <dbl> 42, 18, 18, 21, 15, 86, 45, 150, 9, 32, 5, 20, 3, 23, 3, 64, 30, 136, ...
$ total_games_served                       <dbl> 518, 255, 260, 327, 173, 1126, 553, 1847, 104, 383, 68, 225, 35, 258, ...
$ total_aces                               <dbl> 243, 162, 203, 418, 110, 365, 228, 666, 72, 239, 38, 117, 29, 111, 4, ...
$ total_double_fault                       <dbl> 180, 55, 123, 109, 35, 271, 99, 283, 11, 66, 7, 89, 19, 72, 3, 129, 10...
$ total_service_points                     <dbl> 3355, 1655, 1726, 1985, 1091, 7324, 3658, 11955, 689, 2386, 454, 1511,...
$ total_first_serves_in                    <dbl> 1997, 978, 1067, 1153, 692, 4432, 2109, 6857, 455, 1475, 275, 920, 140...
$ total_second_serves_in                   <dbl> 1178, 622, 536, 723, 364, 2621, 1450, 4815, 223, 845, 172, 502, 73, 71...
$ total_serves                             <dbl> 4713, 2332, 2385, 2817, 1490, 10216, 5207, 17053, 923, 3297, 633, 2102...
$ total_points_won_off_first_serve         <dbl> 1398, 702, 771, 934, 523, 3064, 1463, 4763, 334, 1093, 197, 618, 109, ...
$ total_points_won_off_second_serve        <dbl> 645, 362, 316, 445, 192, 1365, 759, 2568, 131, 458, 100, 276, 43, 352,...
$ total_break_points_faced                 <dbl> 286, 128, 163, 115, 76, 707, 342, 1139, 39, 186, 38, 138, 17, 188, 20,...
$ total_break_points_saved                 <dbl> 158, 81, 111, 71, 45, 400, 197, 676, 25, 116, 27, 76, 10, 107, 12, 255...
$ total_break_points_lost                  <dbl> 128, 47, 52, 44, 31, 307, 145, 463, 14, 70, 11, 62, 7, 81, 8, 205, 99,...
$ total_break_points_converted_on_defense  <dbl> 103, 52, 38, 37, 31, 254, 106, 428, 15, 64, 12, 34, 2, 59, 4, 159, 61,...
$ total_break_points_forced_on_defense     <dbl> 256, 142, 101, 110, 79, 616, 286, 1001, 48, 180, 41, 89, 23, 136, 7, 4...
$ total_return_games_on_defense            <dbl> 521, 256, 258, 322, 173, 1102, 564, 1828, 106, 385, 66, 223, 36, 263, ...
$ total_games_played                       <dbl> 1039, 511, 518, 649, 346, 2228, 1117, 3675, 210, 768, 134, 448, 71, 52...
$ total_minutes                            <dbl> 1539, 2059, 2068, 2406, 1448, 9655, 4572, 16031, 831, 3194, 651, 1817,...
$ pct_matches_won                          <dbl> 0.4285714, 0.5555556, 0.3888889, 0.4761905, 0.6000000, 0.4302326, 0.35...
$ pct_service_games_won                    <dbl> 0.7528958, 0.8156863, 0.8000000, 0.8654434, 0.8208092, 0.7273535, 0.73...
$ pct_ace_per_service_point                <dbl> 0.072429210, 0.097885196, 0.117612978, 0.210579345, 0.100824931, 0.049...
$ pct_ace_per_serve                        <dbl> 0.05155952, 0.06946827, 0.08511530, 0.14838481, 0.07382550, 0.03572827...
$ pct_first_serves_in                      <dbl> 0.5952310, 0.5909366, 0.6181924, 0.5808564, 0.6342805, 0.6051338, 0.57...
$ pct_second_serves_in                     <dbl> 0.8674521, 0.9187592, 0.8133536, 0.8689904, 0.9122807, 0.9062932, 0.93...
$ pct_double_fault                         <dbl> 0.053651267, 0.033232628, 0.071263036, 0.054911839, 0.032080660, 0.037...
$ pct_points_won_off_first_serve           <dbl> 0.7000501, 0.7177914, 0.7225867, 0.8100607, 0.7557803, 0.6913357, 0.69...
$ pct_points_won_off_second_serve          <dbl> 0.4749632, 0.5347120, 0.4795144, 0.5348558, 0.4812030, 0.4719917, 0.48...
$ pct_break_points_saved                   <dbl> 0.5524476, 0.6328125, 0.6809816, 0.6173913, 0.5921053, 0.5657709, 0.57...
$ pct_break_points_lost                    <dbl> 0.4475524, 0.3671875, 0.3190184, 0.3826087, 0.4078947, 0.4342291, 0.42...
$ pct_break_points_converted_on_defense    <dbl> 0.40234375, 0.36619718, 0.37623762, 0.33636364, 0.39240506, 0.41233766...
$ pct_return_games_won_on_defense          <dbl> 0.19769674, 0.20312500, 0.14728682, 0.11490683, 0.17919075, 0.23049002...
$ avg_minutes_per_game                     <dbl> 1.481232, 4.029354, 3.992278, 3.707242, 4.184971, 4.333483, 4.093107, ...
$ avg_pct_of_rounds_won_in_all_tournaments <dbl> 0.13273810, 0.26530612, 0.11688312, 0.14484127, 0.22500000, 0.13184524...
```
