---
layout: post
title: "Why Are JS Numbers Bad With $$$?"
date: 2016-04-25
---

__TLDR:__ Because they are base-2 and our money system is base-10.

We think of change as fractions of a dollar.
A dime is 1/10 or $0.1.
Three dimes together should equal 3/10 or $0.3 then, right?

```
0.1 + 0.1 + 0.1
0.30000000000000004
```

Nope. [JS Numbers are stored in base-2 floating point format](http://www.ecma-international.org/ecma-262/6.0/index.html#sec-terms-and-definitions-number-value) which means they can't store many common base-10 fractions.

## It Depends On Your Base ##

In base-10, it's easy to get from 1/10 to its decimal representation.
Just divide 1 by 10.

```
   0.1
  +----
10|1.0
   -10
   ---
     0
```

But in base-2, you end up with a repeat.

```
     0.000110011...
    +------------
1010|1.000000000
      -1010
     ------
        1100
       -1010
        ----
          10000
          -1010
          -----
            1100
           -1010
            ----
              10
```

This is just like trying to represent 1/3 in base-10.

```
  0.333...
 +------
3|1.000
   -9
  ---
    10
    -9
    --
     10
```

If we go to base-3, 1/3 gets a lot easier though.

```
   0.1
  +----
10|1.0
   -10
   ---
     0
```

As it turns out, a base-10 number system can only represent numbers whose denominators reduce to products of 2's and 5's. So 1/2 is 0.5, 1/4 is 0.25, and 1/100 is 0.01. But 1/11? 0.090909...

Similarly, a base-2 system can only represent numbers whose denominators reduce to products of 2's. 1/2 is 0.1, 1/4 is 0.01, but 1/10 is 0.0001100110011...

How do you store a non-terminating number using a finite number of bits? By rounding to a number you _can_ store. In base 10, 2/3 might become 0.666667, which is not equal to 2/3.
Over time, these rounding errors can build up to something noticeable.
This is what happened with 0.1 + 0.1 + 0.1 above.
