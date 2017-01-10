---
layout: post
title:  Cookies and Sessions
date:   2017-01-09 15:02:00 -0600
categories: Cookies and Sessions
---

Hyper-Text Transfer Protocol (HTTP) is a stateless protocol. A stateless protocol is when, from the point of view of the application, each request sent from the browser to the server is independent of the previous request sent. For example, lets take my time logger app. The homepage is like a login page. It asks the user to select their username. The next page is a menu selection page. This allows the user to select which action they would like to complete (logging time, running a report, etc.). After the user has selected their username, the next page - menu selection will not have any recollection of the username entered in the previous page. This is because HTTP is stateless. 

The stateless nature of HTTP protocols led to the creation of cookies. Cookies are stored on a user's computer by a web server through the browser. Every time you open a webpage cookies are sent back to your browser. These cookies are stored in a hash-like structure that store information such as user preferences and user authentication. These cookies can then be used across multiple HTTP requests. Sinatra allows a developer to set and access these cookies through their controller through something called sessions. The length of a session for my application would be when the user selects their username (when the log in) till they exit the application (when they log out).
