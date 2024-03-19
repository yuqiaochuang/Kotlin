原文地址：https://blog.csdn.net/mp624183768/article/details/128868434
#### CoroutineScope

[CoroutineScope](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-coroutine-scope/ "CoroutineScope") 会跟踪它使用 `launch` 或 `async` 创建的所有协程。您可以随时调用 `scope.cancel()` 以取消正在进行的工作（即正在运行的协程）。在 Android 中，某些 KTX 库为某些生命周期类提供自己的 `CoroutineScope`。例如，`ViewModel` 有 [viewModelScope](https://developer.android.google.cn/reference/kotlin/androidx/lifecycle/package-summary?hl=zh-cn#%28androidx.lifecycle.ViewModel%29.viewModelScope:kotlinx.coroutines.CoroutineScope "viewModelScope")，`Lifecycle` 有 [lifecycleScope](https://developer.android.google.cn/reference/kotlin/androidx/lifecycle/package-summary?hl=zh-cn#lifecyclescope "lifecycleScope")。不过，与调度程序不同，`CoroutineScope` 不运行协程。

**注意**：如需详细了解 `viewModelScope`，请参阅 [Android 中的简易协程：viewModelScope](https://medium.com/androiddevelopers/easy-coroutines-in-android-viewmodelscope-25bffb605471 "Android 中的简易协程：viewModelScope")。

`viewModelScope` 也可用于 [Android 上采用协程的后台线程](https://developer.android.google.cn/kotlin/coroutines?hl=zh-cn "Android 上采用协程的后台线程")中的示例内。但是，如果您需要创建自己的 `CoroutineScope` 以控制协程在应用的特定层中的生命周期，则可以创建一个如下所示的 CoroutineScope：

```kotlin
class ExampleClass {
 
    // Job and Dispatcher are combined into a CoroutineContext which
    // will be discussed shortly
    val scope = CoroutineScope(Job() + Dispatchers.Main)
 
    fun exampleMethod() {
        // Starts a new coroutine within the scope
        scope.launch {
            // New coroutine that can call suspend functions
            fetchDocs()
        }
    }
 
    fun cleanUp() {
        // Cancel the scope to cancel ongoing coroutines work
        scope.cancel()
    }
}
```

已取消的作用域无法再创建协程。因此，仅当控制其生命周期的类被销毁时，才应调用 `scope.cancel()`。使用 `viewModelScope` 时，[ViewModel](https://developer.android.google.cn/topic/libraries/architecture/viewmodel?hl=zh-cn "ViewModel") 类会在 [ViewModel](https://so.csdn.net/so/search?q=ViewModel\&spm=1001.2101.3001.7020) 的 `onCleared()` 方法中自动为您取消作用域。