--- 
layout: post
title:  Interface Segregation Principle
subtitle : SOLID Principles 
tags: [Sample Post, Welcome to the Grape Theme!]
author: Avni Kothari 
comments : False
---

The interface segregation principle is the "I" in the SOLID Principles. Bob Martin defined it as "Many client specific interfaces are better than one general purpose interface." In other words, clients should not be forced to implement interfaces they don't use. Having one large, bloated interface with methods that implementing classes don't require can be dangerous for a few reasons. Firstly, to a client it can signal that an implementing class may have certain behavior, which may not be the case. So it can misrepresent your intent. Secondly, if the interface changes it can cause unncessary work for the classes that implement the interface, but don't need all the methods from that interface. The solution to this problem is to have your methods split up into multiple interfaces rather than having one large interface. 

Here's an example of a violation of the interface segregation principle

```java
public interface Animal {
  void fly();
  void walk();
}

public class Bird implements Animal {

  public void fly() {
    System.out.println("I am flying");
  }

  public void walk() {
    System.out.println("I am walking on a tree");
  }
}

public class Bee implements Animal {

  public void fly() {
    System.out.println("I am buzzing");
  }

  public void walk() {
    System.out.println("I am crawling on a flower");
  }
}


public class Dog implements Animal {

  public void fly() {}

  public void walk() {
    System.out.println("I am walking on my four feet");
  }
}
```

Here, `Dog` cannot fly and therefore, should not need to implement the `fly()` method. 

Instead we can split up the interfaces to be smaller and more representative of the classes that implement those methods. Below I've split the `Animal` interface into `FlyableAnimal` and `WalkableAnimal`. Therefore, `Dog` does not need to implement the `fly()` method. 


```java
public interface FlyableAnimal {
  void fly();
}

public interface WalkableAnimal {
  void walk();
}

public class Bird implements FlyableAnimal, WalkableAnimal {

  public void fly() {
    System.out.println("I am flying");
  }

  public void walk() {
    System.out.println("I am walking on a tree");
  }
}

public class Bee implements FlyableAnimal, WalkableAnimal {

  public void fly() {
    System.out.println("I am buzzing");
  }

  public void walk() {
    System.out.println("I am crawling on a flower");
  }
}


public class Dog implements WalkableAnimal {

  public void walk() {
    System.out.println("I am walking on my four feet");
  }
}
```

This is a simple principle to reduce duplication in your code and increase readability. 
