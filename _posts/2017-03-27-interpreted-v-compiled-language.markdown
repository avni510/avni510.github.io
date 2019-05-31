---
layout: post
title:  Interpreter Vs. Compiler
date:   2017-03-24 09:20:00 -0600
---
Interpretation and compilation are implementations of a language rather than a property of a language. It is, in fact, not correct to say a specific language is compiled. A language can either be executed through a compiler or interpreter, it's just a matter of implementation. For example, Python code is usually executed through an interpreter, but it is also possible to create a compiler to execute the same Python code. 

### Compilation

All the source code is translated into machine code which is then directly executed by the hardware. An executable is usually created which contains the machine code instructions.This means if you make a change to your source code, the code needs to be compiled into machine code before it can actually be executed. Here's what the process looks like visually:

![compiler](/assets/Compiler.png)

### Interpreted

There are a few different ways an interpreter can work. One way is that it reads a line of source code and then translates that line to machine code and executes it. For example, when the interpreter sees `+` at run time it will call its own definition of `add(x,y)`, which then executes the machine code's equivalent of the add instruction. The other way (which is more common) is the interpreter reads a line of source code, translates that into some efficient intermediate representation, which is then translated and executed into machine code. The important thing to remember about an interpreter is that execution of code happens line by line. Here's what the process looks like visually:

![interpreter](/assets/Interpreter.png)

### Java

The most common way to run Java code is first it goes through a Java compiler (called Javac) which converts the Java code into something called bytecode. The JVM then interprets the bytecode into machine code. The reason why Java is not directly translated intto machine code, and instead has an intermediate step of being converted to bytecode is because machine code is specific to the machine. So it cannot run across different platforms. Whereas bytecode is platform independent. Upon execution, the bytecode is handed off to the JVM which interprets the bytecode, translates it to machine code, executes it. The advantage of translating it to bytecode and then interpreting it is interpreting the bytecode is much faster than directly interpreting the source code. 


 
