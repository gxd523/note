* ANR：focused window has not finished processing the input events that were previously delivered to it
* 一排3个view获取焦点的优先级与绘制顺序相同，先绘制的优先获取焦点
* 方法命名
  * 如果方法调用时机确定，方法的内容不确定，则写成onXXX()，如回调onClick()
  * 如果方法调用时机不确定，方法的内容确定，则写成XXX()，如功能startVideo()

