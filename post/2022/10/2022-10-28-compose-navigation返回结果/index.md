---
title: "Compose Navigation返回结果"
date: "2022-10-28"
---

```kotlin
//封装获取结果函数
@Composable
fun <T> NavController.GetOnceResult(keyResult: String, onResult: (T) -> Unit){
    val valueScreenResult =  currentBackStackEntry
        ?.savedStateHandle
        ?.getStateFlow<T?>(keyResult,null)?.collectAsState()

    valueScreenResult?.value?.let {
        onResult(it)

        currentBackStackEntry
            ?.savedStateHandle
            ?.remove<T>(keyResult)
    }
}

//设置结果
navHostController.previousBackStackEntry?.savedStateHandle?.let {
    it["some_key"] = "test"
}
navHostController.popBackStack()

//获取结果
navController.GetOnceResult<String>("some_key"){
    ...
    // make something
}
```
