---
layout: post
title:  Database Cleaning
date:   2017-01-30 15:00:00 -0600
---

If you are using a database for your application you are probably writing some tests to verify the correct results are written to your database. In my case, I set up a test database that I write results out to and verify the results. I initially made the mistake of not cleaning my test database after each test and ran into the same result being added to my database mulitple times. I then started to realize the importance of database cleaning for your tests - it allows each test to be run independently of each other. I started looking into different database cleaning methods and here are a few I found: 


**Deletion**

This cleaning mechansim uses SQL's `DELETE FROM <table_name>` statement. This will delete one row at a time in the table. Generally causing it to be one of the slower Database Cleaning methods. `DELETE FROM` also allows the usage of a where clause to elminate specific entries ex: `DELETE FROM USERS WHERE id=1` will only delete the row where id is 1. If you have an auto incrementer for the ID column `DELETE FROM` will not reset the auto incremeter. Another statement has to be run in order to do so. 

**Truncation**

This uses SQL's `TRUNCATE TABLE <table_name>` statement. It will delete all the rows from the table specified. Contrary to the `DELETE FROM` statement `TRUNCATE TABLE` does not allow the specification of a where clause. Either all entries go or none. An advantage of truncation is that after deleting all the rows if you have an ID column that is auto incremented, it will be reset. But beware because `TRUNCATE TABLE <table_name>` cannot be used on tables with a foreign key reference. Here is an example. Lets say we have a Users table

![before_truncation](https://avni510.github.io/assets/db_truncation_before_table.png)

We can run the statement `TRUNCATE TABLE USERS;`

And all rows in the table are deleted (this statement works because there is no foreign key reference to any of the columns in Users)

![after_truncation](https://avni510.github.io/assets/db_truncation_after_table.png)


**Transaction**

This will use SQL's `BEGIN` statement. This allows you to, in a sense, protect the following commands you write. I think it's easiest to see how `BEGIN` works by an example. Lets say we have an Employees table that looks like this: 

![before_transaction](https://avni510.github.io/assets/db_transaction_before_table.png)

Then we run a series of commands starting with the `BEGIN` statement and then updating an employee name

![transaction_commands](https://avni510.github.io/assets/db_transaction_commands.png)

Our Employees table now looks like this

![after_transaction](https://avni510.github.io/assets/db_after_transaction.png)

But perhaps we made a mistake and didn't mean to update that specific employee and it should have instead been another employee. 

We can easily run `ROLLBACK;` and all our changes (inside the `BEGIN` block) will be deleted. 

![before_transaction](https://avni510.github.io/assets/db_transaction_before_table.png)

Or we can run `COMMIT;` and all the changes we made (inside the `BEGIN` block) will be saved. Transactions are particularly useful if you're making a large change and want to double check that the command you entered will make the correct changes to your database. This is also probably the most frequently used cleaning method as it is generally the fastest. 

For Rspec there is a gem called [Database Cleaner](https://github.com/DatabaseCleaner/database_cleaner) that uses the strategies listed above to clean your database for your test suite.

