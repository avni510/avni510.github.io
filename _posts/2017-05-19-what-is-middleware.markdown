---
layout: post
title:  What is Middleware?
subtitle : An Explanation 
tags: [Sample Post, Welcome to the Grape Theme!]
author: Avni Kothari 
comments : False
---

Middleware connects two parts of your application together and passes data between them. It can be thought of as the glue for your application. A common example is Rack middleware. Rack is an interface between webservers supporting Ruby and Ruby frameworks. Rails and Sinatra are a couple of frameworks that use Rack by default to communciate with the webservers. It orders Ruby modules and places them in a stack like structure for the final web application. Rails creates modules for dispatching requests, session handling, or parsing parameters and specifies an order that Rack understands. 

