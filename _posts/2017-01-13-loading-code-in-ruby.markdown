---
layout: post
title:  Loading Code in Ruby
date:   2017-01-13 10:00:00 -0600
categories: Ruby, load code
---

You may have seen `$LOAD_PATH` written out in Ruby programs. `$LOAD_PATH` is an array where each element in the array represents a file directory where files live. Each time you run a Ruby file `$LOAD_PATH` requires all the files in those directories even if `$LOAD_PATH` is not explicitly written out in your program. To see a list of default directories your Ruby program runs you can run `irb`, enter `puts $LOAD_PATH`. This will output a path of file directories that will be loaded upon running your ruby program. For me it outputs

```
/Users/avnikothari/.rbenv/rbenv.d/exec/gem-rehash
/Users/avnikothari/.rbenv/versions/2.2.5/lib/ruby/site_ruby/2.2.0
/Users/avnikothari/.rbenv/versions/2.2.5/lib/ruby/site_ruby/2.2.0/x86_64-darwin16
/Users/avnikothari/.rbenv/versions/2.2.5/lib/ruby/site_ruby
/Users/avnikothari/.rbenv/versions/2.2.5/lib/ruby/vendor_ruby/2.2.0
/Users/avnikothari/.rbenv/versions/2.2.5/lib/ruby/vendor_ruby/2.2.0/x86_64-darwin16
/Users/avnikothari/.rbenv/versions/2.2.5/lib/ruby/vendor_ruby
/Users/avnikothari/.rbenv/versions/2.2.5/lib/ruby/2.2.0
/Users/avnikothari/.rbenv/versions/2.2.5/lib/ruby/2.2.0/x86_64-darwin16
```

#### Using Require Statements
In your Ruby file if you write `require time_logger` at the top of your file the program will go through the file directories that are each element in the `$LOAD_PATH` array and look for `time_logger.rb`. While working on my time logger application I wanted to load numerous files in another directory. I did this by adding an additional path to the `$LOAD_PATH` array. I used the File class to grab the file path for the directory I wanted to insert in `$LOAD_PATH`. At the top of my file I added 

```
$LOAD_PATH << File.expand_path('../lib', __FILE__)
```

I've now included my lib directory in the `$LOAD_PATH` array. So I can use `require` statements and it will look for files in the added directory in addition to all the standard directories. One benefit of this is instead of having messy `require_relative` statements I could use clean and easy to read `require` statements. The other benefit is that if you move files and add directories in the `/lib` folder you no longer have to change your path because `$LOAD_PATH` will look through everything in your `/lib` folder. 
