---
layout: post
title: "How JavaVM treat boolean values"
author: Gary
tags: ["Java"]
image: img/welcome-to-ghost.jpg
date: "2019-01-01T22:02:37.000Z"
draft: false
---

TL:DR;

jvm will use `int 1` as the constant value the represen boolean `true`.
But jvm also treat any postive integer value as true, like c.

Take the following code as example,

```java
public class Foo {
 public static void main(String[] args) {
  boolean flag = true;
  if (flag) System.out.println("Hello, Java!");
  if (flag == true) System.out.println("Hello, JVM!");
 }
}
```

By default, javac will put `1` as value in the variable `flag`. So the comparission `if(flag == true)` will be translated to `if(1 == 1)` in jvm.

However, if we use asmtools to modified the value of flag to be `2`, then the condition `if(flag == true)` will be evaluated as `false`, but the condition `if(flag)`, which is translated to `if(2)`, will still hold true.

You can run the example in the following bash scripts.

```bash
#!/bin/bash

echo ">>gen code..."

echo '
public class Foo {
 public static void main(String[] args) {
  boolean flag = true;
  if (flag) System.out.println("Hello, Java!");
  if (flag == true) System.out.println("Hello, JVM!");
 }
}' > Foo.java


echo ">>javac Foo.java"
javac Foo.java

echo ">>Run unmoded class"
java Foo

echo ">>Read class file with asmtools"
java -cp ~/install/asmtools.jar org.openjdk.asmtools.jdis.Main Foo.class > Foo.jasm.1

echo ">>Print it out"
cat Foo.jasm.1

echo ">>Awk replce generated jasm file??"
awk 'NR==1,/iconst_1/{sub(/iconst_1/, "iconst_2")} 1' Foo.jasm.1 > Foo.jasm

echo ">>Print moded jasm file"
cat Foo.jasm

echo ">>Print diff"
diff --unified=3 Foo.jasm.1 Foo.jasm

echo ">>Use asmtools to compile mod jasm file"
java -cp ~/install/asmtools.jar org.openjdk.asmtools.jasm.Main Foo.jasm

echo ">>Run mod class"
java Foo
```

Refs:
1. Download asmtools (https://wiki.openjdk.java.net/display/CodeTools/How+to+build+AsmTools)
2. This article is mainly quoted from origin tutoria (https://time.geekbang.org/column/article/11289)

