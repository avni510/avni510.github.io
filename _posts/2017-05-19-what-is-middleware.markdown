---
layout: post
title:  What is Middleware?
date:   2017-05-19 10:00:00 -0600
categories: Middleware
---

Middleware connects two parts of your application together and passes data between them. It can be thought of as the glue for your application. A common example is Rack middleware. Rack is an interface between webservers supporting Ruby and Ruby frameworks. Rails and Sinatra are a couple of frameworks that use Rack by default to communciate with the webservers. It orders Ruby modules and places them in a stack like structure for the final web application. Rails creates modules for dispatching requests, session handling, or parsing parameters and specifies an order that Rack understands. 

For my HTTP server, written in Java, I used a similar pattern to Rack's middleware to handle the routing. I created a stack like structure which takes in a request and returns a response. The first class on the stack is called `RoutingMiddleware` and that finds a routes that matches with what was initially populated in the `Main` class of my program. If it does not find that route it passes the request onto to the next class called `FileMiddleware` which looks to see if the request route matches a file in the specified directory. If it does not find the route it passes the request onto to the next class called `FinalMiddleware` which returns a 404 Not Found error. 
