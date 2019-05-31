---
layout: post
title:  Comparisons of Middleware 
date:   2017-06-06 09:00:00 -0600
---
A few weeks back I had a [blogpost](http://avni510.github.io/middleware/2017/05/19/what-is-middleware.html) that briefly defines middleware. For this post I wanted to go a bit further into middleware, how I used it, and different ways it can be used. 

### How I Used It

I used middleware for returning the appropriate `Handler`. A class that implements the `Middleware` interface has a `call` method on it and takes in a `Request` and returns a `Handler`. I created a stack like structure to find the `Handler` for a specific route. 

The first class on the stack is called `RoutingMiddleware` and that finds a routes that matches with what was initially populated in my `ConfigurationRoutes` class (which is invoked in `Main`). If it does not find that route it passes the request onto to the next class called `FileMiddleware` which looks to see if the request route matches a file in the specified directory. If it does not find the route it passes the request onto to the next class called `FinalMiddleware` which returns a 404 Not Found error. 

A benefit of this pattern is that the Middlewares can be shuffled around if new requirements arise. Another advantage is you can add additional logic if need be - ex: having a middleware that checks if the user is authenticated and returning a "404 Not Found" if they are not or continuing down the chain if they are authenticated. It also allows for an easy separation of responsibilities. For example, my `FileMiddleware` lazily loads the file routes if a request is made to a file that exists in the directory. All the files are not prepopulated when I initially add all my routes at the top level. 

Here's an example from my server where I use Middleware: 

```java
public interface Middleware {
  Handler call(Request request) throws Exception;
}

public class RoutingMiddleware implements Middleware {
  private Middleware app;
  private Router router;

  public RoutingMiddleware(Router router, Middleware app) {
    this.router = router;
    this.app = app;
  }

  public Handler call(Request request) throws Exception {
    Handler handler = router.retrieveHandler(request.getRequestMethod(), request.getUri());
    if (handler != null) {
      return handler;
    } else {
      return app.call(request);
    }
  }
}
```

Here the next middleware is injected into `RoutingMiddleware` and is called if the handler was not found in the initial routes that were populated (`retrieveHandler` does this).


### Clojure Ring Middleware

There are also other ways to use middleware. For example, Clojure Ring implements middleware. They have each handler take in a request and return a response, the response only contains 3 parts - status, headers, and body. 

Here's an example from their [wiki](https://github.com/ring-clojure/ring/wiki/Concepts) 

```clojure
(defn what-is-my-ip [request]
  {:status 200
   :headers {"Content-Type" "text/plain"}
   :body (:remote-addr request)})
```

The middleware is in charge of adding additional functionality to a handler. They help build up a handler. The first argument must be a handler. They return a new handler function that calls the original handler and adds some logic to the response returned by the original handler.

Here's another example from their wiki

```clojure 
(defn wrap-content-type [handler content-type]
  (fn [request]
    (let [response (handler request)]
      (assoc-in response [:headers "Content-Type"] content-type))))
```

In the example above, all responses are returned with the header set to the content type passed into `wrap-content-type` (ex: "text/html") for the specific handler passed into the function (ex: `what-is-my-ip`). The function then takes in a request and returns a response. 

Here's an example of how it's used. 

```clojure
(def request 
  {:request-method :get
   :uri "/"
   :headers {"Host" "localhost"}})

(def new-handler
    (wrap-content-type what-is-my-ip "text/html"))

(new-handler request)
```

This returns a response that looks like this

```clojure
  {:status 200
   :headers {"Content-Type" "text/html"}
   :body (:remote-addr request)})
```

Although this example is quite contrived it shows a different way in which middleware can be used. Overall, I found it interesting to read about how Clojure Ring used middleware and comparing it to my server project. 

