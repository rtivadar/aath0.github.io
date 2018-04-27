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

The Analysis







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
