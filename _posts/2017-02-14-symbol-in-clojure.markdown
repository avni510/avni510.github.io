---
layout: post
title:  Symbols in Clojure
date:   2017-02-14 15:40:00 -0600
categories: Clojure Symbols
---

Symbols in Clojure are frequently used. Clojure.org states that symbols are used to "refer to function parameters, let bindings, class names, and global vars". Essentially symbols are a piece of data. Symbols are not storage locations, but instead they are used to refer to the value of a variable. It can also be said that symbols are used as identifiers within the language. 

Here are a few examples of symbols:

```clojure
(defn get-board [board-vector]
  board-vector)
```

`board-vector` is a symbol and it refers to the value passed into the function `get-board`

```clojure
(let [x 1] (println x))
; => 1
```

`x` is a symbol that is bound to the value of 1 inside the let statement. 

```clojure
(def person {:name "avni" :age 23})
; => user/person
person
; => {:name "avni", :age 23}
```

The first line denotes a global var, person, which was created in the user namespace. The next call, `person`, is a symbol that refers to the global var `person` which is a hash-map.

```clojure
(* 8 10)
; => 80
```

Here `*` is a symbol that refers to the function `*`.

You can stop the evaluation of symbols but using a quote before them like this

```clojure
'hello_world
=> hello_world
```
Symbols are scattered throughout the Clojure language and are reference points to pieces of data.
