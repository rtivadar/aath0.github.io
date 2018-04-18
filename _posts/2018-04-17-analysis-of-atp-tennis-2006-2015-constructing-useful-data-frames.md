---
layout: post
title: "Analysis of ATP Tennis, 2006 - 2015: Constructing Useful Data Frames"
author: "Ethan Wicker"
categories: journal
tags: [tennis, data analysis, data frames]
image: forrest.jpg
---

## Introduction

This post is the second in a series covering an analysis of ATP (Association of Tennis Professionals) tennis matches.  

My first post in this series covered an introduction to the data I would be working with, as well as some cleaning and organizing of the data.  This post will cover how I constructed more useful data structures from the `atp` data frame I described in the earlier post.  

If you would like to read my first post in this series, you can find it here.  Also, you can find my entire R project here, if you would like to see all the code.  (INCLUDE LINKS)

The rest of this post references the `atp` data frame.  For clarity and completeness, this data frame has ___ observations across ___ different variables.  A the structure of the data frame is provided below using the `glimpse` function.

(INCLUDE GLIMPSE)

This post will likely be my longest in this series, and it will also be my most technical in terms of code.  During my analysis, the bulk of my time was spent constructing the data frames I will describe below.

This post will be divided into three main sections.  By the end of the three sections, I will have described how I created two very useful data frames: `atp_stats_by_player_by_year` and `atp_stats_overall_by_player`.

(INCLUDE TABLE OF CONTENTS?)

To preview this post, the three sections and their purposes are below:

1. Calculating Percent of Rounds Won in Tournaments

  * In this section, I will describe a metric I created, namely determining how far a player advances in a tournament on average, as a percent.  
  * I will create two data frames in this section.  The first data frame, `atp_pct_rounds_won_overall_by_player`, contains a percentage for each player of how far that player advanced through tournaments on average, aggregated over all years in my dataset.  The second data frame, `atp_pct_rounds_won_by_year_by_player`, contains a percentage for each player of how far that player advanced through tournaments on average, per year.
  
2. Calculating Player Stats per Year

  * In this section, I will construct `atp_pct_rounds_won_overall_by_player`, a data frame that contains information on match statistics for each player, per year.  
  * Examples of statistics that will be included are percentage of service games won, and percentage break points forced, among many more.

3. Calculating Player Stats Overall

  * In this section, I will construct `atp_pct_rounds_won_by_year_by_player`, a data frame that contains information on match statistics for each player.  This will be overall statistics for each player across all years of my dataset.
  * These statistics will be nearly identical to the statistics in the data frame constructed in the second section.
  
  ## Calculating Percent of Rounds Won in Tournaments
  
As briefly mentioned in the short preview above, in this section, I will be constructing two similiar data frames.  These data frames are `atp_pct_rounds_won_overall_by_player` and `atp_pct_rounds_won_by_year_by_player`.
  
Before I begin my discussion of how I constructed these data frames, I first need to explain the metric I am creating.  In short, I wanted  a way to quantify how far a player typically advances through a tournament, to see which players usually win more rounds than over players.  The clear problem here is that not all tournaments have the same number of rounds.  So winning a quarterfinal match in a grandslam where the draw size is 128 players isn't the same as winning a quarterfinal match in a much smaller tournament with a draw size of 32.

To quantify how far a player advanced in a tournament, I determined how many rounds of that tournament the player won, and then averaged these percentages out to get the yearly and overall percentages per player.

The metric works like this:  If a player wins no rounds in the tournament, that player advanced through 0% of the tournament.  If a player wins the entire tournament, that player advanced through 100% of the tournament.  If a player wins three rounds of a seven found tournament, but loses in the fourth round, then that player advanced through 3/7 = 42.86% of the tournament.
  
  ```r
  
  # Determining the last round a player won
atp_last_round_player_won_by_tournament <- atp %>% 
  group_by(tourney_name, tourney_date, winner_name) %>% 
  summarize(number_round_of_tournament_advanced_through = max(as.numeric(round)))

# Renaming "winner_name" to "name"
setnames(atp_last_round_player_won_by_tournament, old = "winner_name", new = "name")



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

atp_pct_rounds_won_by_tournament_by_year <- bind_rows(atp_last_round_player_won_by_tournament, 
                                                      atp_first_round_losers_by_tournament)



# Determining how many rounds are in a tournament, per year
atp_number_of_rounds_by_tournament <- atp %>% 
  group_by(tourney_name, tourney_date) %>% 
  summarize(index_of_first_round_in_tournament = min(as.numeric(round)),
            number_rounds_in_tournament = max(as.numeric(round)) - min(as.numeric(round)) + 1)

atp_pct_rounds_won_by_tournament_by_year <- full_join(atp_pct_rounds_won_by_tournament_by_year, 
                                                      atp_number_of_rounds_by_tournament,  
                                                      by = c("tourney_name", "tourney_date"))



# Calculating percent of rounds a player won in tournament, for each tournament
atp_pct_rounds_won_by_tournament_by_year <- atp_pct_rounds_won_by_tournament_by_year %>%
  mutate(pct_of_rounds_won_in_tournament = (number_round_of_tournament_advanced_through - index_of_first_round_in_tournament + 1)/
           number_rounds_in_tournament) %>% arrange(tourney_name)

# Filtering out Tour Finals matches - since round robin play doesn't fit
atp_pct_rounds_won_by_tournament_by_year <- atp_pct_rounds_won_by_tournament_by_year %>% 
  filter(tourney_name != "Tour Finals")

# Calculating average pct of rounds in a tournament a player wins, for each player
atp_pct_rounds_won_overall_by_player <- atp_pct_rounds_won_by_tournament_by_year %>% 
  group_by(name) %>% 
  summarize(avg_pct_of_rounds_won_in_all_tournaments = mean(pct_of_rounds_won_in_tournament))
  


atp_pct_rounds_won_by_tournament_by_year_ymd_separated <- atp_pct_rounds_won_by_tournament_by_year %>% 
  separate(tourney_date, into = c("year", "month", "date"), sep = "-")

# Calculating average pct of rounds in a tournament a player wins, for each player, per year
atp_pct_rounds_won_by_year_by_player <- atp_pct_rounds_won_by_tournament_by_year_ymd_separated %>% 
  group_by(year, name) %>% 
  summarize(avg_pct_of_rounds_won_in_all_tournaments = mean(pct_of_rounds_won_in_tournament))

```
  
  ## Calculating Player Stats per Year
  
  
  
  ## Calculating Player Stats Overall
