---
layout: post
title:  Throws keyword in Java
date:   2017-03-24 09:20:00 -0600
---

<!---
* used in method declaration
* specifies the exceptions that a method might throw
* include a throws clause to delcare those exceptions that might be thrown but don't get caught in the method
* clearer to read, if other methods want to implement a method that throws exceptions they must be able to handle these exceptions

When should it be used? 
* If you are catching an exception type in your method you do not need a throw
* If there is no try/catch then the throws is necessary and the exception will be handled by the calling method
* Write it if youur method throws a checked exception

Checked vs. Unchecked Exceptions

Checked Exceptions
- checked at compile time
- if the code within a method throws a checked expection then the method mush either handle the exception or must specifiy the exception using `throws`

Unchecked Exceptions
- exceptions that are not checked at compile time
- diagram Error and RuntimeExceptions are unchecked 
--->

The `throws` keyword is used in method declaration. It specifies that an exception may be thrown by a particular method. 

#### When should it be used? 

If you are catching an exception in your method then you do not need a `throws` keyword. For example, if you have a `try/catch` within your method then the keyword `throws` when defining the method is not necessary. But if there is no `try/catch` block and the exception is a checked exception (explained further down) then `throws` in the method definition is necessary. 

#### Checked Exceptions

These are exceptions that are checked during compile time. If the code within a method throws a checked exception then the method must either handle the exception or must specify the exception using `throws`. 

#### Unchecked Exceptions

These are exceptions that are not checked at compile time. 

![java exceptions](/assets/java_exceptions.gif)

From the Java documentation "The Throwable class is the superclass of all errors and exceptions in the Java Language". However you don't need to specify `throws` for all subclasses of Throwable. Any exception that is a subclass of Error or RuntimeException are not checked at compiled time - meaning they are unchecked exceptions. All other exceptions are checked exceptions. 
