---
title: "封装compose textField"
date: "2021-12-25"
---

```kotlin
@Composable
    fun Username(username: String, onUsernameChange: (username: String) -> Unit) {
        val focusManager = LocalFocusManager.current
        var isFocused by remember {
            mutableStateOf(false)
        }
        OutlinedTextField(
            value = username,
            onValueChange = onUsernameChange,
            colors = TextFieldDefaults.textFieldColors(
                textColor = Color(0xFF000000),
                backgroundColor = Color.Transparent
            ),
            keyboardOptions = KeyboardOptions(imeAction = ImeAction.Next),
            keyboardActions = KeyboardActions(
                onNext = { focusManager.moveFocus(FocusDirection.Down) }
            ),
            shape = RoundedCornerShape(8.dp),//设置文本框圆角
            label = { Text(text = "账号") },
            trailingIcon = {
                IconButtonClear(username, isFocused) { onUsernameChange("") }
            },
            modifier = Modifier
                .fillMaxWidth()
                .onFocusChanged {
                    isFocused = it.isFocused
                },
            singleLine = true,

            )
    }
```

```kotlin
@Composable
    fun Password(password: String, onPasswordChange: (password: String) -> Unit) {
        var isFocused by remember {
            mutableStateOf(false)
        }
        var showPassword by remember {
            mutableStateOf(false)
        }
        val focusManager = LocalFocusManager.current

        OutlinedTextField(
            value = password,
            onValueChange = onPasswordChange,
            colors = TextFieldDefaults.textFieldColors(
                textColor = Color(0xFF000000),
                backgroundColor = Color.Transparent
            ),
            label = { Text(text = "密码") },
            shape = RoundedCornerShape(8.dp),//设置文本框圆角
            keyboardOptions = KeyboardOptions(imeAction = ImeAction.Done),
            keyboardActions = KeyboardActions(
                onDone = { focusManager.moveFocus(FocusDirection.Down) }
            ),
            trailingIcon = {
                if (password.isNotEmpty() && isFocused) {
                    Row {
                        IconButton(onClick = { showPassword = !showPassword }) {
                            Icon(
                                painter = painterResource(
                                    id = if (showPassword) R.drawable.ic_visibility else R.drawable.ic_visibility_off
                                ),
                                contentDescription = "clear",
                                tint = MaterialTheme.colors.primary
                            )
                        }
                        IconButtonClear(password, isFocused) { onPasswordChange("") }
                    }
                }
            },
            modifier = Modifier
                .onFocusChanged {
                    isFocused = it.isFocused
                }
                .fillMaxWidth(),
            visualTransformation = if (showPassword) VisualTransformation.None else PasswordVisualTransformation(),
            singleLine = true
        )
    }
```

```kotlin
@Composable
    fun Currency(currency: String, onCurrencyChange: (currency: String) -> Unit) {
        val  focusManager= LocalFocusManager.current
        var isFocused by remember {
            mutableStateOf(false)
        }
        OutlinedTextField(
            value = currency,
            onValueChange = { if (isRewardLegal(it)) onCurrencyChange(it) },
            colors = TextFieldDefaults.textFieldColors(
                textColor = Color(0xFF000000),
                backgroundColor = Color.Transparent
            ),
            keyboardOptions = KeyboardOptions(
                imeAction = ImeAction.Next,
                keyboardType = KeyboardType.Number
            ),
            keyboardActions = KeyboardActions(
                onNext = { focusManager.moveFocus(FocusDirection.Down) }
            ),
            shape = RoundedCornerShape(8.dp),//设置文本框圆角
            label = { Text(text = "金额") },
            placeholder = { Text(text = "0.00") },
            leadingIcon = {
                Text(text = "￥")
            },
            trailingIcon = {
                IconButtonClear(currency, isFocused) { onCurrencyChange("") }
            },
            modifier = Modifier
                .fillMaxWidth()
                .onFocusChanged {
                    isFocused = it.isFocused
                },
            singleLine = true,

            )
    }

fun isRewardLegal(reward: String): Boolean {
        val regex = Regex("^([1-9]\\d{0,2}|0)(\\.\\d{1,2}|\\.\$)?\$|^$")
        return reward.matches(regex)
    }
```

```kotlin
    @Composable
    fun Code(
        code: String,
        onCodeChange: (code: String) -> Unit,
        duration: Int = 0,
        trailingIconText: String,
        onTrailingIconClick: () -> Unit
    ) {
        val focusManager = LocalFocusManager.current
        var isFocused by remember {
            mutableStateOf(false)
        }

        OutlinedTextField(
            value = code,
            onValueChange = { if (isCodeLegal(it)) onCodeChange(it) },
            colors = TextFieldDefaults.textFieldColors(
                textColor = Color(0xFF000000),
                backgroundColor = Color.Transparent
            ),
            keyboardOptions = KeyboardOptions(
                imeAction = ImeAction.Next,
                keyboardType = KeyboardType.Number
            ),
            keyboardActions = KeyboardActions(
                onNext = { focusManager.moveFocus(FocusDirection.Down) }
            ),
            shape = RoundedCornerShape(8.dp),//设置文本框圆角
            label = { Text(text = "验证码") },
            trailingIcon = {
                Row(
                    verticalAlignment = Alignment.CenterVertically,
                    modifier = Modifier
                        .padding(end = 16.dp)
                        .padding()
                ) {
                    IconButtonClear(code, isFocused) { onCodeChange("") }
                    if (duration <= 0) {
                        Text(
                            text = trailingIconText,
                            color = MaterialTheme.colors.primary,
                            modifier = Modifier
                                .clip(RoundedCornerShape(8.dp))
                                .clickable {
                                    onTrailingIconClick()
                                }
                                .padding(8.dp),
                            )
                    } else {
                        Text(
                            text = "${duration}s",
                            color = MaterialTheme.colors.primary,
                        )
                    }
                }
            },
            modifier = Modifier
                .fillMaxWidth()
                .onFocusChanged {
                    isFocused = it.isFocused
                },
            singleLine = true,

            )
    }

 fun isCodeLegal(reward: String): Boolean {
        val regex = Regex("\\d{0,6}|^$")
        return reward.matches(regex)
    }
```
