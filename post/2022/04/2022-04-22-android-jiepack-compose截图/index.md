---
title: "Android jiepack compose截图"
date: "2022-04-22"
---

```kotlin
import android.graphics.Bitmap
import android.view.View
import androidx.compose.material.Scaffold
import androidx.compose.material.Text
import androidx.compose.material.TextButton
import androidx.compose.material.TopAppBar
import androidx.compose.runtime.*
import androidx.compose.ui.Modifier
import androidx.compose.ui.geometry.Rect
import androidx.compose.ui.layout.boundsInRoot
import androidx.compose.ui.layout.onGloballyPositioned
import androidx.compose.ui.platform.LocalView
import androidx.core.graphics.applyCanvas
import kotlinx.coroutines.delay
import kotlinx.coroutines.launch
import kotlin.math.roundToInt

@Composable
fun Content() {
    var capturingViewBounds by remember { mutableStateOf<Rect?>(null) }
    val view = LocalView.current
    val scope = rememberCoroutineScope()
    Scaffold(
        topBar = {
            TopAppBar(title = { Text(text = "screenshot") }, actions = {
                TextButton(onClick = {
                    scope.launch {
                        delay(500)//防止截到点击水波纹特效
                        capturingViewBounds?.let { screenshot(it, view) }
                    }
                }) {
                    Text(text = "shot")
                }
            })
        },
        modifier = Modifier
            .onGloballyPositioned {
                capturingViewBounds = it.boundsInRoot()
            }
    ) {
        Text(text = "test")
    }
}

private fun screenshot(
    capturingViewBounds: Rect,
    view: View
): Bitmap {
    val bounds = capturingViewBounds
    val image = Bitmap.createBitmap(
        bounds.width.roundToInt(), bounds.height.roundToInt(),
        Bitmap.Config.ARGB_8888
    ).applyCanvas {
        translate(-bounds.left, -bounds.top)
        view.draw(this)
    }
    return image
}
```
