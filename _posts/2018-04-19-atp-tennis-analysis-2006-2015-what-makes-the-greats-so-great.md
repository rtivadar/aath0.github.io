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

* (INSERT LINK)
* IL
* IL

(INSERT BIT HERE about how you may want to read those first, and that i will be referencing data frames what those posts.  i'll include R code below)

SPELL CHECK EVERYTHING


In this post, I'll be discussing what separates the greatest players in the game from all the other players.  I'll attempt to quantify what is it about these few players that uniquely sets them apart from all the other incredibly skillful tennis players in the world.

Before I begin, I should add some context and clarity.  This data analytics series on ATP tennis matches only covers the years 2006 to 2015.  In the future, I'll likely expand and revise to cover a larger span of years.  However, for now, I should make it clear that my analysis will really only be appropriate for those years.  However, the general traits of great players I uncover can absolutely be applied to other similar years.  In the past 20 or so years, as racquet technology has increased and the game has evolved, what makes a player great has also evolved.  As such, the general traits of great players I lay out can be generalized to similar years.  Clearly however, these traits and characteristics cannot be extrapolated to make conclusions about the game of tennis in the 1970s or 1980s, for example.

Clearly, it doesn't take a detailed analysis to tell any tennis fan which great players I am talking about.  Over the past 15 years of the game, 3 or 4 players have absolutely dominated the landscape of men's professional tennis.  Roger Federer, Rafael Nadal, Novak Djokovic, and, to a slightly lesser extent, Andy Murray, have been the irrefutable best players in the world over their consecutive careers.  

To make this clear, between 2006 and 2015, the trio of Federer, Nadal, and Djokovic won 34 out of the 40 Grand Slams (also know as Majors) played.  That's 85%.  Theres no denying that these three players outpaced everyone else.  

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

As you can see, although Murry won the same amount of majors as Stanislas Wawrinka, he's won far more semi-final and quarter-final matches than any other lower player.  It is for this reason, as well as some we'll see farther along in this analysis, that I'll be including Murray as one of the great players between the years of 2006 and 2015.

Not surpisingly, what makes a tennis player great is to win often, and to win consistently.  

In the remainder of this post, I'll be analysis and discussing some specifics about these great players and what allows them to win often and consistently.  I'll be looking at some more specific statistics, and I'll also be looking at trends over time.  Finally, I'll look at average match time for the first few rounds of a major to see if any of these players are particularly efficient to save energy.

---

## The Analysis

In this data analysis, I'll be discussing and displaying various data visualizations.  In particular, looking at the data here helps us see trends, and helps us see just how much more consistent the top tennis players were between 2006 and 2015.  

This analysis will roughly be divided into three sections.  The first section will deal with match wins and tournament advancements, the second section will deal with the offensive side of the game, and the third section will deal with the defensive side of the game.

Throughout much of the below analysis, I subsetted my visualizations to include only players who for any year, were ranked in the top 5 players, by yearly avearge ranking.  This was done to make the trends in the data easier to see, and also for direct comparison between the great players and the next tier down of really good, but not consistent players.  As you'll see in the line graph belows, for players with an average ranking at or above the top 5 in the world in consectutive years, there are lines drawn between their data points.  On the contray, for players who sparacticaly were ranked in the top 5 players in the world, their statistics are just represented as singulary data points.

### Match Wins and Tournament Advancement

(CHANGE WORDING A BIT SINCE REORGANIZED)
 
#### Percentage of Matches Won

In this section of my analysis, I'll be looking at overall trends among players, both in terms of the percentage of matches won for an entire year, and the average percentage of rounds a player advances through a tournament.

To begin, below is a line graph of a player's percent of matches won versus year.

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
  labs(title = "Average Percent of matches won for Top 5 Players Per Year, 2006 - 2015",
       x = "Year", 
       y = "Average Percent of Matches Won")
```
(ADD GRAPH)

As you can see, various players are in and out of the top 5 world rankings throughout the years.  In total, 12 different players had an average ranking of 5 or higher for any given year.  As for the great players of Federer, Nadal, Djokovic, and Murray, only Federer is ranked in the top 5 throughout all 10 years.  Dkojokic and Murray, whose average ranking enters into the top 5 in 2008 and 2009 respectively, also do not leave for the reminder of the years.  Nadal was the only great player to drop out of the top 5, after a string of bad performances in 2015.

Looking at the trends in the graph more closely, notice the consistency of Federer from 2006 to 2012, in which his match win percentage never dropped below 80%.  Perhaps the most incredible statistic in this data set as well, is that in the 2006 season, Federer won 94.7% of his matches. The next closest match win percentage is Novak Djokovic in 2011, where he won 94.0% of his matches.  

Another interesting trend here is Djokovic's continuous rise in match win percentage from 2012 to 2015.

To look at percentage of matches won in more detail, below is a histogram of average percentage of matches won between 2006 and 2015.  To make the histogram more meaningful as well as more readable, I filtered the data to only include players with atleast 10 match wins.

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
(ADD HISTOGRAM - make sure correct one)

This histogram shows just how far above all other players Federer, Nadal, and Djokovic are.  The right most bar of the histogram represents three players.  These players are Federer, Nadal, and Djokovic, with overall match win percentages of 84.7%, 84.3%, and 83.7%, respectively.  The bar second from the right representing one player is Andy Murray, with a match win percentage of 76.3%.  The player represented by the bar to the left of Murray is Andy Roddick, with a match win percentage of 73.1%.

#### Average Tournament Advancement

Next, below is a similar line graph.  This line graph plots the average percent of rounds a player won in across all tournaments for a year on the y-axis, and year on the x-axis.

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

(INSERT GRAPH)

As you would expect, the trends here are pretty similar to the first line graph showing percent of matches won.  Federer and Nadal both bounce around 75% for much of the data.  This means, that for those years, both these players tended to advance about 75% of the way through a tournament before losing out.  Andy Murray is consistently below Federer, Nadal, and Djokovic in just about every year.

A couple of interesting observations with this plot are Federer's 2006 season and Djokovic's 2015 season.  These seasons are commonly held as two of the most dominate season in men's professional tennis, and for good reason.  In these years, Federer and Djokovic advanced through 90.3% and 90.8% of tournament rounds, respectively.  These were the highest such averages for the data set.

From the previous two line graphs, we've seen that Federer, Nadal, Djokovic, and Murray win more matches than every other player on tour, and also do so much more consistently.  Out of the remaining eight players that make an appearance in the graphs, only Nikolay Davydenko had consecutive years with an average ranking of 5 or higher.










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
