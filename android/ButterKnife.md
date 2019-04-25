[TOC]

## 导入ButterKnife

1. project的`build.gradle`中添加

```
classpath 'com.jakewharton:butterknife-gradle-plugin:8.5.1'
```

2. module的`build.gradle`中添加

```
apply plugin: 'com.jakewharton.butterknife'
```

3. module的`build.gradle`中的`dependencies`里添加

```
implementation 'com.jakewharton:butterknife:8.5.1'
annotationProcessor 'com.jakewharton:butterknife-compiler:8.5.1'
```

## ButterKnife的使用

* 初始化

```
// 注意不要在BaseActivity中添加,因为必须添加到setContentView的下面
setContentView(R.layout.activity_butter_knife_test);
ButterKnife.bind(this);
```
* 绑定View

```
FitImageView diamondIv;
@BindView(R.id.activity_main_view_pager)
```

* Click、Focus、Key事件

> 随便写一个方法，参数可以放View也可以不放任何参数

```
@OnClick(R.id.activity_main_avatar_rl)
public void onAvatarClick(View view) {
		...
}
```

* 绑定res资源

```
//绑定字符串
@BindString(R.string.title) String title;
//绑定图形
@BindDrawable(R.drawable.graphic) Drawable graphic;
//绑定颜色
@BindColor(R.color.red) int red; // int or ColorStateList field
//绑定长度
@BindDimen(R.dimen.spacer) Float spacer; // int (for pixel size) or float (for exact value) field
// ...
```

* 绑定Fragment
* 绑定adapter

```
public class PlanSeriesItemViewHolder extends BaseViewHolder {
    @BindView(R.id.adapter_series_item_b_title_tv)
    FitTextView titleTv;

    public PlanSeriesItemViewHolder(ViewGroup parent) {
super(LayoutInflater.from(parent.getContext()).inflate(R.layout.adapter_series_item_b, parent, false));
        ButterKnife.bind(this, itemView);
     }
}
```

* 绑定控件ID直接将控件添加至集合或数组中
* 一次性改变集合中所有对象的值-apply
* 绑定listview的item点击事件
* findviewById的简化

## Android ButterKnife Zelezny插件

