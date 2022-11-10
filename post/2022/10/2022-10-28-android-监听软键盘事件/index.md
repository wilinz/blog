---
title: "Android 监听软键盘弹出收回事件"
date: "2022-10-28"
---

使用开源库 [https://github.com/yshrsmz/KeyboardVisibilityEvent](https://github.com/yshrsmz/KeyboardVisibilityEvent)

## 安装

AAR 通过 Maven Central 分发。最新版本点击 [Maven](https://maven-badges.herokuapp.com/maven-central/net.yslibrary.keyboardvisibilityevent/keyboardvisibilityevent)[](https://maven-badges.herokuapp.com/maven-central/net.yslibrary.keyboardvisibilityevent/keyboardvisibilityevent)

```kotlin
dependencies {
    implementation("net.yslibrary.keyboardvisibilityevent:keyboardvisibilityevent:3.0.0-RC3")
}
```

### 为键盘更改事件添加事件侦听器

#### [](https://github.com/yshrsmz/KeyboardVisibilityEvent#automatically-unregistering-the-event-on-the-activitys-ondestroy)在 Activity 的 onDestroy 上自动注销事件

```kotlin
KeyboardVisibilityEvent.setEventListener(
        getActivity(),
        new KeyboardVisibilityEventListener() {
            @Override
            public void onVisibilityChanged(boolean isOpen) {
                // some code depending on keyboard visiblity status
            }
        });
```

#### [](https://github.com/yshrsmz/KeyboardVisibilityEvent#automatically-unregistering-the-event-on-the-lifecycleowners-on_destroy)自动注销 LifecycleOwner 上的事件 `ON_DESTROY`

当您想从片段中获取 KeyboardVisibilityEvent 时，这很方便。

```kotlin
KeyboardVisibilityEvent.setEventListener(
        getActivity(),
        getLifecycleOwner(),
        new KeyboardVisibilityEventListener() {
            @Override
            public void onVisibilityChanged(boolean isOpen) {
                // some code depending on keyboard visiblity status
            }
        });
```

#### [](https://github.com/yshrsmz/KeyboardVisibilityEvent#manually-unregistering-the-event)手动注销事件

```kotlin
// get Unregistrar
Unregistrar unregistrar = KeyboardVisibilityEvent.registerEventListener(
        getActivity(),
        new KeyboardVisibilityEventListener() {
            @Override
            public void onVisibilityChanged(boolean isOpen) {
                // some code depending on keyboard visiblity status
            }
        });

// call this method when you don't need the event listener anymore
unregistrar.unregister();
```
