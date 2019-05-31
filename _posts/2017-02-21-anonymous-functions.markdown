---
layout: post
title:  Anonymous Functions 
date:   2017-02-21 11:00:00 -0600
---

There are a few ways to denote a function in Clojure. The most common way is something like this

```clojure
(defn merge-vector [v1 v2] 
  (into v1 v2))
```

This is a function that takes two vectors and returns a new vector that is the combination of the elements in both these vectors. For example, 

```clojure
=> (merge-vector [1 2] [3 4])
; [1 2 3 4]
```

This is actually short hand for this

```clojure 
(def merge-vector 
  (fn[v1 v2] (into v1 v2)))
```

Which can then be invoked in the same way as above

```clojure
(merge-vector [1 2] [3 4])
```

Anonymous Functions are a way to denote a function. The general notation of `(fn [params*] body)` can also be written `#(...)` which is called literal notation. So we can rewrite the above function like this

```clojure
(#(into %1 %2) [1 2] [3 4])
; [1 2 3 4]
```

Where `%1 %2` denotes the that there are two arguments. When only argument is passed in it is convention to use `%` instead of `%1`. 

Anonymous Functions can be useful to use in higher order functions like `map`, `reduce`, `filter`

Lets say we wanted to find the remainder of all elements in a vector when the elements are divided by 3. It can be concisely written with an anonymous function.

```clojure
=> (map #(mod % 3) [6 7 8])
; (0 1 2)
```

Here's another example with `filter`

```clojure
=> (filter #(zero? (mod 15 %)) (range 3 15 2))
; (3 5)
```

If we take this by steps: 

```clojure
; returns numbers 3 to 15 (exclusive) by 2
=> (range 3 15 2)
; (3 5 7 9 11 13)
```

```clojure
; finds the numbers that are factors of 15 and returns a lazy sequence of those factors
=> (filter #(zero? (mod 15 %)) (3 5 7 9 11 13))
; (3 5)
```

Both literal notation and the long hand way of `(fn [params*] body)` to denote an anonymous function are frequently used. 

 
