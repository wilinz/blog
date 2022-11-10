---
title: "Android toast 优化"
date: "2021-08-03"
---

解决原始toast连续弹出时需要等待上个toast弹完才能弹出来的问题，  
原理就是弹出新的toast前先取消之前的toast

```kotlin
import android.content.Context
import androidx.annotation.StringRes
import android.widget.Toast
import android.widget.Toast.LENGTH_SHORT
import java.lang.ref.WeakReference

private var toast: WeakReference<Toast>? = null

fun toast(context: Context, @StringRes resId: Int, duration: Int = LENGTH_SHORT) {
    toast(context, context.getString(resId), duration)
}

fun toast(context: Context, text: String, duration: Int = LENGTH_SHORT) {
    toast?.get()?.cancel()//取消之前的toast
    toast = WeakReference(Toast.makeText(context.applicationContext, text, duration))//创建新toast
    toast?.get()?.show()
}
```

## 效果：

![](images/20200302202136401.gif)
