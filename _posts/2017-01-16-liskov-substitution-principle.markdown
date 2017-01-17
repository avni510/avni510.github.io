---
layout: post
title:  Liskov Substitution Principle
date:   2017-01-16 11:09:00 -0600
categories: SOLID Principles
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
def Animal 
  def eat 
    puts "I am eating"
  end
end

def Dog < Animal
  def walk
    put "I am walking"
  end
end
{% endhighlight %}

This is a violation because any instance of Dog cannot replace any instance of Animal and be expected to behave the same way. For example, we cannot do something like this: 

{% highlight ruby %}
animal1 = Animal.new
animal1.walk
{% endhighlight %}

This would cause the program to crash. Instead we can rewrite the example above to this

{% highlight ruby %}
def Animal 
  def eat 
    puts "I am eating"
  end

  def walk
    raise NotImplementedError
  end
end

def Dog < Animal
  def walk
    put "I am walking"
  end
end
{% endhighlight %}

This way any instance of Dog can be replaced by any instance of Animal, and an error would be raised if we called `#walk` on an instance of Animal. 
