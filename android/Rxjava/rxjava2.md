[TOC]

### 被观察者
#### Observable
#### Flowable
> 支持背压

#### Single
> 只有onSuccess()、onError()，没有onNext()，只能发射一个事件或错误，之后再发射也不会处理。

#### Completable
> 只有onComplete()和onError()，不发射数据，没有`map`、`flatMap`操作符，常结合`andThen`操作符使用

#### Maybe
> 只有onSuccess()、onError()，没有onNext()，只能发射一个事件或错误，之后再发射也不会处理。

* 5种被观察者可以通过：toObservable()、toFlowable()、toSingle()、toCompletable()、toMaybe()互转
* Single、Completable、Maybe可以看做是Observable的简化版，Flowable是Observable背压加强版

### 线程切换原理
```java
class ObservableObserveOn {
	void subscribeActual(Observer observer) {
		Worker w = scheduler.createWorker();
    // ObserveOnObserver实现Observer, Runnable接口
		source.subscribe(new ObserveOnObserver<T>(observer, w));
	}
}
```
```java
class ObservableSubscribeOn {
	void subscribeActual(Observer observer) {
    // SubscribeOnObserver实现Observer接口
		SubscribeOnObserver parent = new SubscribeOnObserver(observer);
		scheduler.scheduleDirect(new SubscribeTask(parent));
	}
}
class SubscribeTask implements Runnable {
	private final SubscribeOnObserver parent;

	SubscribeTask(SubscribeOnObserver parent) {
		this.parent = parent;
	}

  @Override
  public void run() {
    source.subscribe(parent);
  }
}
```

```java
class Scheduler {
  Disposable scheduleDirect(@NonNull Runnable run) {
        final Worker w = createWorker();

        final Runnable decoratedRun = RxJavaPlugins.onSchedule(run);

        DisposeTask task = new DisposeTask(decoratedRun, w);
				// 这句重点
        w.schedule(task);

        return task;
    }
}
class EventLoopWorker extends Scheduler.Worker {
	Disposable schedule(Runnable action) {
    // NewThreadWorker
		return threadWorker.scheduleActual(action);
	}
}
class NewThreadWorker extends Scheduler.Worker {
	Disposable scheduleActual(Runnable action) {
    // ScheduledExecutorService executor = SchedulerPoolFactory.create(threadFactory);
    executor.submit(run);
	}
}
class HandlerScheduler {
  Disposable scheduleDirect(Runnable run) {
        ScheduledRunnable scheduled = new ScheduledRunnable(handler, run);
        handler.postDelayed(scheduled, unit.toMillis(delay));
        return scheduled;
    }
}
class HandlerWorker extends Worker {
        private final Handler handler;

        HandlerWorker(Handler handler) {
            this.handler = handler;
        }

        @Override
        public Disposable schedule(Runnable run) {
            ScheduledRunnable scheduled = new ScheduledRunnable(handler, run);

            Message message = Message.obtain(handler, scheduled);
          	// Used as token for batch disposal of this worker's runnables.
            message.obj = this;
            handler.sendMessageDelayed(message, unit.toMillis(delay));
            return scheduled;
        }
}
```

###RxLifecycle
```java
public class RxLifecycleActivity extends Activity {
    private Subject<ActivityEvent> subject;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_lifecycle);

        subject = BehaviorSubject.create();
        subject.onNext(ActivityEvent.CREATE);

        final Disposable disposable = Observable.interval(0, 1, TimeUnit.SECONDS)
                .compose(new ObservableTransformer<Long, Long>() {
                    @Override
                    public ObservableSource<Long> apply(Observable<Long> upstream) {
                        return upstream.takeUntil(
                                subject.filter(new Predicate<ActivityEvent>() {
                                    @Override
                                    public boolean test(ActivityEvent activityEvent) {
                                        return activityEvent == ActivityEvent.PAUSE;
                                    }
                                })
                        );
                    }
                })
                .subscribe(new Consumer<Long>() {
                    @Override
                    public void accept(Long aLong) {
                        Log.d("gxd", "onNext-->" + aLong);
                    }
                });
    }

    @Override
    protected void onPause() {
        super.onPause();
        subject.onNext(ActivityEvent.PAUSE);
    }

    private enum ActivityEvent {
        CREATE,
        PAUSE
    }
}
```

### 细节
* Observer的onSubscribe()执行的线程跟Observable执行的线程相同，不收subscribeOn()的影响
* dispose() 能够取消事件发射，并阻止subscribe()，也就是订阅，onSubscribe()中调用则可以都阻止
* 背压subscribeOn()、observableOn()要在两个不同的线程
* observeOn()只能指定下游的线程，subscribeOn()只有第一次指定的线程有效，且是从被观察者开始往下游，直到有observeOn()