---
title: "java unicode็ธๅณ"
date: "2022-07-27"
---

```kotlin
    String myStr = "1๐3";
    System.out.println(myStr.length()); // print 4, because ๐ is two char
    System.out.println(myStr.codePointCount(0, myStr.length())); //print 3, factor in all unicode
    
    int result = myStr.codePointAt(0);
    System.out.println(Character.toChars(result)); // print 1
    
    result = myStr.codePointAt(1);
    System.out.println(Character.toChars(result)); // print ๐, because codePointAt will get surrogate pair (high and low)
    
    result = myStr.codePointAt(2);
    System.out.println(Character.toChars(result)); // print low surrogate of ๐ only, in this case it show "?"
    
    result = myStr.codePointAt(3);
    System.out.println(Character.toChars(result)); // print 3
```
