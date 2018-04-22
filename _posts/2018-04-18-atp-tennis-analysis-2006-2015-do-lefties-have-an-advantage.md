---
layout: post
title: "ATP Tennis Analysis, 2006 - 2015: Do Lefties have an Advantage?"
author: "Ethan Wicker"
categories: journal
tags: [tennis, data analysis, data frames]
image: tennis-nadal-serving.jpg
---

## Introduction

This post is the third in a series covering an analysis of ATP (Association of Tennis Professionals) tennis matches during the 10 year span between 2006 and 2013.  You can find the first two posts in this series at the following links:

* (INSERT LINK)
* (INSERT LINK)

In this post I will be discussing whether or not left-handed players have an advantage over their right-handed counterparts.  To provide some context, it has long been believed that lefties have an advantage over righties in various sports, and tennis especially, is no exception.  

In tennis, there are various reasons why left-handed players are thought to have an advantage over right-handed players.  A crucial reason, if not the largest advantage lefties might possess, is that service games in tennis always start in the deuce court (right side) and then serves alternate from there, switching between the ad court (left side) and back to the deuce court until the service game is over.  What this means, is that more often than not, and everytime the score of a service game is 40-40 (simply called deuce), the last service of the service game will come from the ad court. And, due to the manner in which the net in tennis is pulled down from the middle, coupled with how players mechanical serve to produce spin, should provide an advantage to lefties.

Another typical reason lefties are thought to have an advantage - this one less tennis specific - is just that there are less of them.  They're just a rarer breed.  And since players typically play right-handed players, when they face the occasional unicorn of the left-handed player, they're thrown off their game.  For a more tennis exact rational of this, this often has to do with both tennis strategy, and the spin of the ball coming off different shots.  In short, when playing a leftie, a player has to flip their strategy upside down.  All of a sudden, hitting a backhand approach has to be hit to the opposite side.  And as for the spin, tennis players rarely hit perfect 6-o'clock-to-12-o'clock spin on the ball.  Often times that spin has a little or a lot side spin mixed in as well - think 5-o'clock-to-11-o'clock for a right-handed forehand.  This spin completely changes how the ball flies through the air, how the ball bounces, and how potentionally, how a player has to redirect a ball to counter the spin.

This only scratches the surface of the advantages and disadvantages of handediness in tennis, but if you were unfamiliar with the nuances of the sport, it provides you with enough information to see the legitimacy in the question.

For the remainder of this post, I'll be describing my methods and my results, as well as including and discussing various segments of my R code.  I'll be discussing some smaller topics in each section, such as if lefties serve more aces or win more a higher percentage of service games.  Towards the end of this post, I'll perform some more indepth statistical analysis (i.e. a Two Proportion z-Test and a Chi-Squared Test of Independence) and I'll summarize my results.

So, do lefties really have an advantage?

The answer: No.  Not a bit.  A big resounding no.  If anything, they lose more matches than they should.  Which is curious.  

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

Furthermore, it's worth checking a couple of conditions before diving into the bulk of my analysis.  The first condition worth checking is to ensure that there is a relatively even distribution of left-handed players and right-handed players in the data set.  If for instance, lefties really did have an enormous advantage, and there were a disporptionionate amount of them in the lower ranks, that could throw off some of my later analyses.  

```r
# Making sure lefties are uniformly dispersed across players
atp_stats_overall_by_player_left_right_known %>% ggplot(aes(x = hand, y = avg_rank, color = hand)) + 
  geom_boxplot() +
  coord_flip()
  ```
