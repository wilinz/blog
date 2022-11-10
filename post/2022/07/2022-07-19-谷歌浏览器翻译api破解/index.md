---
title: "谷歌浏览器翻译API破解"
date: "2022-07-19"
---

```kotlin
package com.example

import okhttp3.FormBody
import okhttp3.OkHttpClient
import okhttp3.Request

const val googleTranslateTKK = "448487.932609646";
const val url = "https://translate.googleapis.com/translate_a/t?anno=3&client=te&v=1.0&format=html&sl=auto&tl="

fun main() {
    val queryList = listOf("hello world")
    val tk = (calcHash(queryList.joinToString(""), googleTranslateTKK))
    println(tk)
    val targetLanguage = "zh-CN"

    val formBodyBuilder = FormBody.Builder()
    queryList.forEach {
        formBodyBuilder.add("q", it)
    }
    val request = Request.Builder()
        .url("$url$targetLanguage&tk=$tk")
        .header("Content-Type", "application/x-www-form-urlencoded")
        .post(formBodyBuilder.build())
        .build()
    val response = OkHttpClient().newCall(request).execute()
    println(response.body?.string())
}

fun calcHash(query: String, windowTkk: String): String {
    val tkkSplited = windowTkk.split('.');
    val tkkIndex = tkkSplited[0].toInt()
    val tkkKey = tkkSplited[1].toLong()

    val bytesArray = query.toByteArray().map { it.toUByte() }

    var encondingRound = tkkIndex.toLong();
    for (item in bytesArray) {
        encondingRound += item.toInt()
        encondingRound = shiftLeftOrRightThenSumOrXor(encondingRound.toInt(), "+-a^+6");
    }
    encondingRound = shiftLeftOrRightThenSumOrXor(encondingRound.toInt(), "+-3^+b+-f");

    encondingRound = (encondingRound xor tkkKey)
    if (encondingRound <= 0) {
        encondingRound = ((encondingRound and 2147483647) + 2147483648)
    }

    val normalizedResult = encondingRound % 1000000;
    return normalizedResult.toString() + '.' + (normalizedResult xor tkkIndex.toLong())
}

fun shiftLeftOrRightThenSumOrXor(num: Int, optString: String): Long {
    var num1 = num
    var i = 0
    while (i < optString.length - 2) {
        var acc = optString[i + 2].code
        if ('a'.code <= acc) {
            acc -= 87;
        } else {
            acc = optString[i + 2].digitToInt()
        }
        acc = if (optString[i + 1] == '+') {
            (num1 ushr acc)
        } else {
            (num1 shl acc)
        }
        num1 = if (optString[i] == '+') {
            num1 + (acc.toLong() and 4294967295).toInt()
        } else {
            num1 xor acc
        }
        i += 3
    }
    return num1.toUInt().toLong()
}
```
