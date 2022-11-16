---
layout: post
title:  Tail Recursive Minimax
subtitle: The Algorithm
tags: [Sample Post, Welcome to the Grape Theme!]
author: Avni Kothari 
comments : False
---

My post a few weeks ago about implementing minimax in Clojure was written in a linear recursive way. Another way to write the same algothrim is in a tail recursive manner. Both have their pluses and minuses which I'll discuss further into the post. But I want to go through a brief explantation on how to think about this algothrim tail recursively.

The main difference between the two different types of recursion is that with linear recursion the stack frames continue to grow on the call stack until the base case is hit, in which case it'll then pop each recursive call off the stack and evaluate them. The general shape of the process of evaluation for linear recursion can be thought of as a bell curve. By contrast, tail recursion passes the results from the current recursive call to the next recursive call. The function keeps track of its return value as it goes along. This can be thought of as accumulation value. The general shape of the process of evaluation can be thought out a straight down line. Some languages, like Clojure, are optimizied for tail recursion, while others are not.


With minimax, I approached this problem by thinking about what I want to keep track of in the tree. I know the nodes in the tree are boards representing each game state, so between each node I want pass up the score of the current node to the parent node and the optimal score is then calculated for the parent node (either the max of min score depending on which kind of node it is).

![tail_recursive_tree](/assets/img/tail_recursive_tree.png)

The circles can be thought of as nodes (which in my case are boards), and the blue circles can be thought of as max nodes (calculating the max of their subtree) while the green circles can be thought of as min nodes (calculating the min of their subtree).

Here's the order the computer would evaluate this kind of tree

1. Continue to go down the tree until it hits a terminal node
2. Get the value or the score of the terminal node
3. Pass back the current node's score to the parent node
4. Replace the parent node's score if the current node's score is the optimal score

As you can see at each node, we're keeping track of the best possible score hence why we need to implement this tail recursively.

I also decided that the default score for a max node is -100 and for a min node is 100. I chose these values because that's the worst possible score each respective node could have and it makes it easier for calculation purposes. Also, what we finally want returned from the algothrim is not the score but the space. So we need to make sure if we are at the root node and the entire tree has been traversed that we return the best space rather than the best score.

Unfortunately, with an algothrim like minimax there's a lot of conditions and a lot to keep track of so this implementation takes in a lot of parameters. 

There's a few conditions we have to keep in mind: 
* If the algothrim is at a root node and the whole tree is traversed -> return the space
* If the algothrim is at a terminal node -> figure out if the parent's score needs to be replaced
* If the algothrim is at a node where the children are traversed -> figure out if the parent's score needs to be replaced
* If the algothrim is at an intermediate node and none of the children are traversed -> traverse the children

I decided to keep track of all the parameters with a map. And I call it accumulator because it represents the accumulated best score at each node (which is a board). 

accumulator
- board-and-space (this is a hash map, and it is the current node's board and space)
- score (represents optimal score of the current node's)
- depth (represents the current node's depth)
- parent_accumulator (represents the current node's parent node - it has the same keys as accumulator although different values to represent the parent)
- children (is a sequence of all my children nodes - which are boards)
- markers (is a map that contains the computer marker and the human marker)

Here's some pseudo code for the algothrim. I wrote some comments in first person, with the perspective that "I" represents the current node

``` clojure
(defn tail-minimax [accumulator]
  ; if I am the root node and whole tree has been traversed
  if accumulator.depth = 0 and accumulator.children = empty
    return space associated with best score

  ; if I am the terminal node or all the children have been traversed
    if accumulator.board is terminal node or accumulator.children = empty:

      if the node of the accumulator is a MIN node
        ; if my score is better than my parent's score 
        if accumulator.score < parent_accumulator.score
          ; the root node needs to know the best space so I pass my space to the root node
          if accumulator.depth = 1
             parent_accumulator.score = accumulator.score
             parent_accumulator.space = accumulator.space
          ; if my parent is an intermediate node I just need to replace my parent's score
          if accumulator.depth > 1
             parent_accumulator.score = accumulator.score
          ; call back the function with my parent but I have already been traversed
          recur (parent_accumulator with parent_accumulator.children = (rest parent_accumulator.children))
        ; if my score is not better than my parent's score - do not replace it, but call my parent to traverse the remaining children
        if accumulator.score > parent_accumulator.score
          recur (parent_accumulator with parent_accumulator.children = (rest parent_accumulator.children))

      if the node of the accumulator is a MAX node
        if accumulator.score > parent_accumulator.score
          if depth = 1
            parent_accumulator.score = accumulator.score
            parent_accumulator.space = accumulator.space
          if depth > 1
             parent_accumulator.score = accumulator.score
          recur (parent_accumulator with parent_accumulator.children = (rest parent_node.children))
        if accumulator.score < parent_node.score
          recur (parent_accumulator with parent_accumulator.children = (rest parent_node.children))

    ; if I am an intermediate node and I still have children I have not traversed
    if the accumulator's node is an intermediate node:
      recur (first accumulator.children))
```

I realize this is a mess and very difficult to read. So I've tried to space it out into the main conditions which are then followed by subconditions. 

This pseudo code is pretty thorough (maybe a bit too much) the rest is just a matter of putting it into the Clojure language. 

Here's the final implementation

```clojure
(def best-move (memoize (fn [accumulator]
  (let [board (:board (:board-and-space accumulator))
        space (:space (:board-and-space accumulator))
        depth (:depth accumulator)
        alpha (:alpha accumulator)
        beta (:beta accumulator)
        parent-accumulator (:parent-accumulator accumulator)
        children (:children accumulator)
        computer-marker (:computer-marker (:markers accumulator))
        opponent-marker (:opponent-marker (:markers accumulator))]

    (if (and (zero? depth) (or (empty? children) (= (:score accumulator) best-possible-score)))
      space
      (do
        (if (or (game-evaluation/game-over? board) (empty? children) (<= beta alpha))
          (let [score (if (game-evaluation/game-over? board)
                        (calculate-score board computer-marker opponent-marker depth)
                        (:score accumulator))]
            (if (zero? (mod depth 2))
              (if (< score (:score parent-accumulator))
                (if (= depth 1)
                  (recur (assoc parent-accumulator :children (rest (:children parent-accumulator))
                                                   :score score
                                                   :beta (min score (:beta parent-accumulator))
                                                   :board-and-space (assoc (:board-and-space parent-accumulator)
                                                                           :space space)))
                  (recur (assoc parent-accumulator :children (rest (:children parent-accumulator))
                                                   :beta (min score (:beta parent-accumulator))
                                                   :score score)))
                (recur (assoc parent-accumulator :children (rest (:children parent-accumulator)))))
              (if (> score (:score parent-accumulator))
                (if (= depth 1)
                  (recur (assoc parent-accumulator :children (rest (:children parent-accumulator))
                                                   :score score
                                                   :alpha (max score (:alpha parent-accumulator))
                                                   :board-and-space (assoc (:board-and-space parent-accumulator)
                                                                           :space space)))
                  (recur (assoc parent-accumulator :children (rest (:children parent-accumulator))
                                                   :alpha (max score (:alpha parent-accumulator))
                                                   :score score)))
                (recur (assoc parent-accumulator :children (rest (:children parent-accumulator)))))))
          (recur {:board-and-space (first children)
                  :score (determine-initial-score (inc depth))
                  :depth (inc depth)
                  :alpha alpha
                  :beta beta
                  :parent-accumulator accumulator
                  :children (generate-boards (determine-player-marker (+ 2 depth)
                                                                      computer-marker
                                                                      opponent-marker)
                                             (:board (first children)))
                  :markers (:markers accumulator)}))))))))
```

An extremely large function. Testing this was difficult. I ran through multiple different types of board scenarios and drew out a tree on paper and had a bunch of print statements in my function to verify that I was getting the correct results. But to really thoroughly test it you can generate all possible end game states and double check that the computer never loses (the game is either won by the computer or is tied). 

So which implemention do we pick? The linear recursive way or the tail recursive way? 

The tail recursive way is much slower. There are many more recursive calls to the function than the linear recursive way. To go back to the picture above, there are 5 recursive calls for 3 nodes until the best move is returned. But the amount of memory that is used is much lower than the linear recursive version. This is because we are keeping track of the best score as we go along, rather than waiting till the end to evaluate it. So it depends which one you're willing to let go on - speed or memory. Although the tail recursive version is slower there is a way to optimize it with alpha beta pruning.

