---
layout: post
title: "ATP Tennis Analysis, 2006 - 2015: What Makes the Greats so Great?"
author: "Ethan Wicker"
categories: journal
tags: [tennis, data analysis, data frames]
image: tennis-federer.jpg
---

## Introduction

This the fourth post in a series covering an analysis of ATP (Association of Tennis Professionals) Tennis Matches between the 10 year span from 2006 to 2016.  You can find my previous posts in this series at the following links:

* [ATP Tennis Analysis, 2006 - 2015: An Introduction and Data Cleaning](https://ethanwicker.github.io/journal/atp-tennis-analysis-2006-2015-an-introduction-and-data-cleaning.html).
* [ATP Tennis Analysis, 2006 - 2015: Constructing Useful Data Frames](https://ethanwicker.github.io/journal/atp-tennis-analysis-2006-2015-constructing-useful-data-frames.html)
* [ATP Tennis Analysis, 2006 - 2015: Do Lefties have an Advantage?](https://ethanwicker.github.io/journal/atp-tennis-analysis-2006-2015-do-lefties-have-an-advantage.html)

In the rest of this post, I'll be referencing various parts of those previous posts.  In particular, I'll be making use of the `atp_stats_overall_by_player` and `atp_stats_by_player_by_year` data frames created in the second link above about creating useful data frames.  You may want to read those posts before this one.  

Also, for anyone interested in my methodology or attempting to learn, I've include all of my R code used for the analysis in this post as well.  Furthermore, if you would like to see my entire R script file for this entire project, you can find it here.  (INSERT LINK) 

SPELL CHECK EVERYTHING

In this post, I'll be discussing what separates the greatest players in the game from everyone else.  I'll attempt to quantify what is it about these few players that uniquely sets them apart from all the other incredibly skillful tennis players in the world.

Before I begin, I should add some context and clarity.  This data analytics series on ATP tennis matches only covers the years 2006 to 2015.  In the future, I'll likely expand and revise to cover a larger span of years.  However, for now, I should make it clear that my analysis will really only be appropriate for those years.  However, the general traits of great players I uncover can absolutely be applied to other similar years.  In the past 20 or so years, as racquet technology has increased and the game has evolved, what makes a player great has also evolved.  As such, the general traits of great players I lay out can be generalized to similar years.  Clearly however, these traits and characteristics cannot be extrapolated to make conclusions about the game of tennis in the 1970s or 1980s, for example.

Now, it doesn't take a detailed analysis to tell any tennis fan which great players I am talking about.  Over the past 15 years of the game, 3 or 4 players have absolutely dominated the landscape of men's professional tennis.  Roger Federer, Rafael Nadal, Novak Djokovic, and, to a slightly lesser extent, Andy Murray, have been the irrefutable best players in the world over their consecutive careers.  

To make this clear, between 2006 and 2015, the trio of Federer, Nadal, and Djokovic won 34 out of the 40 Grand Slams (also know as Majors).  That's 85%.  Theres no denying that these three players outpaced everyone else.  

Depending on who you talk to, some tennis fans, myself included, will also want to include Andy Murray in this select group of great players.  Between 2006 and 2015, Murray only won 2 Majors, which is just a fifth of Djokovics 10, or less than a sixth of Nadal's 13 over the same time period.  However, in evaluating Murry, we must also include Murray's semi-finals and quarter-finals wins in Grand Slam Tournaments.  Below is a table with the total number of matches won in Grand Slams, by round, for the best players between 2006 and 2015 to illustrate this point:

Name | Number of Grand Slams Won | Number of Semi-Final Matches Won | Number of Quarter-Final Matches Won
---- | ---------------------- | ----------------------------- | --------------------------------
Rafael Nadal          |           13 | 18 | 30
Roger Federer         |           11 | 20 | 21
Novak Djokovic        |           10 | 18 | 26
Andy Murray           |            2 | 8  | 16
Stanislas Wawrinka    |            2 | 2  | 5
Andy Roddick          |            0 | 2  | 3
Robin Soderling       |            0 | 2  | 2
Juan Martin Del Potro |            1 | 1  | 3
Marin Cilic           |            1 | 1  | 3
David Ferrer          |            0 | 1  | 6
Jo Wilfried Tsonga    |            0 | 1  | 6
Tomas Berdych         |            0 | 1  | 5

As you can see, although Murry won the same amount of Majors as Stanislas Wawrinka, he's won far more semi-final and quarter-final matches than any other lower player.  It is for this reason, as well as some we'll see farther along in this analysis, that I'll be including Murray as one of the great players between the years of 2006 and 2015.

Not surpisingly, what makes a tennis player great is to win often, and to win consistently.  

In the remainder of this post, I'll be analysis and discussing some specifics about these great players and what allows them to win often and consistently.  I'll be looking at some more specific statistics, as well as trends over time.  Finally, I'll look at average match time for the first few rounds of a Major to see if any of these players play particularly short matches early on in a Major (as thus save energy).

---

## The Analysis

In this data analysis, I'll be discussing and displaying various data visualizations.  In particular, looking at the data here helps us see trends, and helps us see just how much more consistent the top tennis players were between 2006 and 2015.  

This analysis will roughly be divided into various sections, divded between the specific player statistics I will be analyzing in that section.  I'll start with overall matches and the offense side of the game first, and then move to the defense side of things.  Lastly, I'll look at player efficiency via average minutes per match. 

Throughout much of the below analysis, I subsetted my visualizations to include only players who for any year, were ranked in the top 5 players, by yearly avearge ranking.  This was done to make the trends in the data easier to see, and also for direct comparison between the great players and the next tier down of really good, but not consistent players.  As you'll see in the line graph belows, for players with an average ranking at or above the top 5 in the world in consectutive years, there are lines drawn between their data points.  On the contray, for players who sparacticaly were ranked in the top 5 players in the world, their statistics are just represented as singulary data points.

I have one last important thing to mention as well before the analysis.  There were two instances in which a player had an average ranking of 5 or greater, but in non-consecutive years.  This happen for David Ferrer in 2008 and 2013, and Andy Murray in 2013 and 2015.  When constructing the line graphs, `ggplot2` drew in the lines between these points.  This is not what I wanted, and did not create a true visualization of the data.  There was no convenient manner I could find to fix this, so I had to hard code the inbetween years for these two players.  For example, I hard coded David Ferrers `pct_matches_won` to `NA` and his `avg_rank` to 5 for the years 2009, 2010, 2011, and 2012.  This was a workaround to create the graphs how I wanted them.  In the below R code, I have not shown how I did this, just to not confuse any readers.  However, if you're curious you can take a look at the analysis #2 portion my R script file, where you'll find commented code explaining everything.  For the remainder of this post however, you can just ignore this paragraph.
 
### Percentage of Matches Won

To begin the analysis, I'll first be looking at percentage of matches won by player.  In the below graphs, I'll discuss this statistic per year, as well as overall.

To begin, below is a line graph of a player's percent of matches won on the y-axis versus year on the x-axis.

```r
atp_stats_by_player_by_year %>% filter(avg_rank <= 5) %>% 
  ggplot(aes(x = year, y = pct_matches_won, 
             group = name, color = name)) + 
  geom_line(size = 1.25) +
  geom_point() +
  scale_colour_discrete(guide = 'none') +
  scale_x_discrete(expand=c(0, 1)) +
  geom_dl(aes(label = name), 
          method = list(dl.combine("last.points"), cex = 1),
          position = position_nudge(x = 0.1)) +
  labs(title = "Percent of Matches Won for Top 5 Players Per Year, 2006 - 2015",
       x = "Year", 
       y = "Percent of Matches Won")
```
![alt text](/assets/img/tennis-pct-matches-won.jpeg "Tennis Percent Matches Won")

As you can see, various players are in and out of the top 5 world rankings throughout the years.  In total, 12 different players had an average ranking of 5 or higher for any given year.  As for the great players of Federer, Nadal, Djokovic, and Murray, only Federer is ranked in the top 5 throughout all 10 years.  Dkojokic, whose average ranking enters into the top 5 in 2008, also does not leave for the remainder of the years.  Nadal and Murray's average ranking both momentarily drop out of the top 5.  Nadal, after a string of bad performances in 2015 dips below 5, and Murray's average ranking goes below 5 for the year of 2014, but he bounces back the very next year.

Looking at the trends in the graph more closely, notice the consistency of Federer from 2006 to 2012, in which his match win percentage never dropped below 80%.  Perhaps the most incredible statistic in this data set as well, is that in the 2006 season, Federer won 94.7% of his matches. The next closest match win percentage is Novak Djokovic in 2011, where he won 94.0% of his matches.  

Other interesting trends here are Djokovic's continuous rise in match win percentage from 2012 to 2015, and Nadal's consistent up and down performance across all years.

To look at percentage of matches won in more detail, below is a histogram of average percentage of matches won between 2006 and 2015.  To make the histogram more meaningful as well as more readable, I filtered the data to only include players with at least 10 match wins.

```r
atp_stats_overall_by_player %>% 
  filter(total_matches_won >= 10) %>% 
  ggplot(aes(x = pct_matches_won)) + 
  geom_histogram(binwidth = .02, fill = "cyan", color = "black") +
  labs(title = "Percent of Matches Won between 2006 and 2015",
       subtitle = "Minimum of 10 Match Wins",
       x = "Percent of Matches Won",
       y = "Count")
```
![alt text](/assets/img/tennis-histogram-pct-matches-won.jpeg "Tennis Histogram Percent Matches Won")

This histogram shows just how far above all other players Federer, Nadal, and Djokovic are.  The right most bar of the histogram represents three players.  These players are Federer, Nadal, and Djokovic, with overall match win percentages of 84.7%, 84.3%, and 83.7%, respectively.  The bar second from the right representing one player is Andy Murray, with a match win percentage of 76.3%.  The player represented by the bar to the left of Murray is Andy Roddick, with a match win percentage of 73.1%.


### Percentage of Service Games Won

Moving on, I turned my attention to a player's percent of service games won, by year.

```r
atp_stats_by_player_by_year %>% filter(avg_rank <= 5) %>% 
  ggplot(aes(x = year, y = pct_service_games_won, 
             group = name, color = name)) + 
  geom_line(size = 1.25) +
  geom_point() +
  scale_colour_discrete(guide = 'none') +
  scale_x_discrete(expand=c(0, 1)) +
  geom_dl(aes(label = name), 
          method = list(dl.combine("last.points"), cex = 1),
          position = position_nudge(x = 0.1)) +
  labs(title = "Percent of Service Games Won for Top 5 Players Per Year, 2006 - 2015",
       x = "Year", 
       y = "Percent of Service Games Won")
```

![alt text](/assets/img/tennis-pct-service-games-won.jpeg "Tennis Percent Service Games Won")


This graph, perhaps more than any other, shows the clear ordering of Roger Federer as the greatest player from 2006 to 2015, and Andy Murray as the fourth best player during that time frame.  Rafael Nadal and Novak Djokovic continually faught for the number two world ranking, and it shows as their percentage of service games won lines cross five times between 2008 and 2014.

Next is an interesting scatterplot of a player's percent of service games won versus that player's height.  For clarity, I've included only players with an average world ranking in the top 50.

```r
atp_stats_overall_by_player %>% 
  filter(avg_rank <= 50) %>% 
  ggplot(aes(x = height, y = pct_service_games_won, label = name)) + 
  geom_point() +
  geom_jitter() +
  geom_label(cex = 4) +
  labs(title = "Percent Service Games Won vs. Height",
       subtitle = "Top 50 Averaged Ranked Players",
       x = "Height (cm)",
       y = "Percent Service Games Won")
```  

![alt text](/assets/img/tennis-pct-service-games-won-vs-height.jpeg "Tennis Percent Service Games Won Versus Height")


Theres a pretty weak positive association between a player's height and percentage of service games won.  However, what's interesting here is that for player's of their height, Federer and Nadal win the highest percentage of service games.  For reference, Federer and Nadal both stand at 185cm, or about 6'1".  For all players that are 185cm tall, the average percentage of service games won is 72.4%.  For players with an average ranking above 50, that percentage increases to 81.3%.  Federer and Nadal both win well above 85% of their service games.


### Average Tournament Advancement

Next, below is a line graph that plots the a player's average tournament advancement, as a percent, versus year.  This plot is similar to the earlier plot on percentage of matches won.

```r
atp_stats_by_player_by_year %>% filter(avg_rank <= 5) %>% 
  ggplot(aes(x = year, y = avg_pct_of_rounds_won_in_all_tournaments, 
             group = name, color = name)) + 
  geom_line(size = 1.25) +
  geom_point() +
  scale_colour_discrete(guide = 'none') +
  scale_x_discrete(expand=c(0, 1)) +
  geom_dl(aes(label = name), 
          method = list(dl.combine("last.points"), cex = 1),
          position = position_nudge(x = 0.1)) +
  labs(title = "Average Percent of Rounds Won In All Tournaments for Top 5 Players Per Year, 2006 - 2015",
       x = "Year", 
       y = "Average Percent of Rounds Won In All Tournaments")
```

tennis-avg-percent-of-rounds-won-tournament
![alt text](/assets/img/tennis-avg-percent-of-rounds-won-tournament.jpeg "Tennis Average Percent Of Rounds Won In Tournaments")


As you would expect, the trends here are pretty similar to the first line graph showing percent of matches won.  Federer and Nadal both bounce around 75% for much of the data.  This means, that for those years, both these players tended to advance about 75% of the way through a tournament before losing out.  Andy Murray is consistently below Federer, Nadal, and Djokovic in just about every year.

A couple of interesting observations with this plot are Federer's 2006 season and Djokovic's 2015 season, which are commonly held as two of the most dominating season in men's professional tennis.  In these years, respectively, both players reached the finals of all four Grand Slams, each and won three Grand Slams.  They both lost in the finals of the French Open.  In these years, Federer and Djokovic advanced through 90.3% and 90.8% of tournament rounds, respectively.  These were the highest such averages for the data set.

From the previous two line graphs, we've seen that Federer, Nadal, Djokovic, and Murray win more matches than every other player on tour, and also do so much more consistently.  Out of the remaining eight players that make appearances in the graphs, only Nikolay Davydenko had consecutive years with an average ranking of 5 or higher.

To make the dominance of Federer, Nadal, and Djokovic clear, we can look at the below scatterplot showing a average percent of tournament advancement versus total matches won.

```r
atp_stats_overall_by_player %>% filter(avg_rank <= 50) %>% 
  ggplot(aes(x = total_matches_won, y = avg_pct_of_rounds_won_in_all_tournaments, label = name)) + 
  geom_point() + 
  geom_label() +
  labs(title = "Average Percent of Rounds Won in Tournament vs. Total Matches Won",
       subtitle = "Top 50 Averaged Ranked Players",
       x = "Total matches Won",
       y = "Average Percent of Rounds Won in all Tournaments")

```

tennis-avg-percent-rounds-won-versus-total-matches-won
![alt text](/assets/img/tennis-avg-percent-rounds-won-versus-total-matches-won.jpeg "Tennis Average Percent Of Rounds Won In Tournaments Versus Total Matches Won")


We of course expect the linear relationship here, but notice the large gap between Federer, Nadal, and Djokovic and everyone else.

### Double Faults

So far, we've looked at various statistics to see how dominating Federer, Nadal, Djokovic, and to a slightly lesser percent, Murray was between 2006 and 2015.  The stats we've looked at were percent of matches won, percentage of service games won, and average advancement through tournaments.  

As previously mentioned, what makes the greats so great is their abilitiy to play at an immensily high level for a long period of time.  To drive this point home, particularly for the likes of Roger Federer, I took at look at a double fault percentages for all players with at least 10 wins.  To be clear, this is the percentage of times a player double faults per service point.

```r
atp_stats_overall_by_player %>% filter(total_matches_won > 10) %>%
  arrange(pct_double_fault)
```

Among all 258 players with more than 10 wins in my data set, Roger Federer had the third lowest double fault percent at 1.86%.  Rafael Nadal had the eight lowest, at 2.08%.

### Percent Return Games Won

Now that I've discussed overall match wins as well as the serving side of the game, I'll turn my attention towards the defense side.  To begin, below is line graph showing a player's percent of return games won versus year.  Once again, I've filtered the data to only include players with an average ranking at or above 5 for a given year.

```r
atp_stats_by_player_by_year %>% filter(avg_rank <= 5) %>% 
  ggplot(aes(x = year, y = pct_return_games_won_on_defense, 
             group = name, color = name)) + 
  geom_line(size = 1.25) +
  geom_point() +
  scale_colour_discrete(guide = 'none') +
  scale_x_discrete(expand=c(0, 1)) +
  geom_dl(aes(label = name), 
          method = list(dl.combine("last.points"), cex = 1),
          position = position_nudge(x = 0.1)) +
  labs(title = "Percent of Return Games Won for Top 5 Players Per Year, 2006 - 2015",
       x = "Year", 
       y = "Percent of Return Games Won")
```
![alt text](/assets/img/tennis-pct-return-games-won.jpeg "Tennis Percent Return Games Won")


What's interesting here is that after 2006, Federer is consistently below Nadal, Djokovic, and Murray.  Federer is almost universially thought to be the best tennis player in history, so we might expect his defense game to be a little stronger.  

However, once again, what separates the great players apart is just their sheer consistency.  While other top ranked players win a higher percentage of return games in the sparitic years they are ranked, the great players are consistently ranked highly and consistently return well.

### Percent of Break Points Converted

To further quantify how well the great players play defense, I took at look at the percentage of break points converted for all players with more than 10 wins in that data set.

```r
atp_stats_overall_by_player %>% filter(total_matches_won > 10) %>% 
  arrange(desc(pct_break_points_converted_on_defense))
```

As we might expect from the above line graph of percent of return games won, Nadal and Djokovic are both among the highest in terms of their percentage of break points forced and then converted.  In particular, Nadal ranks third among 258 players, winning 45.2% of the break points he forces.  Djokovic ranks ninth at 44.2% of break points converted.


### Efficiency in Grand Slams

As a last facet of the sport to analyze, I took at look at how efficient players were.  In particular, during the first four rounds of Grand Slams (which have seven rounds in total), I analyzed and ranked players by their average number of minutes per match.

To analyze this statistic, I had to construct the `atp_grand_slams` data frame, which contains player statistics only from matches in the first four rounds of Grand Slams.

To construct these data frames, I followed a very similar process to what I described in my [second post in this series](https://ethanwicker.github.io/journal/atp-tennis-analysis-2006-2015-constructing-useful-data-frames.html).  You can find more information and rationale for how I constructed this data frame there.  For completeness, I've included the code below.

```r
benchmark_number_of_rounds <- 4

atp_grand_slams_by_winner_id <- atp %>% 
  group_by(winner_id) %>% 
  filter(tourney_level == "Grand Slam") %>% 
  filter(as.numeric(round) <= benchmark_number_of_rounds) %>% 
  summarize(total_matches_as_winner = n(),
            total_games_played_as_winner = sum(w_SvGms, na.rm = TRUE) + 
                                           sum(l_SvGms, na.rm = TRUE),
            total_minutes_as_winner = sum(minutes, na.rm = TRUE))

atp_grand_slams_by_loser_id <- atp %>% 
  group_by(loser_id) %>% 
  filter(tourney_level == "Grand Slam") %>% 
  filter(as.numeric(round) <= benchmark_number_of_rounds) %>% 
  summarize(total_matches_as_loser = n(),
            total_games_played_as_loser = sum(w_SvGms, na.rm = TRUE) + 
                                          sum(l_SvGms, na.rm = TRUE),
            total_minutes_as_loser = sum(minutes, na.rm = TRUE))

winners_columns_replace_na_with_0_grand_slams <- list(total_games_played_as_winner = 0,
                                                      total_minutes_as_winner = 0,
                                                      totaL_matches_as_winner = 0)

atp_grand_slams_by_loser_id <- full_join(atp_grand_slams_by_loser_id, 
          atp_player_info, by = "loser_id")



setnames(atp_grand_slams_by_winner_id, old = "winner_id", new = "player_id")
setnames(atp_grand_slams_by_loser_id, 
         old = c("loser_id", "loser_name", "loser_hand", "loser_ioc"), 
         new = c("player_id", "name", "hand", "ioc"))

atp_grand_slams <- full_join(atp_grand_slams_by_winner_id, 
                             atp_grand_slams_by_loser_id,
                             by = "player_id")

atp_grand_slams <- atp_grand_slams %>% replace_na(winners_columns_replace_na_with_0_grand_slams)

atp_grand_slams <- atp_grand_slams %>% select(player_id, name, hand, ioc, everything())

atp_grand_slams <- atp_grand_slams %>% 
  mutate(total_matches = total_matches_as_winner + total_matches_as_loser,
         total_games_played = total_games_played_as_winner + total_games_played_as_loser,
         total_minutes = total_minutes_as_winner + total_minutes_as_loser,
         avg_minutes_per_match = total_minutes/total_matches,
         avg_minutes_per_game = total_minutes/total_games_played)
         
atp_grand_slams %>% 
  filter(total_matches_as_winner > 10) %>% 
  arrange(avg_minutes_per_match)         
``` 

After constructing this data frame, I filtered it to inlude only players with more than 10 wins, and then arranged the data according to the average number of minutes per match.

Among all 103 players who had won at least 10 Grand Slam matches from the first four rounds of Grand Slams, Roger Federer ranks as the number one most efficent player per match.  That is, he plays these matches in less amounts of time than anyone else.

On the average, it takes Federer 111.8 minutes, or just shy of two hours, to finish a match in the first four rounds of a Grand Slam.  It takes the next closest player (Roberto Bautista Agut) 113 minutes to finish one of these matches.  Novak Djokovic ranks ninth at about 125 minutes.

---

## Conclusion

To finish this post, I'll provide a quick summary of the above analysis, and then provide a few more comments.

In this post, I attempted to quantify what it is about the great players than set them apart from all other players.  The great players between 2006 and 2015 are most obviously Roger Feder, Rafael Nadal, and Novak Djokovic.  As I showed, Andy Murray sits in a strange place between these top three players and the rest of the crowd.  Murray has the consistency and skills of the top three players, and has consistently won more Grand Slam quarter-final and semi-final matches than the lower players, but has not won nearly the number of Grand Slams as Federer, Nadal, or Djokovic.

In my analysis, I looked at various statistics.  Across all players who achieved an average yearly ranking of 5 or higher, I showed that Federer, Nadal, Djokovic, and Murray were far more consistent in terms of their percent of matches won, percent of service games won, average percent of tournament advancement, and percent of return games won.  In the scatterplot of average percent of rounds won in all tournaments versus total matches won, I showed that Federer, Nadal, and Djokovic were clearly far above the rest of players.

In addition, I showed that among all players with at least 10 wins, Federer ranked number three in terms of double fault percentage, and Nadal ranked number three in terms break points conversion percentage.

Finally, I took at look at the average number of minutes a match takes for the first four rounds of Grand Slam tournaments.  Across all 103 players with at least 10 wins in the first four rounds of Grand Slams tournaments, Federer ranked as the most efficient player.

In this post, I showed what is it is about the great players that sets them part.  I took a quantitative look at various statistics, and showed that the four best players in men's tennis from 2006 to 2015 were incrediblely consistent.  This consistency, coupled with their skills, allowed them to dominate the sport.  It is the reason why collectively, these four players accounted for 36 out of 40, or 90% of, Grand Slam Champions between 2006 and 2015.






overall wins to offense to defense to which players are most effecient.  

overall win pct DONE
show avg pct rounds won line graph DONE

show histogram of everyone (DONE)
scatterplot of percent vs total wins
pct service games won
scatterplot service games won vs height
talk about double fault pct
line graph pct return games won
talk about pct break points converted





- fix david ferrer and maybe andy murry in some graph?

introduction 
link to other posts
discussion of great players between 2006 and 2015, which players are obvioiusly the great players
mention those who won the most tournamenets, novaks famous 2015 and fed's great 2006
clearly this subset of data is missing some years of tennis
players obviously those who player better and are more consistent.

go into histogram of avg pct of rounds won - who are the 3 outliers to the right
then 3 nice graphs
then scatterplot of service games won vs height
then random percentages

then show that federer playes the shortest matches in the first 4 rounds of majors

conclusion
