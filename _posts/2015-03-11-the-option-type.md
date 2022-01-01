---
layout: post
title: "The Option Type"
date: 2015-03-11
---

I've been writing some [Rust][rust] recently and have finally had my first experiences with the Option type, a data type that wraps either some value or nothing at all. In this post I'll explain a problem that optionals aim to alleviate, and show how they work in Rust.

## The Problem ##

Let's say I'm writing a method in Java that takes a list of names and returns the shortest one. [1]

```java
String getShortest(ArrayList<String> names) {
    String shortest = names.get(0);
    for (String name : names) {
        if (name.length() < shortest.length()) {
            shortest = name;
        }
    }
    return shortest;
}
```

This solution will compile and work fine in many cases. If we have a list of ("Dave" "LaToya" "Jake" "Ben") and ask for the shortest name, we'll get "Ben" back. But if we pass in an empty list, `names.get(0)` will throw an `IndexOutOfBoundsException`.

One way to prevent that exception is to first check if the list is empty, and return null in that case as follows.

```java
String getShortest(ArrayList<String> names) {
    String shortest = null;
    if (names.size() > 0) {
        shortest = names.get(0);
        for (String name : names) {
            if (name.length() < shortest.length()) {
                shortest = name;
            }
        }
    }
    return shortest;
}
```

Null, which is commonly defined to mean "nothing" or "undefined," makes some sense here. If I ask, "What's the shortest name in an empty list of names?" The answer is, "There isn't one." And it's OK to return null as far as the compiler is concerned, as null can stand in for any object in Java.

But returning null in this way is not very communicative. The method's signature says the method returns a String. It doesn't say anything about returning null when given an empty list. Consider the following situation:

```java
double doCalculation(ArrayList<String> names) {
    return getShortest(names).length() * Math.PI;
}

ArrayList<String> myNames = getNames(); // unfortunately, an empty list
double answer = doCalculation(myNames);
```

As a caller of `getShortest`, `doCalculation` should probably check if its list is empty before asking for the shortest name. But an empty list is an edge case that might not immediately occur to us. After we compile and run the program, if `doCalculation` does get called with an empty list, a runtime null-pointer exception will occur and possibly result in a crash.

When debugging the problem later, it won't be immediately obvious where that null came from. Running the above example with an empty list returned:

```bash
Exception in thread "main" java.lang.NullPointerException
    at MyProgram.doCalculation(MyProgram.java:19)
    at MyProgram.main(MyProgram.java:25)
```

The thought process then goes something like, "So a method was probably called on a null on line 19. So `getShortest(names)` must be null. So I must've called `getShortest` with an empty list. Therefore I need to either not send `getShortest` on an empty list, or do a null check on whatever it returns."

## The Option Option ##

Optional types are basically compiler-enforced null-checks. If a method returns an Option, any caller of that method must deal with it in some way or the code won't compile. As a bonus, the Option type in the method's signature is a dead giveaway to developers that the method may return null. This is very similar to the way checked exceptions work in Java.

Here's `getShortest` written in Rust: [2]

```rust
fn get_shortest(names: Vec<&str>) -> Option<&str> {
    if names.len() > 0 {
        let mut shortest = names[0];
        for name in names.iter() {
            if name.len() < shortest.len() {
                shortest = *name;
            }
        }
        Some(shortest)
    } else {
        None
    }
}
```

Instead of returning either a String or null as we did earlier, this version returns an Option that encloses either a String or null. Trying to use that Option as if it's a String results in a compilation error.

```rust
fn do_calculation(names: Vec<&str>) -> f64 {
    get_shortest(names).len() as f64 * std::f64::consts::PI
}
```

```bash
// compilation result
src/main.rs:16:25: 16:30 error: type `core::option::Option<&str>` does not implement any method in scope named `len`
src/main.rs:16     get_shortest(names).len() as f64 * std::f64::consts::PI
```

To get the enclosed String from the Option, we could call its `unwrap` method: `get_shortest(names).unwrap()`. However, using that method takes us even farther back than our old situation of a possible null pointer exception. If the Option is in fact a `None`, our program will panic and ([crash][thread-crash]) as soon as `unwrap` is called. It won't wait until some other method is called on the `None` later.

One of the preferred ways to unwrap an Option is to use a `match` (a convenient, switch-like statement) to deal with each case separately.

```rust
fn do_calculation(names: Vec<&str>) -> f64 {
    match get_shortest(names) {
        Some(x) => x.len() as f64 * std::f64::consts::PI,
        None    => // handle the null case here
    }
}
```

At a lower level, Rust's [Option type is actually an enum][enum-doc] with two possibilities: `Some(T)` and `None`. The `T` stands for the generic type—in our case, `&str`. We could define and use a similar enum of our own like this:

```rust
enum MyEnum<T> {
    Yes(T),
    No,
}

let thing1: MyEnum<&str> = MyEnum::Yes("some string");
let thing2: MyEnum<&str> = MyEnum::No;
```

If you're curious about the syntax of `Some(T)` and `Yes(T)`, check out the Rust Book's sections on [newtypes][newtypes] and [enums][enums]. The [match][match] and [generics][generics] chapters provide quite a bit of information about optional types in general.

## Concluding Thoughts ##

Optional types are not a catch-all solution for dealing with edge cases. Depending on the situation and the language, throwing an exception or implementing the [Null Object Pattern][null-object] might be a better choice (imagine having to unwrap the result every time you ask for the nth element of an array, just to prevent an "out of bounds" exception). But optionals are a nice tool to have. They communicate that a null return is possible, and force developers to handle that possibility one way or another.

[1] There are shorter ways to get the shortest String from a list in Java. Here's a Java 8 version:

```java
String getShortest(ArrayList<String> names) {
    return names.stream()
                .min(Comparator.comparing(String::length))
                .get();
}
```

[2] A shorter version using Rust:

```rust
fn get_shortest(names: Vec<String>) -> Option<String> {
    names.iter().min_by(|name| name.len())
}
```

[rust]: http://www.rust-lang.org
[enum-doc]: http://doc.rust-lang.org/std/option/enum.Option.html
[newtypes]: http://doc.rust-lang.org/book/compound-data-types.html#tuple-structs-and-newtypes
[enums]: http://doc.rust-lang.org/book/compound-data-types.html#enums
[match]: http://doc.rust-lang.org/book/match.html
[generics]: http://doc.rust-lang.org/book/generics.html
[thread-crash]: http://doc.rust-lang.org/book/error-handling.html
[null-object]: http://en.wikipedia.org/wiki/Null_Object_pattern
