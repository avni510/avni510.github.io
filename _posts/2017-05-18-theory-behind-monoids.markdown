--- 
layout: post
title:  Theory Behind Monoids
date:   2017-05-18 11:20:00 -0600
categories: Functional Programming
---

<!--
Monoid is a system that statisfies these three rules
* Monoids must satisfy these rules 
  - closure
  - associativity
  - identity

a collection of things (or a set) AND the function compose a Monoid, cannot be one or ther other. Ex: integers under addtion or strings under concatenation
-->

This blogpost covers a brief background on the formal definition and theory behind Monoids. It by no means comprehensive or math intensive, but is more so a brief introduction to monoids. A monoid is a mathematical term, but is a practical pattern in functional programming. 

It is a system that satisfies these three rules:

**Closure** - Given a function with parameters, the type of the parameters and the type of the return value are the same 

An example is the `+` function
```clojure
(+ 1 2)
; => 3
```
It takes integers and returns another integer

Another examples is the `or` function
```clojure
(or true false)
; => true
```
It takes in booleans and returns a boolean

The fact that a function's parameters and the function's return value is of the type same is known as closure

**Associativity** - Given that your function abides by the closure property, the order of function operation does not matter, the same value will be returned

Back to the `+` example
```clojure
(+ (+ 1 2) 3)
; => 6
```
Is the same as
```clojure
(+ 1 (+ 2 3))
; => 6
```

And with the `or` function 
```clojure
(or (or true false) false)
; => true
```
Is the same as
```clojure
(or true (or false false))
; => true
```

**Identity** - Given that your function abides by the closure property, there exists some value such that when you combine it with any other value you get back the original value as if nothing happened

`+`  example:

```clojure
(+ 0 5)
; => 5

(+ 0 7)
; => 7
```

Here our identity value is 0

`or`  example:
```clojure
(or true false)
; => true

(or false false)
; => false
```
Here our identity value is false because given another value combined with false we get back the original value. 


To summarize, a monoid must abide by these 3 rules. And to clarify, a collection of things (or a set) AND the function make up a monoid. By definition a monoid can't be one or the other. Here are a couple of examples of monoids: integers under addtion or strings under concatenation.

