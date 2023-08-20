+++ 
draft = true
date = 2022-06-07T20:50:51-05:00
title = "Using cycle time"
description = ""
tags = ["improvement", "delivery-flow"]
categories = ["improvement"]
series = []
+++

I find looking at the cycle time scatterplot one of the easiest ways to trigger
learning and improment conversations.

A typical chart shows all the work items a team finished in a given period along
with the percentiles lines. Without doing any fancy math, the work items above
the 95th percentiles are a great place to look and initiate conversations to
see if we can learn something from those work items.

That isn't the most important though, a team will always have a few work items
that are going to take much longer that others.

One of the biggest benefits of this visualisation for me is the ability to
quickly identify how consistent (or not) the time to finish something is. Not
that the goal is to be consistent, but instead to understand if the work items
are broken down in similarly enough small chunks.

When you see a chart with bubbles all over the place, it is very likely that you
have some work ahead of you.

![bubbles everywhere](/images/cycle-time-everywhere.png)

That is how all over the place could look like, if you find yourself with a
chart like that, it is time for a team conversation about the work items and
differences between them.

The outcome of that conversation will be to collective learn the theme or reason
driving the disparity of time to complete between the work items. The output
could be a set of action items to help reducing such disparity.

After a few weeks you should start seeing some differences in the chart. I
consider something like the chart below consistent.

![bubbles consistent](/images/cycle-time-consistent.png)

Look frequently at this data point as a team, you will have endless opportunites
improve your delivery flow. :smile:

As I just mentioined, this is **a data point**, the conversations you have using
this indicator will likely lead you to start tracking more specific metrics
about the areas of improvement. These might be:

1. Team health and morale
1. Tracking waste time (any column that says "ready for" or other processes)
1. Monitoring code and test churn more closely
1. Analyse the culmulative flow chart (CFD, similar to 2)
1. _many others..._

I won't get on how to improve here because that is completely dependent on your
context. I hope this is a perspective you can leverage in your team.

## What not to do

- Only one data point
- Does not indicate you are building the right thing
- It should be looked within a context
- It should not be compared to other teams
