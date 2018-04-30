---
layout: post
title: "ATP Tennis Analysis, 2006 - 2015: Additional Questions and Thoughts for Future Analyses"
author: "Ethan Wicker"
categories: journal
tags: [tennis, data analysis, data frames]
image: tennis-tsonga.jpg
---

## Introduction

This is the last post in a series covering an analysis of ATP (Association of Tennis Professionals) tennis matches between 2006 and 2015.  You can find the first four posts in this series at the following links:

* [ATP Tennis Analysis, 2006 - 2015: An Introduction and Data Cleaning](https://ethanwicker.github.io/journal/atp-tennis-analysis-2006-2015-an-introduction-and-data-cleaning.html).
* [ATP Tennis Analysis, 2006 - 2015: Constructing Useful Data Frames](https://ethanwicker.github.io/journal/atp-tennis-analysis-2006-2015-constructing-useful-data-frames.html)
* [ATP Tennis Analysis, 2006 - 2015: Do Lefties have an Advantage?](https://ethanwicker.github.io/journal/atp-tennis-analysis-2006-2015-do-lefties-have-an-advantage.html)
* [ATP Tennis Analysis, 2006 - 2015: What Makes the Greats so Great?] (https://ethanwicker.github.io/journal/atp-tennis-analysis-2006-2015-what-makes-the-greats-so-great.html)

In the previous posts, I summarize how I cleaned the original data set and organized it into various useful data frames of player statistics.  I then used these data frames to look at whether or not left-handed players have an advantage, as well as what separates the great players from everyone else.

In this post, I'll be discussing some additional questions I still have, as well as some thoughts for future analyses.  After moving on to other projects for a bit, I am hoping to come back and answer some of the below questions.  

So, in no particular order, below are three ideas for some future tennis analyses.  After the three ideas are some additional questions related to my analysis of left-handed players.

---

## Thoughts for Future Analyses

* Do players from certain countries or regions play better on certain surfaces?

This question is probably the next one on my list, partially because it'll give me a reason to play around with the `ggmap` package.  Aside from that, I imagine this question is one on many tennis fan's minds.  This question comes steeped in the sterotype of the Spanish clay court specialists, whose high-spin game is well suited to the slower clay.  To look at this question in more detail, only hard courts and clay courts would likely result in statistically significant findings.  From my own ancedotal experience, I doubt grass courts, due to there rareness, would favor players of one nationality or another, but perhaps I could be wrong.

* Do taller players have an advantage?  Is there an optimal height?

This question is a bit of a two-parter.  The first part would examine whether or not taller players have any sort of advantage.  The obvious thought is yes, and their almost certainly do when serving the ball, but perhaps their tall size limits their mobility around the court.  Maybe a more interesting question would be to try and determine if their is an optimal height for men's professional tennis players.  For reasons just stated, being too tall has it's disadvantages, and being too short is almost certainly a disadvantage as well.  So there is likely an optimal height, and if I had to guess, I would bet it's likely between about 6'0" (183 cm) and 6'5" (196 cm).  For reference, Roger Federer and Rafael Nadal both stand at 6'1" (185 cm), while Novak Djokovic is 6'2" (188 cm) and Andy Murray is 6'3" (191 cm)

* Do younger players perform better in 5-set matches than older players?

In most tournaments, matches are played best out of 3 sets.  However, in the four Grand Slam tournaments, matches are played best out of 5 sets.  So, it would be interesting to look at these matches to attempt to see if younger players have any sort of advantage in these longer matches where youthful endurance might help.  On the flip side of this, perhaps older players with their experience would have the upper hand here.

---

## Additional Questions from Leftie Analysis

In addition to the thoughts for future analysis above, I also have follow-up questions about my analysis of whether or not left-handed players have an advantage.  Between the years of 2006 and 2015 at least, the data supported the conclusion that left-handed players do not have an advantage over right handed players.  

This result kind of goes against common thought.  In most sports, it's pretty well agreed that playing left-handed has it's advantages, and this is typically attributed to the rareness of a left-handed player.  And this advantage is likely true at most levels of play for most sports.

At the end of my post on that topic, I hypothesized a reason for why left-handed players do not have an advantage.  In short, my hypothesis was that left-handed players do have an advantage in lower tiers of men's tennis.  Do to this slight advantage, they win matches they maybe shouldn't, and their skill level begin to lag behind similarly ranked right-handed players.  This culminates at the very elite level of men's tennis, the ATP World Tour.  At this level of the game, these left-handed players, who got their slightly due to their advantage, are playing the best right-handed players in the world.  And since there is this slight gap in skill level, right-handed players win a higher proportion of their matches than left-handed players.

This hypothesis brings up two interesting questions: 

* Is there a leftie advantage at lower tiers of the game, such as Challenger or Futures level events?  

* If there is a leftie advantage, does it increase with the lower the level of play?

Depending on the answer, this could provide support for my hypothesis, or it could tear it apart.  If my hypothesis has any truth behind it, we would expect to see a slight advantage at the Challenger level, and a larger advantage at the Futures level.  Perhaps this could also be extended to junior level matches, but data for this matches isn't readily available.

I have one last additional, somewhat related question:

* Has the leftie advantage changed over time?

This last question would be very interesting to look at, and it would encompass a large part of tennis history.  As the sport and racquet technology has evolved, the level of play has also evolved with it.  The players today are likely in better shape, have better support staff around them, and certainly have better technology.  All in all, one could make an argument that today's players are better than their counterparts of 20 or 30 years ago.  If this were true, perhaps any supposed left-handed advantage among the elite players would have been greater in the 1970s or 1980s than it is today.  Futuremore, it would be interesting to see if the degree of the advantage has changed over time as well.
