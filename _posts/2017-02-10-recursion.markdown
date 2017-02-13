---
layout: post
title:  Recursion
date:   2017-02-10 11:16:00 -0600
categories: Recursion
---

Recursion is often found in many built in functions within Clojure for example in `filter` and `map`. This blogpost will specifically cover linear and tree recursion.

#### **Linear Recursion**

Linear recursion for a vector works by processing the first element and working it's way down the sequence. The size of the computation grows linearly with the size of the input so the big o notation is O(N)

Here's an example of a recursive function.

```clojure
(defn map-func [f seq1]
  (if (empty? seq1)
    seq1
    (cons (f (first seq1))
      (map-func f (rest seq1))
    )
  )
)
```

Here's the evaluation of the function. 

```clojure
(map dec [6 7 3])
; (cons (dec 6) (map-func dec [7 3]))
;                   ↓
;               (cons (dec 7) (map-func dec [3]))
;                                   ↓
;                             (cons (dec 3) (map-func dec ()))
;                                                  ↓
;                                                  ()
```

The final call occurs because the base case is hit. So the sequence returned is this

```clojure
; (cons (dec 6) (cons (dec 7) (cons (dec 3) ())))
; => '(5 6 2)
```

As you can see there were 3 recursive calls and 3 elements in the vector therefore the big o notation is O(N) for this recursive function. 

#### **Tree Recursion**

Another type of recursion is tree recursion. An example of tree recursion is the Fibonacci Sequence. This sequences adds together the previous two natural numbers. Here are the first 5 Fibonacci numbers. 

f0 = 0

f1 = 1

f2 = 1

f3 = 2

f4 = 3

f5 = 5

...

So to find the next element of the fibonnaci sequence we can do this: 

f2 = f1 + f0

f3 = f2 + f1

f4 = f3 + f2

f5 = f4 + f3

...

In clojure, it can be written like this: 


```clojure
(defn fib [n]                                                                              
   (cond                                                                                    
     (= 0 n)                                                                                
       0                                                                                    
     (= 1 n)                                                                                
       1                                                                                    
     :else                                                                                  
       (+ (fib (dec n)) (fib (- n 2)))                                                      
   )
)
```
If we wanted to find the 4th Fibonacci number

```clojure
(fib 4)
; => 3
```
The evaluation would like something like this

![fib_tree](/assets/fib_tree.png)

This type of recursion spawns off  multiple children until either base case is reached. The values of the nodes are then totaled up and passed up the tree and the final result of 3 is returned. 

![fib_tree_after_eval](/assets/fib_tree_after_eval.png)


