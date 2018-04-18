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
  
  
  
  ## Calculating Player Stats per Year
  
  
  
  ## Calculating Player Stats Overall
