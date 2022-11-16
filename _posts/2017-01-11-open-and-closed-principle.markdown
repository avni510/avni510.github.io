---
layout: post
title:  Open and Closed Principle
tags: [Sample Post, Welcome to the Grape Theme!]
author: Avni Kothari 
comments : False
---
This is the "O" in the SOLID Principles of the five basic principles of object orientated design. The official definition is "software entities (classes, modules, functions, etc.) should be open for extension, but closed for modification." This means that a developer should be able to make changes to a program without making changes to the classes themselves. This may sound counter intuitive, but perhaps an example will help: 

{% highlight ruby %}
class Geometry
  def calculate_area(shape, params)
    if shape == "square"
      params[:length] * 2
    elsif shape == "circle"
      params[:radius] * params[:radius] * 3.14 
    end
  end
end
{% endhighlight %}

This is a violation of the open and closed principle because we if we add another shape like a rectangle we would need modify `#calculate_area`. A better way to write this would be

{% highlight ruby %}
class Geometry
  def initialize(shape)
    @shape = shape
  end

  def calculate_area(params)
    @shape.calculate_area(params)
  end
end

class Square
  def calculate_area(params)
    params[:length] * 2
  end
end

class Circle
  def calculate_area(params)
    params[:radius] * params[:radius] * 3.14
  end
end
{% endhighlight %}

This way if a developer wants to add another shape with `#calculate_area` a new class can be created for each new shape.  

{% highlight ruby %}
class Rectangle
  def calculate_area(params)
    params[:length] * params[:width]
  end
end
{% endhighlight %}

This allows the Geometry class to be open for extension, but closed for modification.
