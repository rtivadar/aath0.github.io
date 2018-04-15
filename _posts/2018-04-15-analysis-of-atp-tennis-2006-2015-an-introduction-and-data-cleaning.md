---
layout: post
title: "Analysis of ATP Tennis, 2006 - 2015: An Introduction and Data Cleaning"
author: "Ethan Wicker"
categories: journal
tags: []
image: mountains.jpg
---

# Introduction

This post will the first in a short series covering an analysis of ATP (Association of Tennis Professionals) Tennis matches.  

Thanks to Jeff Sackmann (insert link to github here), I was able to get my hands on a plethora of professional tennis data.  In short, 
Jeff has player and match statistics on just about every ATP match going back to 1968.  He has also data on many WTA (Woman's Tennis Association)
matches as well as many Challenger level matches.

For my analysis, I looked at the subset of ATP matches covering the 10 year span from 2006 to 2015.  In future posts, I may expand on this
to cover more years, and especially more recent years.

In this post, I will briefly explain the structure of data, and then I will explain my methodology and rationale for wrangling and cleaning
the data.  In future posts, I will reference the cleaned data from this post, as well as the R code.

During this post and subsequent posts, I will attempt to walk the line between breivty and clear explanations.  That is, 
I will try to interest those who are interested more in my methods, as well as those who are more interested in my results.

As a last comment, I have attempted to my make code incredibly readable and easy to follow.  I have leaned on the coding mantra that
clear code is greater than clever code, in hopes that anyone can easily follow my analysis.  In places, this has lead to some rather long
(but clear!) variable names.

