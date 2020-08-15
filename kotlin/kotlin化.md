###   概述  


写了一年的kotlin代码，感觉还是在写java，一点都不kotlin，这里整理收集一些平时遇到的不错的kotlin代码，以便思考。   


##### 多类型定义  

从最开始的定义常量1、2、3等表示类型，到后来的@IntDef注解定义。其实koltin中应该使用`seal`来表示！

网络请求，一般只有两种结果，一个是成功，一个是失败。

```kotlin
sealed class Result<out T : Any> {
    data class Success<out T : Any>(val data: T) : Result<T>()
    data class Error(val exception: Exception) : Result<Nothing>()

    override fun toString(): String {
        return when (this) {
            is Success -> "Success[data = $data]"
            is Error -> "Error[exception = $exception]"
        }
    }
}
```  

##### if判断

```kotlin
inline fun <T : Any> Result<T>.checkResult(
    crossinline onSuccess: (T) -> Unit,
    crossinline onError: (String?) -> Unit
) {
    if (this is Result.Success) {
        onSuccess(data)
    } else if (this is Result.Error) {
        onError(exception.message)
    }
}

result.checkResult(
	onSuccess = {
                    UserManager.notifyLogin(it)
                    uiState.value = LoginUiState(isSuccess = it)
                },
   onError = {
                 uiState.value = LoginUiState(isError = it)
             }
)

这个判断result是哪个类型，执行对应的方法。和下面的写法，没有任务区别。但是，感觉kotlin化的代码确实看起来含义更明显一点

if (result is Result.Success) {
	UserManager.notifyLogin(it)
   uiState.value = LoginUiState(isSuccess = it)
} else if (this is Result.Error) {
	uiState.value = LoginUiState(isError = it)
}
```
