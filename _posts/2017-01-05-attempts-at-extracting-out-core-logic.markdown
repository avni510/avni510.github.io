---
layout: post
title:  Attempts at Extracting Out Core Logic 
date:   2017-01-04 16:16:00 -0600
categories: Web App
---

Yesterday was my second day working on attempting to convert my command line app to a web based version. I had a chance to learn more about how Sinatra works. Specifically, the testing frameworks that I should use are Rspec and Rack::Test. Rspec for unit testing purposes and Rack::Test for sending fake HTTP requests and verifying the response received. I was also able to get further into ERB templating and the different types of tags - subsitution and scripting tags. This led me to discover how to name and store key value pairs in the params hash. Towards the end of the day I started thinking more about how to reuse my existing code base for the time logger and just make modifications for a web based version. The idea is to decouple as much as possible my code base from the command line UI. Kevin hinted at attempting to come up with a more functional approach where a function outputs either a success or failure and the command line or web interface will process the success or failure in different ways. My existing time logger will need to be refactored for this idea to be implemented. But after this implementation I will be able to reuse the bulk of my code. 


