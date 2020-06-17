![](../../pic/view_flow_chat.png)

* 自己调用requestLayout()后，只会执行measure、layout，不会执行draw
* 没有设置图片或颜色就不会走draw()、onDraw()

### Measure
#### ViewGroup.LayoutParams
#### MeasureSpecs
* UNSPECIFIED：父容器没有对当前View有任何限制，当前View可以任意取尺寸。比如 ListView、 ScrollView，一般自定义 View 中用不到
* EXACTLY：当前的尺寸就是当前View应该取的尺寸，match_parent或具体的值，可通过getSize()获得具体尺寸
* AT_MOST：当前尺寸是当前View能取的最大尺寸，wrap_parent，无法通过getSize()获得具体尺寸

#### setMeasuredDimension()
* 在onMeasure()中调用
### Layout
### Draw