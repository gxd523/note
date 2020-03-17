#### onErrorReturn
> 发生错误时继续发射一个新的事件替代原有事件

#### onErrorResumeNext
> 发生错误时返回一个新的Observable

#### onExceptionResumeNext
> 同onErrorResumeNext，区别之处在于异常是Exception的子类时才会触发，例如OutOfMemoryError是Throwable的子类，所以不会触发

#### retry
> 如果原始Observable遇到错误，重新订阅它期望它能正常终止。就是Observable发生异常时（发送onError），捕获，并重新调用Observable的subject订阅方法，重新订阅。

#### retryUntil
> 类似于etry(new Predicate<Throwable>())，提供一个重试条件，true不重试，返回false重试

#### retryWhen
> 不同于retry，retryWhen需要返回一个Observable数据源，这个Observable发出onError则会继续走重试，除非是onNext或者onComplete