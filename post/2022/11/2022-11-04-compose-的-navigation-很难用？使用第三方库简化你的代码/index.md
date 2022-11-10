---
title: "Compose 的 Navigation 很难用？如何简化你的代码？"
date: "2022-11-04"
---

[Navigation 组件](https://link.juejin.cn?target=https%3A%2F%2Fdeveloper.android.com%2Fguide%2Fnavigation)支持 [Jetpack Compose](https://link.juejin.cn?target=https%3A%2F%2Fdeveloper.android.com%2Fjetpack%2Fcompose) 应用。我们可以在利用 Navigation 组件的基础架构和功能，在可组合项之间导航。然而，在项目中使用之后，我发现这个东西真的很难用：

- 拼接路由名麻烦：导航组件的路由如果传递参数的话，需要按照规则拼接。
- 没有返回结果到上一个路由的Api，类似startActivityForResult。
- 定义参数和接收参数麻烦，需要写很多样板代码。
- 耦合：导航需要持有**NavHostController**，在可组合函数中，必须传递**NavHostController**才能导航，导致所有需要导航的可组合函数都要持有**NavHostController**的引用。传递`callback`也是同样的问题。
- 重构和封装变得困难：有的项目并不是一个全新的 Compose 项目，而是部分功能重写，在这种情况下，很难将NavHostController 提供给这些可组合项。
- 跳转功能麻烦，许多时候并不是单纯的导航到下一个页面，可能伴随 `replace`、`pop`、清除导航栈等，需要大量代码实现。
- `ViewModel`等非可组合函数不能获取**NavHostController**。
- 等等

好在强大的Github上有很多好用的第三方库，如 [https://github.com/raamcosta/compose-destinations](https://github.com/raamcosta/compose-destinations) ,

开发文档：[https://composedestinations.rafaelcosta.xyz/](https://composedestinations.rafaelcosta.xyz/)
