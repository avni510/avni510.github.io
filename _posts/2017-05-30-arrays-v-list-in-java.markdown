---
layout: post
title:  Arrays vs. ArrayLists in Java
date:   2017-05-30 10:00:00 -0600
categories: Java
---

In Java, List is an interface. Two common classes that implement the List interface are ArrayList and LinkedList. The Java documentation states, the classes that implement the List interface are an ordered collection. It allows for operations such as accessing elements and inserting each element at a specific point. 

In Java, you will mainly be choosing between an Array and an ArrayList. Array is a fixed length data structure. Meaning it allocates a fixed length of memory for the specific Array. While ArrayList is a variable length. A resize operation is performed when it gets full. An ArrayList is internally backed by Array in Java. So the resizing operation does cause a slow down in performance because it means creating a new (larger) Array and copying over the contents from the old array to the new array. An addtional benefit of Arrays is that it can store both primitives and objects, while ArrayList can only store objects. The advantage of primitives is that they don't take up as much memory storage. 

Each data structure has its own advantages and disadvantages. If you're looking for more performance benefits an Array should be used, but if you're looking for a more flexible data structure to add elements to as you go along then an ArrayList should be used. 


