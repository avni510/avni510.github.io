---
layout: post
title:  Multimethods Vs. Protocols
date:   2017-02-22 11:16:00 -0600
categories: Clojure Polymorphism
---


There a few different ways to define polymorphic behavior in Clojure. The two main ways are through Multimethods and Protocols. 

Lets first take a look at Multimethods

#### Multimethods

Multimethods have something called a dispatch function which can be thought of like an air traffic controller. They determine where in the airport the plane should land based on various conditions and information. Multimethods work similarily in the way that there is one overarching dispatch function which makes a decision on which implementation to execute. Lets look at an example with Tic Tac Toe. 

I'm working on having two different types of players a human and a computer. Each player should select a move in a different way. For the human the program should prompt the user for their input and for the computer it should generate a random number on the board. And for both a human or a computer I want a new board returned with their filled in move

Here's my implementation of multimethods

```clojure
(defmulti make-move
  (fn [board player-map] (:player-type player-map)))

(defmethod make-move :human [board player-map]
  (->
    (read-string (console-ui/get-user-input))
    (board/fill-board board (:marker player-map))))

(defn computer-move []
  (rand-int 9))

(defmethod make-move :computer [board player-map]
  (->
    (computer-move)
    (board/fill-board board (:marker player-map))))

(defmethod make-move :default [board player-map]
  (console-ui/print-message (messages/invalid-player-type)))
```

There are a few core components to a multimethod. I have a dispatch function defined by `(defmulti make-move...)` which returns something called a dispatching value. In my case I am passing in a map to `make-move` which looks like this `{:player-type :human :marker :x}`. So my dispatch function is returning the dispatching value which would either be a `:human` or `:computer`. The keywords to the right of `defmethod` are called dispatch values which will match up with the dispatching value returned from `(defmulti make-move...)`. If the dispatching value is a `:human` it will ask the user for their input and fill in the board accordingly. If the dispatching value is a `:computer` it will compute a random move between 0-9 (exclusive) and return in a filled in value. 

Here's a visual of the vocabulary described above. 

![multimethods](/assets/multimethods.png)

As a clarification the dispatch function will return a dispatching value that is then matched up with a dispatch value. 

Essentially the dispatching function returns a value and that value is used to determine which method defintion should be used.

The last part 

```clojure
(defmethod make-move :default [board player-map]
  (console-ui/print-message (messages/invalid-player-type)))
```

is an exception case. If the dispatching function returns a value that doesn't exist the `:default` method implementation will be used. 

Here's an example of how it works

```clojure
=> (make-move [0 1 2 3 4 5 6 7 8] {:player-type :human :marker :x})
; asks for the user input and the user enters 5
 5
; [0 1 2 3 4 :x 6 7 8]
=> (make-move [0 1 2 3 4 5 6 7 8] {:player-type :computer :marker :x})
; generates a random move and fills in the board
; [0 1 2 3 :x 5 6 7 8]
```

The next way is through protocols. 

#### Protocols

Protocols are more similar to an object orientated way of solving polymorphism. Where as the multimethod is just one polymorphic operation a protocol offers the flexibility of implementing a collection of one or more polymorphic functions. Protocols are setup in a similar way to interfaces in Java. The first argument works like the dispatching value in multimethods. It determines which behavior of the function to use. 
 
Here's an example of how the same functionality from the multimethods example would be implemented through a protocol.

```clojure
(defprotocol Move
  (make-move [this board]))

(deftype Human [marker]
  Move
  (make-move [this board]
    (->
      (read-string (console-ui/get-user-input))
      (board/fill-board board marker))))

(defn- computer-move []
  (rand-int 9))

(deftype Computer [marker]
  Move
  (make-move [this board]
    (->
      (computer-move)
      (board/fill-board board marker))))
```

In the protocol example we're also using `deftype` which in my example above are used similar to classes in an OOP that implement an interface. Here I have two types either a Human or Computer that each implement the protocol and the corresponding method in the protocol - `make-move`. 

Here's an example of how it can be called

```clojure
=> (make-move (Human. :x) [0 1 2 3 4 5 6 7 8])
; asks for the user input and the user enters 5
 5
; [0 1 2 3 4 :x 6 7 8]
=> (make-move (Computer. :x) [0 1 2 3 4 5 6 7 8])
; generates a random move and fills in the board
; [0 1 :x 3 4 5 6 7 8]
```

Another neat thing about a protocol is you can have a varying degree of arguments as long as it specified in the protocol. For example

```clojure
(defprotocol Move
  (make-move [this board] [this board player-num])

(deftype Human [marker]
  Move
  (make-move [this board player-num]
    (->
      (read-string (console-ui/get-user-input))
      (board/fill-board board marker))))

(deftype Computer [marker]
  Move
  (make-move [this board]
    (->
      (computer-move)
      (board/fill-board board marker))))
```
Here we have specified that `make-move` can take either two arguments or three arguments. 


A note of restriction for protocols is that must implement all the methods from your protocol. 

If I had something like this

```clojure
(defprotocol Move
  (make-move [this board])
  (print-player-type [this])

(deftype Human [marker]
  Move
  (make-move [this board]
    (->
      (read-string (console-ui/get-user-input))
      (board/fill-board board marker))))
```

it would be violation since `(deftype Human)` does not implement the method `(print-player-type)`. 

Both multimethods and protocols acheive polymorphism behavior in Clojure. Mutlimethods tend to be a more functional approach  where as protocols tend to be a more object orientated way to solving the problem.



