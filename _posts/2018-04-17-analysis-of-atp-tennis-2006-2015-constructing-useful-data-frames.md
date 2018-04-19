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

To finish organizing `atp_by_year_grouped_by_player_id`, I used `select` to to reorder the columns into a more appropriate order.  I also defined the `hand` variable to be a factor.

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


# Subsetting to get data frame of just year totals
atp_stats_by_player_by_year <- atp_by_year_grouped_by_player_id %>% 
  select(year:height, starts_with("total"))




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



# joining atp_stats_overall_by_player with atp_player_pct_rounds_won

atp_stats_by_player_by_year <- atp_stats_by_player_by_year %>% 
                                  full_join(atp_pct_rounds_won_by_year_by_player, 
                                            by = c("year", "name"))



  
  
  ## Calculating Player Stats Overall
