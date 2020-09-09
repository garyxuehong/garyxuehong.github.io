---
layout: post
title: "How JavaVM load class with orders"
author: Gary
tags: ["Java"]
image: ./03-class-loading-in-vm.banner.jpg
date: "2019-01-02T23:40:37.000Z"
draft: false
---

Recently I learned when Java class loader `load` a class and when does it `initialize` it.

Take the following as example.

We can use some special jvm options to show class loading info, like `java -verbose:class Singleton`

```java
public class Singleton {
  private Singleton() {}
  private static class LazyHolder {
    static final Singleton INSTANCE = new Singleton();
    static {
      System.out.println("LazyHolder.<clinit>");
    }
  }
  public static Object getInstance(boolean flag) {
    if (flag) return new LazyHolder[2];
    return LazyHolder.INSTANCE;
  }
  public static void main(String[] args) {
    getInstance(true);
    System.out.println("----");
    getInstance(false);
  }
}
```

When execute this class, class loader will first load `Singleton`, then load `LazyHolder`. But something interesting here is jvm will not `clinit` the `LazyHolder` when constructing an empty `Array` of `LazyHolder`. It will `clinit` the class when it is being really used, like `return LazyHolder.INSTANCE`.

We can try the above with script at the end.

1. It will output as following order

`[0.123s][info][class,load] Singleton source: file:/Users/xueg/source/fun/javavm/03-how-class-load/`
`...`
`[0.125s][info][class,load] Singleton$LazyHolder source: file:/Users/xueg/source/fun/javavm/03-how-class-load/`

Above indicates that the order the classed loaded is `Singleton` --> `Singleton$LazyHolder`

2. and continue output following

`----`

This means the constructing of new LazyHolder array `new LazyHolder[2]` is called. But the `static{}` init code of the LazyHolder is still not being called.

3. and continue output following

```
java.lang.VerifyError: Operand stack overflow
Exception Details:
  Location:
    Singleton$LazyHolder.<init>()V @0: aload_0
  Reason:
    Exceeded max stack size.
  Current Frame:
    bci: @0
    flags: { flagThisUninit }
    locals: { uninitializedThis }
    stack: { }
  Bytecode:
    0000000: 2ab7 0007 b1

        at Singleton.getInstance(Singleton.java:12)
        at Singleton.main(Singleton.java:17)
```

This is because we mod the bytecode on purpose to show that even there's an error in the init code, it doesn't prevent jvm from loading the class

Full scripts as follow.

```bash
#!/bin/bash

echo "Generating Singleton.java"
echo '
public class Singleton {
  private Singleton() {}
  private static class LazyHolder {
    static final Singleton INSTANCE = new Singleton();
    static {
      System.out.println("LazyHolder.<clinit>");
    }
  }
  public static Object getInstance(boolean flag) {
    if (flag) return new LazyHolder[2];
    return LazyHolder.INSTANCE;
  }
  public static void main(String[] args) {
    getInstance(true);
    System.out.println("----");
    getInstance(false);
  }
}' > Singleton.java

echo "Compiling..."
javac Singleton.java

echo "Run with class loading order printed"
java -verbose:class Singleton

echo "Reading LazyHolder"
java -cp ~/install/asmtools.jar org.openjdk.asmtools.jdis.Main Singleton\$LazyHolder.class > Singleton\$LazyHolder.jasm.1

echo "Mod bytecode to trigger error in the static init block of LazyHolder"
awk 'NR==1,/stack 1/{sub(/stack 1/, "stack 0")} 1' Singleton\$LazyHolder.jasm.1 > Singleton\$LazyHolder.jasm

echo "Put back moded jasm"
java -cp ~/install/asmtools.jar org.openjdk.asmtools.jasm.Main Singleton\$LazyHolder.jasm

echo "Run with new result"
java -verbose:class Singleton
```

Refs:
1. Download asmtools (https://wiki.openjdk.java.net/display/CodeTools/How+to+build+AsmTools)
2. This article is mainly quoted from origin tutoria (https://time.geekbang.org/column/article/11523)


