[TOC]

### standard

* 标准模式。这是系统默认的模式，每次启动Activity都会重新创建一个新的Activity实例，也就是onCreate，onStart，onResume流程走一遍，并且一个任务栈里允许存在多个实例。

### singleTop

* 栈顶复用模式。在同一个任务栈中栈顶如果有此Activity的实例，那么不会重新创建一个新实例，而是调用此Activity的onNewIntent，此时onCreate、onStart不会被调用。但如果此Activity在同一个任务栈但不在栈顶或之前用standard模式启动的，就会重新创建。

### singleTask

* 栈内复用模式。在同一个任务栈（注意：不一定是栈顶了）中如果有此Activity的实例，那么不会重新创建一个新实例，而是调用此Activity的onNewIntent，此时onCreate、onStart不会被调用。并且自带FLAG_ACTIVITY_CLEAR_TOP效果。
* 同一个栈内。如ADBC（要启动D，则把D挪到栈顶，BC由于clearTop而被移除栈，剩下AD）。
* 如果D指定栈为S2，android:taskAffinity="S2"，而启动它的Activity栈为S1，则先创建S2栈然后再new D放到S2中。

### singleInstance
单例模式。这是一种加强的singleTask模式。除了singleTask模式所有特性外，它只能单独在一个任务栈中，跟其他Activity不能同时存在一个任务栈，整个Application也只有一个实例。

### Flags
#### FLAG_ACTIVITY_SINGLE_TOP(singleTop)

#### FLAG_ACTIVITY_CLEAR_TOP(singleTask)

#### FLAG_ACTIVITY_NO_HISTORY
> 使用该模式来启动Activity，当该Activity启动其他Activity后，其他Activity销毁时，该Activity也被同时销毁，不会保留在任务栈中。如A以这种模式启动B，B再启动C，C退出时B也会销毁，则任务栈只有A。

#### FLAG_ACTIVITY_EXCLUDE_FROM_RECENTS
> 使用该标识位启动的Activity不添加到最近应用列表，也即我们从最近应用里面查看不到我们启动的这个activity。与属性android:excludeFromRecents=”true”效果相同。

#### FLAG_ACTIVITY_NEW_TASK
> 该标志位表示使用一个新的Task来启动一个Activity，相当于在清单文件中给Activity指定“singleTask”启动模式。通常我们在Service启动Activity时，由于Service中并没有Activity任务栈，所以必须使用该Flag来创建一个新的Task。

* 当我们使用ApplicationContext去启动Activity的时候，因为默认是standard模式会报错：

> Calling startActivity() from outside of an Activity context requires the FLAG_ACTIVITY_NEW_TASK flag. Is this really what you want?

因为standard 模式默认启动的Activity会和启动它的Activity在同一任务栈，而由于ApplicationContext非Activity的Context，并没有所谓的任务栈，所以提示我们需要使用FLAG_ACTIVITY_NEW_TASK来新建一个任务栈并把启动的Activity放进去。

* 设置此状态，首先会查找是否存在和被启动的Activity具有相同的亲和性的任务栈（即taskAffinity，注意同一个应用程序中的activity的亲和性一样），如果有，则直接把这个栈整体移动到前台，并保持栈中的状态不变，即栈中的activity顺序不变，如果没有，则新建一个栈来存放被启动的activity。

#### FLAG_ACTIVITY_BROUGHT_TO_FRONT
这个标志一般不是由程序代码设置的，如在launchMode中设置singleTask模式时系统帮你设定。

#### FLAG_ACTIVITY_CLEAR_TASK
此Activity将变成一个新Task中新的最底端的Activity，所有的之前此Activity实例和包含该实例的Task都会被关闭，这个标识仅仅和FLAG_ACTIVITY_NEW_TASK联合起来才能使用。

#### FLAG_ACTIVITY_FORWARD_RESULT

如果A需要onActivityResult中获取返回结果，startActivityForResult B，而B只是过渡页，启动C之后就finish掉了，需要在 C 中setResult返回给A就可以用到这个标志。

> A -> B -> XXXXX(无论多少个过渡页) 设置 FLAG_ACTIVITY_FORWARD_RESULT 来启动 C ，之后该XXX过渡页finish - > C ，那么C的结果返回给A

#### FLAG_ACTIVITY_NO_ANIMATION

启动的时候不执行动画。

#### FLAG_ACTIVITY_LAUNCHED_FROM_HISTORY

当用户点击Home，从历史中选择该Activity，系统会自动加上这个Flag。

#### FLAG_ACTIVITY_NO_USER_ACTION

在onPause()之前会调用onUserLeaving( )方法，如果使用了该标识，说明目标Activity不和用户交互，所以也就不需要回调onUserLeaving( )方法。

#### FLAG_ACTIVITY_REORDER_TO_FRONT

如果设置这个标记，新启动的Activity将会被放到它所属task的最前面
例如，假如有一个Task包含4个Activity:A,B,C,D.如果D通过调用startActivity( )来启动B，如果使用了这个标记，B将会排在这个task的最上面，也即现在的顺序变成了A,C,D,B。
如果使用了FLAG_ACTIVITY_CLEAR_TOP，这个标记将会被忽略。

#### FLAG_ACTIVITY_RESET_TASK_IF_NEEDED

如果设置该属性，并且这个Activity在一个新的Task中正在被启动或者被带到一个已经存在的Task的顶部，这时这个Task会被重置（即该Task中之前的Activity会被关闭），该Activity成为栈底第一个Activity。

#### FLAG_ACTIVITY_TASK_ON_HOME

把当前新启动的任务置于Home任务之上，也就是按back键从这个任务返回的时候会回到Home，即使这个不是他们最后看见的Activity。
注意这个标记必须和FLAG_ACTIVITY_NEW_TASK加上android:taskAffinity一起使用。

#### FLAG_DEBUG_LOG_RESOLUTION

用来调试，当设置这个标志的时候，在解析这个intent的时候，将会打出打印信息（queryIntent函数）。

#### FLAG_INCLUDE_STOPPED_PACKAGES 和 FLAG_DEBUG_LOG_RESOLUTION

从Android 3.1开始，给Intent定义了两个新的Flag，分别为FLAG_INCLUDE_STOPPED_PACKAGES和FLAG_EXCLUDE_STOPPED_PACKAGES，用来控制Intent是否要对处于停止状态的App起作用，顾名思义：
FLAG_INCLUDE_STOPPED_PACKAGES：表示包含未启动的App
FLAG_EXCLUDE_STOPPED_PACKAGES：表示不包含未启动的App
值得注意的是，Android 3.1开始，系统向所有Intent的广播添加了FLAG_EXCLUDE_STOPPED_PACKAGES标志。这样做是为了防止广播无意或不必要地开启未启动App的后台服务。如果要强制调起未启动的App，后台服务或应用程序可以通过向广播Intent添加FLAG_INCLUDE_STOPPED_PACKAGES标志来唤醒。

#### FLAG_FROM_BACKGROUND

Intent不光可以在Acitivity里面start，还可以从service里面启动，这个参数就表示这个Intent是从后台服务发起的。

#### FLAG_GRANT_PERSISTABLE_URI_PERMISSION

区别于 FLAG_GRANT_READ_URI_PERMISSION 跟 FLAG_GRANT_WRITE_URI_PERMISSION， URI权限会持久存在即使重启，直到明确的用 revokeUriPermission(Uri, int) 撤销。 这个flag只提供可能持久授权。但是接收的应用必须调用ContentResolver的takePersistableUriPermission(Uri, int)方法实现 。

#### FLAG_GRANT_PERSISTABLE_URI_PERMISSION

Uri 权限授予任何原始授权URI前缀匹配的URI。

#### FLAG_GRANT_PERSISTABLE_URI_PERMISSION

结合FLAG_GRANT_READ_URI_PERMISSION 和 FLAG_GRANT_WRITE_URI_PERMISSION 使用。
Uri 权限授予任何原始授权URI前缀匹配的URI。如果没有这个标志则必须精确匹配Uri了。

#### FLAG_GRANT_READ_URI_PERMISSION 和 FLAG_GRANT_WRITE_URI_PERMISSION

临时访问读权限和写权限 。Intent的接受者将被授予 INTENT 数据 uri 或者 在ClipData 上的读/写权限。

#### FLAG_RECEIVER_FOREGROUND

当发送广播时，允许其接受者 在前台运行的拥有更高的优先级，更短的超时间隔。

#### FLAG_RECEIVER_NO_ABORT

如果是有序广播，不要允许接收者中断广播播。

#### FLAG_RECEIVER_REGISTERED_ONLY

设置之后就不能通过xml来注册监听这个广播了，必须动态注册。

> 很多毒病程序为了证保自己被止终后可以再次行运，都会在xml中册注一些系统广播，妄图利用这些系统高频广播来实现自动启。
> 比如在老版本的android系统中，毒病程序可以通过监听TIME_TICK来动启自己的service后台行运，做一些秘隐的作工，而且就算自己被kill失落了，也能很快重新动启。
> 而一旦这些系统广播加了flag FLAG_RECEIVER_REGISTERED_ONLY，这些毒病程序就没辙了。

> 例如系统的TIME_TICK广播，由AlarmManagerService发送，我们看源码可以看到
> mTimeTickSender = PendingIntent.getBroadcast(context, 0,
> new Intent(Intent.ACTION_TIME_TICK).addFlags(
> Intent.FLAG_RECEIVER_REGISTERED_ONLY), 0);
> 这样就不能监听ACTION_TIME_TICK来自启动了。

#### FLAG_RECEIVER_REPLACE_PENDING

这个Flag 将会将之前的Intent 替代掉。加了这个Flag，在发送一系列的这样的Intent 之后，中间有些Intent 有可能在你还没有来得及处理的时候，就被替代掉了。

#### FLAG_ACTIVITY_NEW_DOCUMENT(原FLAG_ACTIVITY_CLEAR_WHEN_TASK_RESET)

可以跟FLAG_ACTIVITY_MULTIPLE_TASK结合使用，当只用自己的时候相当于Manifast中 android.R.attr.documentLaunchMode="intoExisting"，当跟FLAG_ACTIVITY_MULTIPLE_TASK 结合使用相当于 Manifast中android.R.attr.documentLaunchMode="always"。

#### FLAG_ACTIVITY_RETAIN_IN_RECENTS

默认情况下通过FLAG_ACTIVITY_NEW_DOCUMENT启动的Activity在关闭之后，Task中的记录会相对应的删除。如果为了能够重新启动这个Activity你想保留它，就可以使用者个flag，最近的记录将会保留在接口中以便用户去重新启动。接受该Flag的Activity可以使用autoRemoveFromRecents去复写这个request或者调用Activity.finishAndRemoveTask( )方法。

#### FLAG_ACTIVITY_MULTIPLE_TASK

这个标识用来创建一个新的task栈，并且在里面启动新的activity（所有情况，不管系统中存在不存在该activity实例），经常和FLAG_ACTIVITY_NEW_DOCUMENT或者FLAG_ACTIVITY_NEW_TASK一起使用。这上面两种使用场景下，如果没有带上FLAG_ACTIVITY_MULTIPLE_TASK标识，他们都会使系统搜索存在的task栈，去寻找匹配intent的一个activity，如果没有找到就会去新建一个task栈；但是当和FLAG_ACTIVITY_MULTIPLE_TASK一起使用的时候，这两种场景都会跳过搜索这步操作无条件的创建一个新的task。和FLAG_ACTIVITY_NEW_TASK一起使用需要注意，尽量不要使用该组合除非你完成了自己的顶部应用启动器，他们的组合使用会禁用已经存在的task栈回到前台的功能。

### taskAffinity 和 allowTaskReparenting

taskAffinity用于指定当前Activity所关联的Task，allowTaskReparenting用于配置是否允许该Activity可以更换从属Task，通常情况二者连在一起使用，用于实现把一个应用程序的Activity移到另一个应用程序的Task中。
allowTaskReparenting用来标记Activity能否从启动的Task移动到taskAffinity指定的Task，默认是继承至application中的allowTaskReparenting=false，如果为true，则表示可以更换；false表示不可以。

> 例如在A应用中启动了B应用的Activity，如果设置allowTaskReparenting=true，则Activity允许从A的Task移动到B的Task。但如果A被启动之后，Activity就会回到A的Task中。