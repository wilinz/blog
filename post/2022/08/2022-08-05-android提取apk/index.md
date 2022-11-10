---
title: "Android提取apk"
date: "2022-08-05"
---

```kotlin
val file = File(context.packageManager.getApplicationInfo(packageName,0).sourceDir)
```
