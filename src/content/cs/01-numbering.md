---
layout: post
title: "Proper Numbering in Javascript"
author: Gary
tags: ["Javascript"]
image: ./02-es6-arrow-functions.jpg
date: "2021-03-02T21:23:37.000Z"
draft: true
---

# IEEE Floating Point Format

**Notes**: this section is largely COPIED from [mimosa.org](https://www.mimosa.org/ieee-floating-point-format/) except I change the number and do the calculation myself for the sake of self learning and practice. 

Some interesting reference examples:

1. [mimosa.org](https://www.mimosa.org/ieee-floating-point-format/)
2. [ic.ac.uk](https://www.doc.ic.ac.uk/~eedwards/compsys/float/)

# How to convert a decimal to floating points

Let's consider a number if when written in base of 2, it is `11.110101`, we can do some calculation to know that it's decimal value is `1*2^1 + 1*2^0 + 1*2^-1 + 1*2^-2 + 1*2^-4 + 1*2^-6`, which is `3.828125`. 

Next, we normalised the number (of base 2) to `1.1110101 * 2E1`

As for floating32 binary format, it store the binary in following bits: [Reference from mimosa](https://www.mimosa.org/ieee-floating-point-format/)

```
     Sign  Exponent   Fraction 
     0     00000000   00000000000000000000000
Bit  31    [30 - 23]  [22        -         0]
```

We can now construct the format of the binary number `1.1110101 * 2^1` as :

Notes:
  1. For the exponent part `1` in the `2E1`, we always add `127` on top of it (?? why?).
  2. For the Fration part, we always leave out the leading `1.` as it is implied.

So final binary format resulted in: 

```
     Sign  Exponent   Fraction 
     0     10000000   11101010000000000000000
Bit  31    [30 - 23]  [22        -         0]
```

and putting it in the hex will be

`0100 0000 0111 0101 0000 0000 0000 0000`

To verify it, we can write a simple program to output the binary format

```java
  public static void main(String[] args) {
      System.out.println(Integer.toBinaryString(Float.floatToRawIntBits(3.828125f)));
  }
```

