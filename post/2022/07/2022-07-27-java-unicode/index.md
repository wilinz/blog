---
title: "java unicode相关"
date: "2022-07-27"
---

```kotlin
    String myStr = "1💖3";
    System.out.println(myStr.length()); // print 4, because 💖 is two char
    System.out.println(myStr.codePointCount(0, myStr.length())); //print 3, factor in all unicode
    
    int result = myStr.codePointAt(0);
    System.out.println(Character.toChars(result)); // print 1
    
    result = myStr.codePointAt(1);
    System.out.println(Character.toChars(result)); // print 💖, because codePointAt will get surrogate pair (high and low)
    
    result = myStr.codePointAt(2);
    System.out.println(Character.toChars(result)); // print low surrogate of 💖 only, in this case it show "?"
    
    result = myStr.codePointAt(3);
    System.out.println(Character.toChars(result)); // print 3
```
