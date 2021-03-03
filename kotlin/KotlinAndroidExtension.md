* 直接使用控件：编译时生成扩展属性，用`HashMap`保存`findViewById()`获取的控件
* `@Parcelize`：自动实现`Parcelable`序列化

## deprecated
* res下的任何id都可以被访问，有可能因访问了非当前Layout下的id而出错
* 这主要体现在Configuration中的对应布局不全时，运行时可能出现NPE
* ViewHolder里使用时，实际是通过itemView.findViewById()调用