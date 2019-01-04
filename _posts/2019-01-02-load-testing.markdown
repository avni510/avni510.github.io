---
layout: post
title:  Load Testing 
date:   2019-01-01 07:04:00 -0600
categories: Software 
---

- What is Load Testing? 
  * Test how many users your server can support
  * A Test that sends many concurrent requests
  * Works by applying "stress" to the serverA
  * It helps to point out the weaknesses in your system
  * From there you can derive how many users you can support
  * When weaknesses are found, you can fix those weaknesses and find the next bottleneck to help support more users
  * There can be many different types of goals:
    - how many users can your test support
    - What is your average response time
    - Where are the weaknessess in the system to handle more "load"
- How we did it? 
  * Steps
    * Get breakdown of different types of requests in a span of a week or hour or minute (depending on how much traffic you have), percentage breakdown
    * Find how many requests are in the peak minute or second of traffic
    * Build out a test that encompasses those different types of requests (each agent represents a set of all those requests)
    * Generative testing approach
    * Decided to use Chaperon
    * Create a separate environment where load testing can be performed
    * Run the test, ramp up the number of agents
    * Decide the threshold of request time to support max capacity
    * Calculate the number of requests per minute
    * From there we can determine how many users to support

Directions
* Steps to perform load testing
* Strategies and libraries we used for load testing
* Insights learned from Load Testing

On my current project, we had a desire to see how many users our system can support. So we decided to build out a test that allows us to do that. This was my first time doing
something like this, and I wanted share our approach for solving this problem. Load testing works by applying a certain amount 
of "stress" to the server and seeing how the server preforms. The "stress" in our case was to send many concurrent requests to our server and see the statistics captured by the test.
There can be many goals to the purpose of performing a load test. A few of them are seeing how many users your system can support, the average response time of an HTTP request, and weaknesses 
in your system to support a higher load of users. Load testing provides a nice feedback loop, and a cadence often follows from it run the test, the test points out the weaknesses in your system, 
the team works on fixing that weakness, and you start the cycle again (show diagram). This cycle is really nice because it provides you instant feedback and helps point out the weaknesses instead
of waiting for a large amount of users to hit your server. So how do you get to writing a good load test that provides this feedback loop? This post will outline the steps to creating a load test. 

### Step 1: Breakdown HTTP Requests
Over a meaningful span of time find out the different types of requests that are sent to your server. It depends on how many users your system is currently supporting to figure out the right timeframe.
It could be over the span of a second, minute, hour, or week, whichever gives you an accurate percentage breakdown for the requests your server is receiving. If you currently don't have the tooling
to do this, it's probably worth spending some time building out your analytics and monitoring capabilities before proceeding with load testing. Once this is built out it will help substainially 
with the rest of load testing.

### Step 2: The Peak Minute
Find out how many requests are made during the minute or second. Determining whether to choose the timeframe of minute or second again depends on how many users you are currently supporting. If
second doesn't provide you with much meaningful data then the peak minute may be a better option.

### Step 3: Determine Your Tool
Decide which load testing tool will best suit your needs. There are number of tools on the internet for this. We decided to use an Elixir library called `Chaperon`. We chose this tool so we could 
handle more specific customization in our test and have the flexibility of writing our test with code.

### Step 4: Build out the Test
This step is probably the most time consuming part. You'll want to build out your test such that it represents the breakdown of HTTP requests found in step 1. The test should be written such that
you can ramp up the number of agents where each agent simulates a user by performing a variety of HTTP requests (determined by step 1). This can be tricky. In particular we had to think about how
we wanted to handle requests that were time dependent and related to authentication. Another layer of testing that we decided to add was generative testing. We used a library called `StreamData` to
help us with it. It works by defining a set of rules that your "generator" will conform to and then it'll output data. An example is a datetime generator which will randomly select a datetime. I think
it's important to note that the test should be designed in order to support each agent be run concurrently. 
