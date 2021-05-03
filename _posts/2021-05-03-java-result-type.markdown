---
layout: post
title:  Java Result Type
date:   2021-05-03 08:51:00 -0600
---

## Result Type

`Result.java` is a custom type. Code can be found [here](https://github.com/avni510/java-result-type/tree/master) It is heavily modeled off the Result Type that exists in other functional and ML Languages (ex: Elm, Haskell, Rust).

As described by the Rust documentation, "Result is a richer version of the Option type that describes possible error instead of possible absence." Particularly in Java this gives the developer more control over when they would like to throw an error or bubble up an error.

### Usage
#### Basics
A `Result` should be defined with the types of the potential successful object and the potential failure object. Ex: `Result<Integer, String>`.  If the computation is successful it should should use `.ok` like `Result.ok(5)` and failures should be returned with error like `Result.error("Unable to parse into integer")`.

A `Result` can also be `Void` meaning the computation was successful, but there was no return value. The type would be defined like this `Result<Void, String>`. Below is an example:

```java
 if (creationIsSuccessful) {
   return Result.ok();
 } else {
   return Result.error("Could not create");
 }
```

To get the successful object. Please first check if the `Result` is successful by using the `.isSuccessful()` function. The successful object can be retrieved with `.get()`. Ex:

```java
 Result<Integer, String> result = Result.ok(5)

 if (result.isSuccessful()) {
   Integer value = result.get();
 }
```
To get the error. Please first check if the `Result` is successful by using the `.isSuccessful()` function. The error object can be retrieved with `.getError()`. Ex:

```java
 Result<Integer, String> result = Result.error("Could not parse")

 if (result.isSuccessful()) {
   Integer value = result.get();
 } else {
   String error = result.getError();
 }
```

#### Additional functionality
##### `.map()`

This should be used when transforming the successful type of a `Result` into another type. Ex: `Result<Integer, String> -> Result<String, String>`.

```java
Result<Integer, String> originalResult = Result.ok(1);
Result<String, String> newResult =
  result.map(value -> Integer.toString(value));

-> newResult.isSuccessful() -> returns true
-> newResult.get() -> returns "1"
```
If the originalResult is an error the `.map` is not applied and `originalResult` remains unchanged. Ex:

```java
Result<Integer, String> originalResult = Result.error("Error message");
Result<String, String> newResult = result.map(value -> value * 2);

-> newResult.isSuccessful() -> returns false
-> newResult.getError() -> returns "Error Message"
```

##### `.andThen()`
This should be used when chaining together functions that all return `Result` types and are conditional on the previous function call being successful.

```java
Result<Integer, String> resultOne = Result.ok(1);

Result<String, String> newResult =
  resultOne.andThen(value -> Result.ok(Integer.toString(value)))
           .andThen(value -> Result.ok(value.concat(", 2, 3")));

-> newResult.isSuccessful() -> returns true
-> newResult.get() -> returns "1, 2, 3"
```
The function `value -> Result.ok(value.concat(", 2, 3"))` is conditional on the value being a String. If any of the functions return a `Result.error` none of the other functions are applied. Ex:

```java
Result<ArrayList, String> newResult =
  resultOne.andThen(value -> Result.error("Error Message"))
           .andThen(value -> Result.ok(value.concat(", 2, 3")));

-> newResult.isSuccessful() -> returns false
-> newResult.getError() -> returns "Error Message"
```
The block `.andThen(value -> Result.ok(value.concat(", 2, 3")))` does not run and instead the `Result.error` is returned.

If you are not concerned with the value returned the double underscore can be used to denote this. Ex:

```java
Result<String, String> newResult = result.andThen(__ -> func1())
                                         .andThen(__ -> func2());
```

### Summary

Instead of throwing exceptions, functions can return a `Result` type instead, which contains a corresponding error message. This allows the developer to decide when/ if they would like to throw in a top level method or display an appropriate error message to the user.

### Additional Reading
[Rust Result Type](https://doc.rust-lang.org/rust-by-example/error/result.html)

[Elm Result Type](https://package.elm-lang.org/packages/elm/core/latest/Result)
