---
layout: post
title:  Enumerable Module in Ruby
date:   2017-01-17 16:16:00 -0600
categories: Ruby Enumerable Module
---

The Enumerable Module is a mixin. The methods from the module are used in the Array and Hash classes. You can actually use the Enumerable module in any of your classes as long as you have `#each` defined. All the methods in the module create a new data structure - ex: an array or hash - and the old one is not mutated. You would want to use the methods from the Enumerable module when you are iterating through a collection of objects and manipulating the data in that collection. 

Lets say we want an array of shortened words 

{% highlight ruby %}
class Phrase
  def shorten(data)
    shortened_phrase = []

    data.each do |word|
      shortened_phrase << word[0..2]
    end
    
    shortened_phrase
  end
end

phrase = Phrase.new
phrase.shorten(["Hello", "World"])
# => ["Hel", "Wor"]
{% endhighlight %}

We can shorten the length of `#shorten` by calling `#map` from the Enumerable module

{% highlight ruby %}
class Phrase
  def shorten(data)
    data.map { |word| word[0..2] }
  end
end
{% endhighlight %}

This method applies the block (what's in the parenthesis) to each element in the original array and returns a new array (the original array is untouched)

{% highlight ruby %}
phrase = Phrase.new
words = ["Hello", "World"]
phrase.shorten(words)
# => ["Hel", "Wor"]
print words
# => ["Hello", "World"]
{% endhighlight %}

Here are a few other useful methods from the Enumerable module

##### `#select` 
{% highlight ruby %}
["Hello", "World", "foo", "bar"].select { |word| word.length > 3 }
# => ["Hello", "World"]
{% endhighlight %}
This method only keeps the elements that evaluate to true 

##### `#reduce`
This method aggregates values based on the operation passed in the block. The value passed into `#reduce` is the starting value of the aggregation

{% highlight ruby %}
[3, 8, 4].reduce(5) { |sum, num| sum + num * 2 }
# => 35
{% endhighlight %}

`#reduce` is doing this
{% highlight ruby %}
5 + 3 * 2 + 8 * 2 + 4 * 2
# => 35
{% endhighlight %}

If no value is passed into `#reduce` it will automatically start with 0. 




