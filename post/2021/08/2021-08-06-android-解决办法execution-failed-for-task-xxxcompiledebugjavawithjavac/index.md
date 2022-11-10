---
title: "Android 解决办法Execution failed for task ':xxx:compileDebugJavaWithJavac'."
date: "2021-08-06"
---

当我把compileSdkVersion
targetSdkVersion都设置成31后，构建项目直接报Execution failed for task ':xxx:compileDebugJavaWithJavac'.
经过一番折腾把jdk版本从jdk8改成jdk11就不报错了，可能是Android12需要jdk11环境
