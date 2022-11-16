---
layout: post
title:  Lazy Sequences
tags: [Sample Post, Welcome to the Grape Theme!]
author: Avni Kothari 
comments : False
---
Lazy sequences are powerful tools in Clojure. But before we dive further into lazy sequences I want to briefly go over the differences between a collection and sequence in Clojure. 

#### **Collections**
* Types of collections: vector, list, hash-map
* Immutable
* Functions that can be used on them: count, conj, seq, first rest, cons
* Cannot be infinite 

#### **Sequences**
* A walkable list abstraction for collections and can be sequentially traversed 
* Immutable                                                                     
* Functions that can be used on them:  first, rest, cons                        
* Can be infinite                                                               

Sequences can be evaluated "lazily". And here's a few examples of functions that return lazy sequences. 

```clojure
(take 5 (range))
; => (0 1 2 3 4)
(class (take 5 (range)))
; => clojure.lang.LazySeq
```

```clojure
(map inc [1 2 3 4])
; => (2 3 4 5)
(class (map inc [1 2 3 4]))
; =>clojure.lang.LazySeq
```

The first line of each of the code examples above call a function that return lazy sequences. The second call verifies that these functions do in fact return lazy sequences. 

Lazy sequences are a data structure and they are a specific type of sequences. The values of a lazy sequence are produced on an as needed basis. The clojure docs state that the "sequence elements are not avaialable ahead of time. [Instead they are] produced as the result of a computation." The neat thing about lazy sequences is that they can be infinite for example in the first code example `range` without a default value specifies the default value to be infinity. 

We can also define our own lazy sequence in Clojure

```clojure
(defn multiply-2 [lazysequence]
  (lazy-seq (cons (* 2 (first lazysequence)) (multiply-2 (rest lazysequence)))))
```

The example above is a recursive function that generates a new lazy sequence with every value from the original lazy sequence multiplied by two. It does this by taking the first element of the original lazy sequence and multiplying it by 2. It then calls back `multiply-2` on the remaining elements in the original lazy sequence. 

To generate all the positive whole numbers mulitplied by 2 we can run `(multiply-2(range))` although this will cause the repl to crash. But the internals of how it is evaluated look something like this

```clojure
(multiply-2 (range))

; below (rest range) is equal to all of the postive numbers excluded 0
; (lazy-seq (cons (* 2 0)) (multiply-2 (rest range))) 
;                                   ↓
;
;        below (rest range) is equal to all of the postive numbers excluded 0 and 1
;                          (lazy-seq (cons (* 2 1)) (mulitply-2 (rest range))) 
;                                                             ↓
;
;                         below (rest range) is equal to all of the postive numbers excluded 0 and 2
;                                                   (lazy-seq (cons (* 2 2)) (mulitply-2 (rest range))) 
;                                                                                       ↓
;                                                                                 on to infinity 

```
We can then `take` the first 5 elements of the infinite lazy sequence `(multiply-2(range))`. 

```clojure
(take 5 (multiply-2 (range)))
; (0 2 4 6 8)
(class (take 5 (multiply-2 (range))))
; clojure.lang.LazySeq
```




