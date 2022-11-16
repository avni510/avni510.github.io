---
layout: post
title:  SQL Prepare Statement
tags: [Sample Post, Welcome to the Grape Theme!]
author: Avni Kothari 
comments : False
---
The `PREPARE` statement is a useful and widely used statement in SQL. The way it works is it essentially creates a template of the query you would like to run. Instead of the actual values being passed in, placeholders are passed in (similar to parameters). Here's an example

```
PREPARE insert_new_emp(text, bool) 
AS INSERT INTO EMPLOYEES (username,admin) values($1, $2);
```

We have now prepared a statement (aliased by insert_new_emp) to insert a new user into the table `EMPLOYEES`. This does not actually insert any values into the `EMPLOYEES` table.

We can now execute the statement like this

```
EXECUTE insert_new_emp('foo', false);
```

This has now created a new user in the `EMPLOYEES` table with the username as 'foo' and the admin value as false. So `EXECUTE` will execute the prepared statement and the values are now inserted into the database. 

Prepared statements have some advantages over just running a straight insert command like this

```
INSERT INTO EMPLOYEES (username,admin) values('foo', false);
```

Here are a couple of advantages:

#### Query Optimization

By using `PREPARE` the query is only compiled once. The query is actually sent to the SQL server when `PREPARE` is used, but the data will be sent during `EXECUTE`. This can be helpful if you are running a similar query (with different data values) numerous times because the compilation of the query will only happen once. 

#### Mitigates against SQL injection

SQL injection is when malicious SQL statements are run through your application. For example, lets say you have a web application that has a textbox input that asks the employee to enter their username. A SQL query is then run, based on what the user entered, to make sure the user exists. Lets say they enter "foo" into the textbox. The application would then run this query to make sure "foo" exists

```
SELECT * FROM EMPLOYEES WHERE username=#{value_from_username_textbox};
```

This query would not produce any harmful results, but SQL allows statements to be chained together. Like this

```
SELECT * FROM EMPLOYEES 
WHERE username=#{value_from_username_textbox}; DROP TABLE EMPLOYEES;
```

In the textbox field a user may be able to enter "foo; DROP TABLE EMPLOYEES;" if there is no validation. These SQL commands would first find where the username is equal to the value from the textbox field and then drop the entire table `EMPLOYEES`. 

If the developer uses a prepare statement the and the user enters "foo; DROP TABLE EMPLOYEES;". It would never be run. This is because the `PREPARE` will first send the query to the SQL server then `EXECUTE` will pass the query the data. So the query and the data are separate. 
