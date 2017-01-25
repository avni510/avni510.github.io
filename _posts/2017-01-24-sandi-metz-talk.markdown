---
layout: post
title:  Null Object Pattern
date:   2017-01-24 11:16:00 -0600
categories: Sandi Metz
---
Today I watched the [Nothing is Something](https://www.youtube.com/watch?v=OMPfEXIlTVE) talk by Sandi Metz. During the first half of her talk she goes into the Null Object Pattern. This pattern specifically handles how to deal with nil or null values in your program. When over looked they can cause majors errors in your code and should be handled carefully. Here's an example when nil can cause a problem 

{% highlight ruby %}
class LogTimeEntry
  attr_reader :id
  attr_reader :date
  def initialize(id, date)
    @id = id
    @date = date
  end
end

class LogTimeRepo
  def initialize
    @entries = 
      [ 
        LogTimeEntry.new(1, "01-01-2017"), 
        LogTimeEntry.new(2, "12-31-2017"), 
        LogTimeEntry.new(3, "01-04-2017") 
      ]
  end 

  def find_by(id)
   @entries.find { |entry| entry.id == id }
  end
end

class EmployeeReport
  def display_date(id)
    log_time_entry = log_time_repo.find_by(id)
    puts "#{log_time_entry.date}"
  end 
end
{% endhighlight %}

Here we have a `LogTimeEntry` class that has the instance variable date. `LogTimeRepo` has a `#find_by` method that  will return an instance of `LogTimeEntry`. Therefore, in `#display_date` we are anticipating an instance of `LogTimeEntry`. What if an id is passed into `#display_date` that does not exist (ex. id = 5)? Then we will recieve back a nil value. In this case the program will crash because we attempt to call `date` on a nil value. 

Lets say that we decide to handle in the nil value in `EmployeeReport`

{% highlight ruby %}
class EmployeeReport
  def display_date(id)
    log_time_entry = log_time_repo.find_by(id)
    if log_time_entry
      puts "#{log_time_entry.date}"
    else
      puts "No date"
    end
  end 
end
{% endhighlight %}

This works but imagine we are to call `#date` on a potentially nil value across many parts of the program. This could cause a lot of duplication. Along with that what if we wanted to handle the nil a different way in the future. This would mean a lot of searching around in the code would have to take place. 

Instead here's a work around

{% highlight ruby %}
class LogTimeEntry
  attr_reader :id
  attr_reader :date
  def initialize(id, date)
    @id = id
    @date = date
  end
end

class NoLogTimeEntry
  attr_reader :date
  def initialize
    @date = "No date"
  end
end

class LogTimeRepo
  def initialize
    @entries = 
      [ 
        LogTimeEntry.new(1, "01-01-2017"), 
        LogTimeEntry.new(2, "12-31-2017"), 
        LogTimeEntry.new(3, "01-04-2017") 
      ]
  end 

  def find_by(id)
    @entries.find { |entry| entry.id == id } || NoLogTimeEntry.new
  end
end

class EmployeeReport
  def display_date(id)
    log_time_entry = log_time_repo.find_by(id)
    puts "#{log_time_entry.date}"
  end 
end
{% endhighlight %}

We create an additional object - `NoLogTimeEntry` that contains `#date` as well. This way `log_time_entry` will have the `#date` method regardless of whether or not it is a nil value. This allows our program to handle nil values in a cleaner way and avoid having to explicitly check for them. It also makes it easy to change the way we handle nil values in the future (ex: changing the message that is displayed to the user if there is no date).
