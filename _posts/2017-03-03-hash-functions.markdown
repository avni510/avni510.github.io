---
layout: post
title:  Hash Functions
date:   2017-03-03 09:20:00 -0600
---

Recently I've been reading about what hash functions are and how they work. 

Essentially a hash function takes an input of data and returns a fixed size length of data back. A hash function is a one way function. This means given the output value you are not able to calculate the input value. 

An example of a hash function is called md5. Here's an example of how to run a piece of data through a hash function:

```
# Lets say I have a file called example.txt and the contents are Hello World
avnikothari@Avnis-MacBook-Air:~ $ cat example.txt
Hello World

# I can encode this file with the md5 hash function
avnikothari@Avnis-MacBook-Air:~ $ md5 example.txt
MD5 (example.txt) = e59ff97941044f85df5297e1c302d260
```

Visually it might look something like this: 

![hash function](/assets/hash-function.png)

The output of a hash function is often called a hash value or digest. For this article I'll stick with hash value. 

We'll first look into the properties of a hash function and then go into why it can be useful for Database Queries and Cryptography.

A hash function should fufill the following properties 
1. It should be deterministic - meaning given the same input it should always output the same hash value
2. For any given data, it should be easy to calculate a hash value
3. Given the hash value, it should be computationally difficult to calculate the input. This property has often led to a hash function being called a one-way function. You can only go one-way, not from the hash-value to the original input.
4. There should be a relatively low probability that two slightly different messages will have the same exact hash value. How low this probability actually is will be dependent on the usage of the hash function. But when 2 different inputs yield the same hash value it is said that it is a collision. Ideally, hash functions should be collision resistant - meaning given 2 different inputs they should not yield the same hash value.


