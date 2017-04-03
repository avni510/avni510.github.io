---
layout: post
title:  Brief Introduction to Generative Testing in Clojure
date:   2017-04-03 11:20:00 -0600
categories: Clojure
---

In [Eric Normand's talk](https://www.youtube.com/watch?v=r5i_OiZw6Sw&list=PLZdCLR02grLp4W4ySd1sHPOsK83gvqBQp&index=18) at Clojure/west he covers the benefits of generative testing. He specifically covers Clojure, although generative testing can be used for any language. In unit testing the engineer comes up with the different scenarios that a user could possibly select and asserts that their program works in the correct way. With generative testing, the engineer defines the properties the result should have and the test runner generates randomized data to check against. In Eric's talk he creates a simple key-value database with some basic operations. Here are those operations

```clojure
(db/create)

(db/clear! db)
(db/store! db k v)
(db/delete! db k)
(db/fetch db k)
(db/size db)
```

These basic operations should be relatively self explanatory. We can then write a few basic tests on this simple system in clojure.test. 

```clojure
(deftest store-contains
  (let [db (db/create)
        k "age"
        v "23"]
    (db/store! db k v)
    (is(= v (db/fetch db k)))))

(deftest clear-empty
  (let [db (db/create)
        k "age"
        v "23"]
    (db/store! db k v)
    (db/clear! db)
    (is (zero? db/size db))))
```

These are typical unit test cases we would run for our system. But in fact this can be a very poorly tested system. Typically, as a engineers we end up testing relatively basic scenarios. It's difficult to think of odd user scenarios that could potentially break your system. We try our best to think about edgecases, but the reality is they are difficult to think of. For example, if a user enters 3 actions of our basic system, they have 5 functions they could use for each of their actions (assuming the database is already created). 

Action 1: 

```clojure
(db/clear! db)
(db/store! db k v)
(db/delete! db k)
(db/fetch db k)
(db/size db)
```

Action 2:

```clojure
(db/clear! db)
(db/store! db k v)
(db/delete! db k)
(db/fetch db k)
(db/size db)
```

Action 3:

```clojure
(db/clear! db)
(db/store! db k v)
(db/delete! db k)
(db/fetch db k)
(db/size db)
```

That's 5 * 5 * 5 = 125 different combinations of actions. This is close to impossible to test with our traditional unit testing approach. This is where generative testing can be helpful. 

Here are some pictures from Eric's talk where he defines a generative test case using [test.check](https://github.com/clojure/test.check) which works with clojure.test.

![generative_testing](/assets/generative_testing_1.png)

The above code generates and randomly generates 20 string inputs. These are strange inputs that we would often not test with unit tests.

![generative_testing_2](/assets/generative_testing_2.png)

This will go through all the given inputs, create a new database, store the randomly generated key and value pairs, and assert they are actually in the database. 

These tests work great for testing pieces of your system, but is there a test we can write that would describe the behavior of the entire system? 

![generative_testing_3](/assets/generative_testing_3.png)

Here we create many generators. And the last one, `gen-ops`, creates a vector of actions executed in a random order, and the number of actions performed is random as well. 

![generative_testing_4](/assets/generative_testing_4.png)

This is a sample of our results. Many different operations in different orders again different orders that when we're writing unit tests we don't often consider. The talk goes further into how we would assert that our system behaved correctly. But this is just a glimpse into the power the generative testing. It allows us to have a specified amount of random data that we can then test against. It also allow us to thoroughly test the entire behavior of our system. Check out the talk to learn more about it. 

