---
layout: post
title:  Ruby Mutability
date:   2017-02-06 16:16:00 -0600
categories: Ruby Mutability
---
This post will cover the differences between immutability and mutability in Ruby. 

#### Variable References

An object has state which is often referred to as a value. When a variable is assigned to an object it has a reference to that object. Ex:

{% highlight ruby %}
>> a = "hello world"
=> "hello world"

>> a.object_id
=> 70113614077220
{% endhighlight %}

 This means the variable `a` has a reference to the String object with the value "hello world". `#object_id` is a method that Ruby provides and it tells us what the id of the object it is refering to. In this example `a` is pointing to the string object with the id 70113614077220.

![reference_diagram](/assets/Reference.png)

There are two types of objects - those that are immutable and those that are mutable 

#### Immutable Objects

These are objects whose state cannot be changed. In Ruby, the Integers class is immutable. Here's an example

{% highlight ruby %}
>> number = 5
=> 5

>> number.object_id
=> 11

>> number = 12
=> 12

>> number.object_id
=> 25
{% endhighlight %}

When we assign `number` to the value 5 the object id is 11. But when we change `number` to the value of 12 the object id is 25. So number is referring to a different object after being reassigned. 

Internally it looks like this

![reference_integer_diagram](/assets/Reference_Integer.png)


Even something like this will cause `number` to have a different reference point

{% highlight ruby %}
>> number = 5
=> 5

>> number.object_id
=> 11

>> number += 2
=> 7

>> number.object_id
=> 15
{% endhighlight %}

Even when we are incrementing `number` it actually references a new integer (7) which is a different instance of the Integer class.

#### Mutable Objects 

These are objects whose state can be changed. In Ruby, Strings, Hashes, and Arrays are a few of the classes that are mutable. Here's an example

{% highlight ruby %}
# create an empty hash
>> a = {}
=> {}
>> a.object_id
=> 70113613908440
# add a value to the hash
>> a[1] = "foo"
=> "foo"
>> a
=> {1=>"foo"}
# the variable 'a' still has the same object id
>> a.object_id
=> 70113613908440
# the key of '1' has a different object id than a 
>> a[1].object_id
=> 70113613877980
{% endhighlight %}

Here's what's happening internally 
![mutable_diagram](/assets/Mutable.png)

`a` has a reference to an instance of Hash and each key of the hash is pointing to an instance of String with the corresponding hash value. 

Because a Hash is mutable if we add another key, value pair to the hash, `a` will have the same reference point (the same object id). 

{% highlight ruby %}
# add a key value pair to the hash
>> a[2] = "bar"
=> "bar"
>> a
=> {1=>"foo", 2=>"bar"}
# a has the same object id 
>> a.object_id
=> 70113613908440
# the new key of '2' has a new object id
>> a[2].object_id
=> 70113613831220
{% endhighlight %}

Again here's what's happening internally

![mutable2_diagram](/assets/Mutable2.png)

