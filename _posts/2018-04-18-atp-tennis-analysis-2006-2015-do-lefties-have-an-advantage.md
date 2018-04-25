---
layout: post
title: "ATP Tennis Analysis, 2006 - 2015: Do Lefties have an Advantage?"
author: "Ethan Wicker"
categories: journal
tags: [tennis, data analysis, data frames]
image: tennis-nadal-serving.jpg
---

## Introduction

This post is the third in a series covering an analysis of ATP (Association of Tennis Professionals) tennis matches during the 10 year span between 2006 and 2015.  You can find the first two posts in this series at the following links:

* (INSERT LINK)
* (INSERT LINK)

In this post I will be discussing whether or not left-handed players have an advantage over their right-handed counterparts.  To provide some context, it has long been believed that lefties have an advantage over righties in various sports, and tennis especially, is no exception.  

In tennis, there are various reasons why left-handed players are thought to have an advantage over right-handed players.  A crucial reason, if not the largest advantage lefties might possess, is that service games in tennis always start in the deuce court (right side) and then serves alternate from there, switching between the ad court (left side) and back to the deuce court until the service game is over.  What this means, is that more often than not, and everytime the score of a service game is 40-40 (simply called deuce), the last serve of the service game will come from the ad court. And, due to the manner in which the net in tennis is pulled down from the middle, coupled with how players mechanical serve to produce spin, should provide an advantage to lefties.

Another typical reason lefties are thought to have an advantage - this one less tennis specific - is just that there are less of them.  They're just a rarer breed.  And since players typically play right-handed players, when they face the occasional unicorn of the left-handed player, they're thrown off their game.  For a more tennis exact rational of this, this often has to do with both tennis strategy, and the spin of the ball coming off different shots.  In short, when playing a leftie, a player has to flip their strategy upside down.  All of a sudden, hitting a backhand approach has to be hit to the opposite side.  And as for the spin, tennis players rarely hit perfect 6-o'clock-to-12-o'clock spin on the ball.  Often times that spin has a little or a lot side spin mixed in as well - think 5-o'clock-to-11-o'clock for a right-handed forehand.  This spin completely changes how the ball flies through the air, how the ball bounces, and how potentionally, how a player has to redirect a ball to counter the spin.

This only scratches the surface of the advantages and disadvantages of handediness in tennis, but if you were unfamiliar with the nuances of the sport, it provides you with enough information to see the legitimacy in the question.

For the remainder of this post, I'll be describing my methods and my results, as well as including and discussing various segments of my R code.  I'll be discussing some smaller topics in each section, such as if lefties serve more aces or win more a higher percentage of service games.  Towards the end of this post, I'll perform some more indepth statistical analysis (i.e. a Two Proportion z-Test and a Chi-Squared Test of Independence) and I'll summarize my results.

So, do lefties really have an advantage?

The answer: No.  Not a bit.  If anything, they lose more matches than they should.  Which is curious.  

Keep reading (or skimming) below to see my rationale and supporting arguments.

I should pause right here and make one very important point.  My analysis shows that lefties, during the 10 year span from 2006 - 2015 did not have an advantage over right handed players (in part because they lost more often to righties than won over them).  This analysis, and the resulting conclusion only holds true for ATP World Tour players (i.e. the top 250 - 500 or so players in the world), and cannot really be extrapolated beyond these dates.

Sure, I would absolutely be willing to extend my conclusion out some years and bet that lefties still do not have an advantage in today's game.  But this analysis can not be used to draw any conclusions about lower skilled players.  Among lower level players, such as those on the Challenger or Future circuits, or even college players in the US, lefties almost universely have an advantage (although some future analyses would be needed to really show this).  And of course, at even lower levels, we would imagine the advantage would increase.  However, my analysis is only concerned with ATP World Tour players between 2006 and 2015, and the results of my analyses cannot properly be extrapolated to lower level players.  See the conclusion section of this post for more details.

---

## The Analysis  

To begin this analysis, I'll first look at specific match statistics to see if, for example, left-handed players serve better or if they win more break points.  After that, I'll turn my attention towards match-ups of left-handed players versus right-handed players.  And finally, I'll then look at the overall proportions of matches won by left-handed players versus right-handed players to see if there is any statistically significant difference.

Unfortunately, the playing hand for 114 out of the 832 unique players in my data set is unknown.  These players are mostly players that only played in a couple of smaller tournaments and were not very successful at this level of the game.  The mean and median world ranking for these players is 314 and 275, respectifully.  The mean and median total number of matches played for these players was 2.47 and 1, confirming their lack of playing time.  Regardless, since their playing hand is unknown, they have to be removed from my data set for the purposes of this analysis.

Note:  For the majority of this analysis, I'll be referencing the `atp_stats_overall_by_player` data frame constructed in my second post in this series.  See that post for more details on how it was constructed.

```r
# Fitlering out where player's hand is unknown
atp_stats_overall_by_player_left_right_known <- atp_stats_overall_by_player %>% filter(hand != "Unknown")
```

Furthermore, it's worth checking a couple of conditions before diving into the bulk of my analysis.  The first condition worth checking is to ensure that there is a relatively even distribution of left-handed players and right-handed players in the data set.  If for instance, lefties really did have an enormous advantage, and there were a disporptionionate amount of them in the among the very top players, that could throw off some of my later analyses.  

```r
# Creates boxplot - making sure lefties are uniformly dispersed across players
atp_stats_overall_by_player_left_right_known %>% ggplot(aes(x = hand, y = avg_rank, color = hand)) + 
  geom_boxplot() +
  coord_flip()
  ```
  (FIX PLOT)
![alt text](https://github.com/ethanwicker/ethanwicker.github.io/blob/master/assets/img/tennis-left-right-distribution.jpeg "Tennis-left-right-distribution")
  
We can see in the above boxplots that the distribution of left-handed players and right-handed players seems to be pretty even across the rankings.  Ofcourse, since there are more righties, we would expect their overall spread to be larger, and it is.  To quantify this a little more, the median world ranking is 147 and 153 for all left-handed and right-handed players, respectfully.  The mean world ranking for each is 187 and 201.
  
Looking at world rankings alone, it appears that lefties do tend to be ranked higher, but there is more to the story.

The other condition worth checking is the percentage of lefties in the data set.  According to a quick google search, somewhere between 10% and 13% of the world's population is left-handed.  In my data set of 718 unique players, 13.93% are left-handed.  This is a bit higher than we'd expect, assuming that left-handedness would be equally common among ATP Tennis Players as the rest of the world.  Although it seems to be a bit higher than the world average, it's not all that far off. (MAYBE ADD PROP TEST IN HERE - or CHI SQ GOF)

### Specific Match Statistics

In the above, I've shown that left-handedness isn't any more rare or more common in ATP Tennis Players than in the general population, and that left-handed players are pretty well distributed among all the players, regardless of rank.

In this portion of the post, I'll be comparing overall match statistics for left-handed players versus right-handed players.  

To begin, I'll be looking at whether or not lefties **serve more aces**.  To determine this in my analysis, I calculated the average number of aces served per service point for both left-handed and right-handed players.  

```r
atp_stats_overall_by_player_left_right_known %>% 
  group_by(hand) %>% 
  summarize(avg_pct_ace_per_service_point_by_hand = mean(pct_ace_per_service_point, 
                                                     na.rm = TRUE))                                                     
```
Per service point, lefties on average serve 6.29% aces.  For righties, that number increases slightly to 6.38%.  This is a fairly small increase, and doesn't really warrent hypothesizing a reason why.

To continue with the offense side of things, next I'll look at whether lefties or righties win a higher percentage of **points off the first serve**.

```r
atp_stats_overall_by_player_left_right_known %>% 
  group_by(hand) %>% 
  summarize(avg_pct_points_won_off_first_serve_by_hand = mean(pct_points_won_off_first_serve, 
                                                              na.rm = TRUE))
```

And there are similar results here.  Left-handed players win 66.8% of their points when they get their first service in, while right-handed players win 67.6%.  Once again, although the righties are just a nudge higher, the differences are quiet small.

Below I'll repeat the same procedure, but these time to determine which type of player wins a higher percentage of **points off the second serve**.

```r
atp_stats_overall_by_player_left_right_known %>% 
  group_by(hand) %>% 
  summarize(avg_pct_points_won_off_second_serve_by_hand = mean(pct_points_won_off_second_serve, 
                                                               na.rm = TRUE))
```

On the average, left-handed players win 46.9% of points after missing their first serve and getting their second serve in.  For right-handed players, that percentage increases slightly once again to 47.3%.  

On a side note here:  It's interesting that both left-handed and right-handed players win less than 50% of service points off their second serve.  This would be an interesting place for further explaration.  Perhaps players on the whole would be better off increasing their speed or placement on the second service (i.e. taking more risk), and with data on serve speeds, perhaps their is a breakeven point in terms of serve speed and percentage of points won off the second serve. (MAYBE TAKE OUT).

To finish looking at the offensive side, I compared whether left-handed or right-handed players won a higher **percentage of service games**.
```r
atp_stats_overall_by_player_left_right_known %>% 
  group_by(hand) %>% 
  summarize(avg_pct_service_games_won_by_hand = mean(pct_service_games_won, 
                                                     na.rm = TRUE))
```
On the average, left-handed players win 69.6% of their service games.  Once again, right-handed players win a slightly higher percentage of their service games at 70.4%.

Moving to the defensive side of the ball, I looked at whether lefties or righties win a higher percentage of the break points they force, as well as which handed players win a higher percent of return games.

```r
atp_stats_overall_by_player_left_right_known %>% 
  group_by(hand) %>% 
  summarize(avg_pct_break_points_converted_on_defense_by_hand = 
              mean(pct_break_points_converted_on_defense, na.rm = TRUE))
```

As for the **average percentage of break points converted** on defense, lefties convert 36.4% and righties convert 37.3%.

The last defense statistic I looked at was the **percentage of return games won**.

```r
atp_stats_overall_by_player_left_right_known %>% 
  group_by(hand) %>% 
  summarize(avg_pct_return_games_won_on_defense = 
              mean(pct_return_games_won_on_defense, na.rm = TRUE))
```

On the average, lefties win 16.1% of their return games, while righties win 16.8%.

And lastly, the last simple statistic I looked at is on the average, which type of player wins a higher **percentage of their matches**.

```r
atp_stats_overall_by_player_left_right_known %>% 
  group_by(hand) %>% 
  summarize(avg_pct_matches_won_by_hand = mean(pct_matches_won, 
                                               na.rm = TRUE))
```

Across the board, left-handed players win an average of 25.7% of their matches, while right-handed players win an average of 27.6%.

(MOVE THIS TABLE TO THE VERY TOP)(CHECK THE SIGNIFICANCE AGAIN)
Below is a summary table for the above information.  Note: "Avg Pct" stands for "Average Percent"

Statistic | Left-Handed Players | Right-Handed Players | Type of Player with Higher Stat | Significant at \\( \alpha = 0.05 \\) Level?
--------- | ------------------- | -------------------- | ------------------------------- | -------------------------------------------
Avg Pct Ace Per Service Point | 6.29% | 6.38% | Right | No
Avg Pct Points Won Off 1st Serve | 66.8% | 67.6% | Right | **Yes**
Avg Pct Points Won Off 2nd Serve | 46.9% | 47.3% | Right | No
Avg Pct Service Games Won | 69.6% | 70.4% | Right | No
Avg Pct of Break Points Converted |36.4% | 37.3% | Right | No
Avg Pct Return Games Won | 16.1% | 16.8 | Right | No
Avg Pct Matches Won |25.7% | 27.6% | Right | No

I also performed a Two-Sample t-Test on each of the above statistics to see if the differences in any were significant at the \\( \alpha = 0.05 \\) significance level.  The only statistic that was significant was the average percentage of points won off the first serve, with a p-value of 0.02948.  This p-value, although below the common threshold of 0.05, isn't terribly low, so I wouldn't feel comfortable stating that right-handed players win significantly more points off their first serve than left-handed players.

For completeness, and for anyone interested in the syntax, I've provided my R code for the significant result below.  To perform a t-Test on a different variable, just change out the appropriate portion of code.

```r
t.test(atp_stats_overall_by_player_left_right_known$pct_first_serves_in ~ 
         atp_stats_overall_by_player_left_right_known$hand)
```

Although the mean difference was only statistically significant for one variable I examined, right-handed players had, on average, higher stats across the board.  This provides evidence to support my argument that left-handed players do not have an advantage amongst the highest level of professional male tennis players.

However, just slightly higher percentages across seven stats isn't exactly clear evidence for any verdict.  In the rest of this post I'll be comparing head-to-head matchups between left-handed and right-handed players.  I'll also be examining the overall proportion of matches won and lost by left-handed and right-handed players to see if there is in discernible difference.

### Head-to-head Matchups

In this portion of this post, I'll be looking at head-to-head matchups between left-handed players and right-handed players.  The objective here is to see if, on the average, lefties or righties win more in matches between each other.

If left-handed players have an advantage, we would expect them to win more matches against right-handed players.  That is, we would expect them to win more than 50% of these matches, due to this supposed advantage.

In the below chunk of code I created the data frame `matches_right_vs_left` which contains the percentage of these matche won by left-handed players, as well as the percentage won by right-handed players.  These are of course complements.
```r
number_matches_right_vs_left_won_by_right <- atp %>% 
  filter((winner_hand == "Right" & loser_hand == "Left")) %>% 
  summarize(number_right_vs_left_won_by_right = n())

number_matches_right_vs_left_won_by_left <- atp %>% 
  filter((winner_hand == "Left" & loser_hand == "Right")) %>% 
  summarize(number_right_vs_left_won_by_left = n())

matches_right_vs_left <- number_matches_right_vs_left_won_by_right %>% 
  bind_cols(number_matches_right_vs_left_won_by_left) %>% 
  mutate(total_matches_right_vs_left = number_matches_right_vs_left_won_by_right + 
           number_matches_right_vs_left_won_by_left,
         pct_matches_right_vs_left_won_by_right = number_matches_right_vs_left_won_by_right/
           total_matches_right_vs_left,
         pct_matches_right_vs_left_won_by_left = number_matches_right_vs_left_won_by_left/
           total_matches_right_vs_left)
```

From the above data frame, we can see that 48.1% of head-to-head matchups are won by the left-handed player, while 51.9% are won by the right-handed player.

So across 10 years of modern man's tennis, right-handed players have actually won a higher percentage of these match ups than left-handed players.  

### Proportion of Match Wins and Losts

In the last portion of this analysis below, I explored the proportion of all matches won and lost by left-handed and right-handed players between 2006 and 2015.  Using a Two-Sample proportion test, I then show that right-handed players win a statistically significant higher proportion of their matches.


```r
atp_hand_analysis <- atp[, c("tourney_id", "tourney_date", "tourney_name", "surface", 
                         "winner_name", "loser_name", "winner_hand", "loser_hand", 
                         "winner_rank", "loser_rank")]



# Renaming columns to more relevant names, and using gather to organize data
atp_hand_analysis <- atp_hand_analysis %>% 
  rename(Won = winner_hand, Lost = loser_hand ) %>%      
  gather(key = result, value = hand, Won, Lost) 

# Filtering out players with unknown hand
atp_hand_analysis <- atp_hand_analysis %>% filter(hand != "Unknown")

table_of_right_left_won_lost <- table(atp_hand_analysis$hand, 
                                      atp_hand_analysis$result)
```


  (FIX TABLE_
|  | Lost | Won
- | ---- | ---
Left | 3329 | 3148
Right | 22143 | 22503


```r
# Two Proportion z-Test
# Result: p-value of 0.007041, there is a statistically significant difference in proportion of
# matches lefties and righties win.
#
# Since lefties actually lost more of their matches overall, there is strong evidence to show that
# right handers actually win more matches.
prop.test(table_of_right_left_won_lost)
```




win more service games
break more often
win more return games
win more matches
win more tournaments
- add in a table here to summarize the above percentages - 
head to head match ups
prop test for all
prop test for top 100 vs top 100
proportion tests


