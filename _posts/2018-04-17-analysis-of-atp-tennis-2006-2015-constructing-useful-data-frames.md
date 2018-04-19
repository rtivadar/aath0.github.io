---
layout: post
title: "Analysis of ATP Tennis, 2006 - 2015: Constructing Useful Data Frames"
author: "Ethan Wicker"
categories: journal
tags: [tennis, data analysis, data frames]
image: forest.jpg
---

## Introduction

This post is the second in a series covering an analysis of ATP (Association of Tennis Professionals) tennis matches.  

My first post in this series covered an introduction to the data I would be working with, as well as some cleaning and organizing of the data.  This post will cover how I constructed more useful data structures from the `atp` data frame I described in the earlier post.  

If you would like to read my first post in this series, you can find it here.  Also, you can find my entire R project here, if you would like to see all the code.  (INCLUDE LINKS)

The rest of this post references the `atp` data frame.  For clarity and completeness, this data frame has ___ observations across ___ different variables.  A the structure of the data frame is provided below using the `glimpse` function.

(INCLUDE GLIMPSE)

This post will likely be my longest in this series, and it will also be my most technical in terms of code.  During my analysis, the bulk of my time was spent constructing the data frames I will describe below.

This post will be divided into three main sections.  By the end of the three sections, I will have described how I created two very useful data frames: `atp_stats_by_player_by_year` and `atp_stats_overall_by_player`.

(INCLUDE BIT ABOUT USING DPLYR & the pipe operating and dplyr pipes)

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

First, I used `mutate` to create a new column, `pct_of_rounds_won_in_tournament`.  

Next, I used `filter` to filter out all of the matches played at the ATP World Tour Finals Tournament.  Play at these tournaments occurs in a round-robin format, and thus doesn't match typical tournament style play.

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

atp_pct_rounds_won_by_tournament_by_year_ymd_separated <- atp_pct_rounds_won_by_tournament_by_year %>% 
  separate(tourney_date, into = c("year", "month", "date"), sep = "-")

# Calculating average pct of rounds in a tournament a player wins, for each player, per year
atp_pct_rounds_won_by_year_by_player <- atp_pct_rounds_won_by_tournament_by_year_ymd_separated %>% 
  group_by(year, name) %>% 
  summarize(avg_pct_of_rounds_won_in_all_tournaments = mean(pct_of_rounds_won_in_tournament))

```
  
  ## Calculating Player Stats per Year
  
  
  
  ## Calculating Player Stats Overall
