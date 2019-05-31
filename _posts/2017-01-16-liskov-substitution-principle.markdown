---
layout: post
title:  Liskov Substitution Principle
date:   2017-01-16 11:09:00 -0600
---

Liskov Substitution Principle is the "L" in the SOLID Principles. It states if you have a parent class and a child class, any instance of the child class should be able to replace any instance of the parent class without having unexpected behaviors.

This is a classic example of a violation that Bob Martin uses in his book *Agile Software Development*

{% highlight ruby %}
class Rectangle
  def set_height(height)
    @height = height 
  end

  def set_width(width)
    @width = width
  end
end

class Square < Rectangle
  def set_height(height)
    super(height)
    @width = height
  end
  
  def set_width(width)
    super(width)
    @height = width
end
{% endhighlight %}

This is a violation because the Square class modifies both height and width when  `#set_height` or `#set_width` are called. Where as in the Rectangle class when `#set_height` or `#set_width` only one instance variable is being changed by each method. Any instance of `Square` cannot be replaced for any instance of `Rectangle` because modifying the other instance variable in the methods of the Square class can cause unintended consequences. 

This can be rewritten like this so it is not a violation

{% highlight ruby %}
class Rectangle
  def initialize(params)
    @height = params[:height]
    @width = params[:width]
  end
end

class Square
  def initialize(params)
    @height = params[:height] || params[:width]
    @width = params[:height] || params[:width]
  end
end
{% endhighlight %}


Below is another example of a violation. 

{% highlight ruby %}
class Animal 
  def eat 
    puts "I am eating"
  end
  
  def walk
    puts "I am walking"
  end
end

def Dog < Animal
  def walk
    puts ["I", "am", "walking"]
  end
end
{% endhighlight %}

This is a violation because any instance of Dog cannot replace any instance of Animal and be expected to behave the same way. If we create an instance of Animal and an instance of Dog and call `#walk` on both of the instances we will receive back two different data structures - a string and an array respectively. 

{% highlight ruby %}
animal1 = Animal.new
print animal1.walk
# => "I am walking"
dog1 = Dog.new
print dog1.walk
# => "["I", "am", "walking"]
{% endhighlight %}

Instead we need consistency. Both implementations of `#walk` need to return the same data structure. 

{% highlight ruby %}
class Animal 
  def eat 
    puts "I am eating"
  end
  
  def walk
    puts "I am walking"
  end
end

def Dog < Animal
  def walk
    puts "I am walking fast"
  end
end
{% endhighlight %}


Other violations include different method signatures or errors (the child class produces an error in a method that the parent class does not)
