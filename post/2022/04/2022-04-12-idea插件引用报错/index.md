---
title: "Idea插件引用Dom4j报错"
date: "2022-04-12"
---

```
java.lang.ClassCastException: class org.apache.xerces.parsers.SAXParser cannot be cast to class org.xml.sax.XMLReader (org.apache.xerces.parsers.SAXParser is in unnamed module of loader com.intellij.util.lang.PathClassLoader @5abca1e0; org.xml.sax.XMLReader is in unnamed module of loader com.intellij.ide.plugins.cl.PluginClassLoader @71b14149)
```

在build.gradle.kts文件添加下面代码排除 xml-apis 依赖即可

```
configurations.all { exclude ("xml-apis","xml-apis")}
```
