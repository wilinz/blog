---
title: "Jetpack Compose 中的键盘处理——所有你需要知道的"
date: "2022-10-28"
---

原文链接：[https://blog.canopas.com/keyboard-handling-in-jetpack-compose-all-you-need-to-know-3e6fddd30d9a](https://blog.canopas.com/keyboard-handling-in-jetpack-compose-all-you-need-to-know-3e6fddd30d9a)

## 无论您是初学者还是经验丰富的开发人员，您都会发现自己在处理键盘 API。这是掌握它的指南。

![Jetpack Compose 中的键盘处理——所有你需要知道的](images/1*B1lDJXAiqJaROI1NVZzwVw.png)

Jetpack compose 中的键盘处理

在本文中，我们将学习在 Jetpack compose 中管理键盘的重要用例。

**我们将介绍：**

1.如何隐藏和显示软键盘。

2\. 键盘出现时如何调整布局。

3\. 键盘动作

4\. 键盘选项

5.如何检测键盘？

**让我们开始吧！**

# 1.如何隐藏和显示软键盘

当我们点击 TextField 时，键盘会弹出，输入文本后，我们想关闭键盘。

## **a. **KeyboardController****

在 jetpack compose 中，我们有`**LocalSoftwareKeyboardController**`API 来控制键盘的可见性。此 API 被标记为 ExperimentalComposeUiApi，因此将来可能会更改。

```kotlin
val keyboardController = LocalSoftwareKeyboardController.current
//To hide keyboard
keyboardController?.hide()
//To show keyboard
keyboardController?.show()
```

通过隐藏和显示请求，`SoftwareKeyboardController`我们可以控制键盘的可见性。

如果我们有带有键盘 ImeAction.Done 的 TextField，那么我们需要`hide()`在键盘操作的回调中调用 call。

## b. LocalFocusManager

此外，我们可以通过从 TextField**移除焦点**来关闭键盘

```kotlin
val focusManager = LocalFocusManager.current
var text by remember {
    mutableStateOf("")
}

TextField(
    value = text,
    onValueChange = { text = it },
    keyboardOptions = KeyboardOptions(imeAction = ImeAction.Done),
    keyboardActions = KeyboardActions(
        onDone = { focusManager.clearFocus() }),
)
```

这将关闭键盘并从 TextField 中清除焦点，而`SoftwareKeyboardController`仅关闭键盘而不从任何 TextField 中移除焦点。

# 2\. 出现键盘时如何调整布局

键盘在出现时会减少应用程序 UI 的可用空间。

当我们有太多的文本字段时，例如，我们有 5 到 6 个文本字段来输入用户详细信息的注册表单，可能并非所有字段都可见，一些文本字段可能会隐藏在键盘后面。那么如何预防呢？

## **a. **windowSoftInputMode****

使用该`windowSoftInputMode`属性，我们可以配置我们的键盘，它将如何影响我们的 UI 及其可见性状态。

在清单文件中设置`**adjustResize**`为`windowSoftInputMode`属性。

```xml
<activity
    android:name=".MainActivity"
    android:windowSoftInputMode="adjustResize"
    .../>
```

系统会根据可用空间调整布局大小，以便在键盘出现在屏幕上时可以访问内容。

## b. BringIntoViewRequester

通过`BringIntoViewRequester`我们可以自动滚动焦点更改以使 TextField 完全可见。`BringIntoViewRequester`是一个实验性 API，将来可能会改变。

![](images/1*HsrhyAz1j2wizrsjUAjsrQ.png)

这里的文本字段 9 不完全可见，`BringIntoViewRequester`自动滚动项目并带入父边界。让我们看看如何添加`BringIntoViewRequester`

```kotlin
val state = rememberLazyListState()
LazyColumn(
    state = state,
    modifier = Modifier.fillMaxSize(),
    horizontalAlignment = Alignment.CenterHorizontally
){
    items(12) { item ->
        YourTextField(item)
        Spacer(modifier = Modifier.height(10.dp))
    }
}
//YourTextField
@OptIn(ExperimentalFoundationApi::class)
@Composable
fun YourTextField(index: Int) {
  
    val bringIntoViewRequester = remember { BringIntoViewRequester() }
    val coroutineScope = rememberCoroutineScope()
    TextField(
       /* ... your text field value and onValueChange callback */   
      modifier = Modifier
            .bringIntoViewRequester(bringIntoViewRequester)
            .onFocusEvent { focusState ->
                if (focusState.isFocused) {
                    coroutineScope.launch {
                        bringIntoViewRequester.bringIntoView()
                    }
                }
            }
    )
}
```

`Modifier._bringIntoViewRequester_(bringIntoViewRequester)`演示了可组合项如何要求其父级滚动，以便将使用此修饰符的组件带入其所有父级的边界。

![](images/1*aHT-zDUmphJs9B3dmUcb2A.gif)

BringIntoViewRequester 仅适用于`windowSoftInputMode=”adjustResize”`

## c. imePadding

Ime 填充**修饰符**允许您将键盘填充应用于可组合。

在下面的示例中，我们在底部有一个按钮`Column`

```kotlin
Column(
    horizontalAlignment = Alignment.CenterHorizontally,
    modifier = Modifier
        .fillMaxSize()
        .padding(10.dp)
) {
    TextField(value = "", onValueChange = {})
    Spacer(Modifier.weight(1f))
    Button(onClick = {}) {
        Text(text = "Yeah Visible")
    }
}
```

不设置`**adjustResize**`属性`windowSoftInputMode`，按钮隐藏在键盘后面。

![](images/1*gUoOCFkr-wAaB4kCroYx2w.gif)

现在让我们设置`imePadding`在键盘出现时移动按钮。我们还需要设置`WindowCompat.setDecorFitsSystemWindows(_window_, false)`

```kotlin
Column(
    horizontalAlignment = Alignment.CenterHorizontally,
    modifier = Modifier
        .fillMaxSize()
        .statusBarsPadding()
        .navigationBarsPadding()
        .imePadding()
        .padding(10.dp)
) {
    TextField(value = "", onValueChange = {})
    Spacer(Modifier.weight(1f))
    Button(onClick = {}) {
        Text(text = "Yeah Visible")
    }
}
```

这是结果

![](images/1*LasTNUd-HDHnAFVKRFfdlw.gif)

# 3\. Keyboard options

`keyboardOptions`在 TextField 中允许您配置键盘选项，例如自动大写、自动更正、您希望 TextField 和 IME 操作按钮使用的键盘类型。例如，对于密码 TextField，您既不想大写输入，也不想自动更正功能。

```kotlin
keyboardOptions = KeyboardOptions.Default.copy(
    capitalization = KeyboardCapitalization.None,
    autoCorrect = false,
    keyboardType = KeyboardType.Password,
    imeAction = ImeAction.Done
)
```

**大写**——自动将字符、单词或句子大写。  
**autoCorrect** — 通知键盘是否启用自动更正。  
**keyboardType** — 要在此文本字段中使用的键盘类型。例如，对于密码 TextField，键盘类型是`KeyboardType.Password`  
**imeAction** — 应该在键盘上显示什么类型的操作按钮。

# 4\. Keyboard actions

键盘动作用于检测键盘输入法动作按钮的点击事件。有 6 个键盘操作可用。

```kotlin
keyboardActions = KeyboardActions(
    onDone = { },
    onGo = { },
    onNext = { },
    onPrevious = { },
    onSearch = { },
    onSend = { },
)
```

例如，如果您想关注下一个文本字段，`ImeAction.Next`点击时，您将收到回调`onNext`

```kotlin
TextField(
    value = text,
    onValueChange = { text = it },
    keyboardOptions = KeyboardOptions(imeAction = ImeAction.Next),
    keyboardActions = KeyboardActions(
        onNext = { focusManager.moveFocus(FocusDirection.Down) }),
)
```

# 5.如何检测键盘

`WindowInsets`我们可以检查键盘是否存在于屏幕容器中。返回以像素为单位的底部空间。  
`WindowInsets._ime_.getBottom(_LocalDensity_.current)`

```kotlin
val isVisible = WindowInsets.ime.getBottom(LocalDensity.current) > 0
LaunchedEffect(key1 = isVisible) {
    if (isVisible) {
        //hide fab button
    } else {
        //show fab button
    }
}
```

而已。希望现在您对 Jetpack compose 中与键盘 API 的常见交互有基本的了解。随时在下面的评论部分添加您的宝贵反馈和建议。

谢谢你的支持！

如果**你**喜欢你读到的东西，一定要在下面👏 👏👏 它——作为一个作家，它意味着**世界**！
