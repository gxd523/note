[TOC]

## 主要类
### LiveData
* 可添加多个Observer观察LifecycleOwner，Observer的Map集合
* `ObserverWrapper`集合，也就是`LifecycleBoundObserver`集合
* `dispatchingValue()`：根据version，`Lifecycle.State`判断是否回调`Observer.onChanged()`

### LifecycleOwner
* 实现类持有被观察者`LifecycleRegistry`

### LifecycleRegistry
* 被观察者，`Lifecycle`的实现类
* 拥有`LifecycleObserver`集合
* `handleLifecycleEvent()`：处理生命周期事件
* `State`：将生命周期转换为`CREATE`、`START`、`RESUME`、`DESTROY`
* `dispatchEvent()`：回调`LifecycleObserver.onStateChanged()`
* 

### LifecycleBoundObserver
* 观察者，`LifecycleObserver`的实现类

### ReportFragment
* 在生命周期方法回调`LifecycleRegistry`分发生命周期`Event`

## 过程
### 生命周期事件分发
* 通过注册名为`ProcessLifecycleOwnerInitializer`的`ContentProvider`在`onCreate()`中分别调用`LifecycleDispatcher`、`ProcessLifecycleOwner`的`init()`方法
* `LifecycleDispatcher`里通过`Application`注册`ActivityLifecycle`监听，在`onCreate()`回调中创建`ReportFragment`添加到`Activity`
* 在`ReportFragment`中通过`Activity`调用`LifecycleRegistry.handleLifecycleEvent()`
* `Event`转换为`State`，例如`onCreate`、`onStop`为`Create`，`onStart`、`onPause`为`Start`
* 根据`State`调用`dispatchEvent()`，`LifecycleBoundObserver.onStateChanged()`
* 最后调用`LiveData.activeStateChanged()`，回调用户设置的`Observer`集合

### setValue
* `dispatchingValue()`，遍历`LiveData`的`Observer`集合
* 通过`LifecycleRegistry.State`判断是否发送数据

### 删除Observer、LifecycleObserver
* 在`LifecycleObserver.onStateChanged()`里判断`Event`为`DESTROY`，调用`LiveData.removeObserver()`
* 先调用`LiveData.mObservers.remove()`删除`Observer`
* 再调用`detachObserver()`，`owner.getLifecycle().removeObserver()`删除`LifecycleObserver`

## LiveDataBus
* LiveData会跟随Activity销毁，不用显示注销，避免忘记注销导致内存泄漏
* LiveData感知生命周期，只有在界面可见时才发送数据
* LiveData粘性特性，导致订阅者会收到订阅之前未发出的消息
* 解决方式是：把LifecycleBoundObserver继承的ObserverWrapper里的mLastVersion的值设为LiveData的mVersion

```java
public enum LiveDataBus {
    INSTANCE;
    private final Map<String, BusMutableLiveData<Object>> bus;

    LiveDataBus() {
        this.bus = new HashMap<>();
    }

    public <T> MutableLiveData<T> with(String key, Class<T> type) {
        if (!bus.containsKey(key)) {
            bus.put(key, new BusMutableLiveData<>());
        }
        return (MutableLiveData<T>) bus.get(key);
    }

    public MutableLiveData<Object> with(String key) {
        return with(key, Object.class);
    }
}
```

```java
public class BusMutableLiveData<T> extends MutableLiveData<T> {
    private final Map<Observer<? super T>, Observer<? super T>> observerMap = new HashMap<>();

    @Override
    public void observe(@NonNull LifecycleOwner owner, @NonNull Observer<? super T> observer) {
        super.observe(owner, observer);
        try {
            hook(observer);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    @Override
    public void observeForever(@NonNull Observer<? super T> observer) {
        if (!observerMap.containsKey(observer)) {
            observerMap.put(observer, new BusObserverWrapper<>(observer));
        }
        super.observeForever(observerMap.get(observer));
    }

    @Override
    public void removeObserver(@NonNull Observer<? super T> observer) {
        Observer<? super T> realObserver;
        if (observerMap.containsKey(observer)) {
            realObserver = observerMap.remove(observer);
        } else {
            realObserver = observer;
        }
        super.removeObserver(realObserver);
    }
    /**
     * 把LifecycleBoundObserver继承的ObserverWrapper里的mLastVersion的值设为LiveData的mVersion
     */
    private void hook(@NonNull Observer<? super T> observer) throws Exception {
        Field mObserversField = LiveData.class.getDeclaredField("mObservers");
        mObserversField.setAccessible(true);
        Object mObservers = mObserversField.get(this);

        if (mObservers == null) {
            throw new NullPointerException("Can not find mObservers!");
        }

        Method getMethod = mObservers.getClass().getDeclaredMethod("get", Object.class);
        getMethod.setAccessible(true);
        // 拿到Entry<Observer, ObserverWrapper>
        Object mapEntry = getMethod.invoke(mObservers, observer);

        Object lifecycleBoundObserver = null;
        if (mapEntry instanceof Map.Entry) {
            lifecycleBoundObserver = ((Map.Entry) mapEntry).getValue();
        }
        if (lifecycleBoundObserver == null) {
            throw new NullPointerException("ObserverWrapper can not be bull!");
        }

        Class<?> ObserverWrapperClass = lifecycleBoundObserver.getClass().getSuperclass();
        Field mLastVersionField = ObserverWrapperClass.getDeclaredField("mLastVersion");
        mLastVersionField.setAccessible(true);

        Field mVersionField = LiveData.class.getDeclaredField("mVersion");
        mVersionField.setAccessible(true);
        Object mVersion = mVersionField.get(this);

        mLastVersionField.set(lifecycleBoundObserver, mVersion);
    }
}
```

```java
/**
 * 将Observer再包裹一层
 */
public class BusObserverWrapper<T> implements Observer<T> {
    private final Observer<T> observer;

    public BusObserverWrapper(Observer<T> observer) {
        this.observer = observer;
    }

    @Override
    public void onChanged(@Nullable T t) {
        if (observer != null) {
            if (isCallOnObserve()) {
                return;
            }
            observer.onChanged(t);
        }
    }

    private boolean isCallOnObserve() {
        StackTraceElement[] stackTrace = Thread.currentThread().getStackTrace();
        if (stackTrace.length > 0) {
            for (StackTraceElement element : stackTrace) {
                if (LiveData.class.getName().equals(element.getClassName())
                        && "observeForever".equals(element.getMethodName())) {
                    return true;
                }
            }
        }
        return false;
    }
}
```

