## System.currentTimeMillis()
* 在用户设置、联网后自动设置，通过`SystemClock.setCurrentTimeMillis()`设置
* 由于时间可能因为被设置而变化，所以应该监听以下事件广播，`ACTION_TIME_TICK`、`ACTION_TIME_CHANGED`、`ACTION_TIMEZONE_CHANGED`
* 适合用在闹钟、日历等

## SystemClock.uptimeMillis()
* 从系统启动开始计时，适合测量间隔时间
* 当系统进入深度睡眠(CPU关闭、显示变暗、设备等待外部输入)时停止计时，但不受时钟scaling、idle或其他节能机制的影响

## SystemClock.elapsedRealtime()
* 从系统启动开始计时，包括系统进入深度睡眠

## SystemClock.currentThreadTimeMillis()
* 返回当前线程`Running`状态的运行时长
* `Thread.sleep()`、`wait()`等时长不计入

## SystemClock.sleep()
* 不响应`InterruptedException`

## SystemClock.setCurrentTimeMillis()
* 设置时间，只有系统应用能设置，且需要`android.permission.SET_TIME`权限