---
layout: post
title:  Dependency Inversion Principle
date:   2017-06-06 09:00:00 -0600
categories: SOLID principles
---
This stands for the "D" in the SOLID principles. Formally the definition states: "High level modules should not depend on low-level modules, instead both should depend on abstractions" and "abstractions should not depend on details"

So this means the abstraction layer should between the high level and low level classes, visually it would look something like this: 

```
high level classes -> abstraction layer <- low level classes
```

This allows flexibility for changing out implementation details, and extensibility within in your application. Lets take a look a simple example related to my HTTP server.  

```java
public class HelloWorldHandler {

  public Response generate(Request request) {
    // returns an HTTP Response with the body set as Hello World
  }
}

public class Router {
  
  public Response getHttpResponse(HelloWorldHandler helloWorldHandler, Request request) {
    return helloWorldHandler.generate();
  }
}
```

`getHttpResponse` is tightly coupled to the specific implementation of helloWorldHandler

The problem with this design is that if I want to add more handlers the `getHttpResponse` method only takes in the `HelloWorldHandler`. Here's a way that we can fix that.

```java
public interface Handler {
  Response generate(Request request);
}

public class HelloWorldHandler implements Handler {

  public Response generate(Request request) {
    // returns an HTTP Response with the body set as Hello World
  }
}

public class ErrorHandler implements Handler {

  public Response generate(Request request) {
    // returns an HTTP Response with a 404 Not Found as the status code
  }
}

public class Router {
  
  public Response getHttpResponse(Handler handler, Request request) {
    return handler.generate();
  }
}
```

We can create an Interface that all Handlers implement, and change the first argument type of `getHttpResponse` to be the interface `Handler`. This allows for new Handlers to be created and used and it also allows for all classes that implement the Handler interface to be switched out in the `getHttpResponse` method. 

Both high level and low level classes are dependent on an abstraction in this example. `Router` is the high level class which is dependent on only the abstraction, not the specific implementation details of the lower level classes. 

By having our `Router` class depend on an abstraction rather than specific implementation details the function `getHttpResponse` is reusable. 

