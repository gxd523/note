[TOC]

![](pic/setContentView.png)

### LayoutInflater的创建
> LayoutInflater是一个抽象类，它的创建，并不是交由App层处理的，而是调用了from()的静态函数，经由系统服务LAYOUT_INFLATER_SERVICE，最终创建了一个LayoutInflater的子类对象--PhoneLayoutInflater

* `baseContext`就是`ContextWrapper`里的`mBase`属性，`ContextImpl`实例对象，创建过程如下：

### inflate()的使用
root | attachToRoot | return | 说明
:---: | :---: | :---: | ---
root | true | root | 布局已被绑到root上，无需额外addView()，否则会报错 
root | false | view | 布局未绑定到root上，需额外addView()，此时root参数是为了解析布局根元素的属性，使其在addView()时生效 
null | false |view | 不正确写法，导致被填充布局的根元素的参数无法解析
null | true |view | 此时true无效，等同于false

### LayoutInflater耗时点
> LayoutInflater将xml文件实例化为一个view对象的流程，其中有两个部分是耗时的主要来源

* XmlResourseParser对xml的遍历
* 反射创建View对象导致的耗时(反射比直接创建对象要慢3倍，iReader的x2c框架就是基于这一点去做的优化)

### inflate流程

#### inflate(resource, root, attachToRoot)
```java
View inflate(int resource, ViewGroup root, boolean attachToRoot) {
    final Resources res = getContext().getResources();
    final XmlResourceParser parser = res.getLayout(resource);
    try {
        return inflate(parser, root, attachToRoot);
    } finally {
        parser.close();
    }
}
```
#### inflate(parser, root, attachToRoot)
```java
View inflate(XmlPullParser parser, ViewGroup root, boolean attachToRoot) {
    final AttributeSet attrs = Xml.asAttributeSet(parser);
    View result = root;

    final String name = parser.getName();
    if ("merge".equals(name)) {
        rInflate(parser, root, attrs, false, false);
    } else {
        final View temp = createViewFromTag(root, name, attrs, false);

        ViewGroup.LayoutParams params = null;

        if (root != null) {
            params = root.generateLayoutParams(attrs);
            if (!attachToRoot) {
                temp.setLayoutParams(params);
            }
        }

        rInflate(parser, temp, attrs, true, true);

        if (root != null && attachToRoot) {
            root.addView(temp, params);
        }

        if (root == null || !attachToRoot) {
            result = temp;
        }
    }
    return result;
}
```
#### rInflate(parser, parent, context, attrs, finishInflate)
> 递归解析xml，生成View添加到父容器

```java
void rInflate(XmlPullParser parser, View parent, final AttributeSet attrs, boolean finishInflate, boolean inheritContext) {
    final int depth = parser.getDepth();
    int type;

    while (((type = parser.next()) != XmlPullParser.END_TAG || parser.getDepth() > depth) && type != XmlPullParser.END_DOCUMENT) {
        final String name = parser.getName();

        if ("requestFocus".equals(name)) {
            parseRequestFocus(parser, parent);
        } else if ("tag".equals(name)) {
            parseViewTag(parser, parent, attrs);
        } else if ("include".equals(name)) {
            parseInclude(parser, parent, attrs, inheritContext);
        } else if ("merge".equals(name)) {
            throw new InflateException("<merge /> must be the root element");
        } else {
            final View view = createViewFromTag(parent, name, attrs, inheritContext);
            final ViewGroup viewGroup = (ViewGroup) parent;
            final ViewGroup.LayoutParams params = viewGroup.generateLayoutParams(attrs);
            rInflate(parser, view, attrs, true, true);
            viewGroup.addView(view, params);
        }
    }
}
```
#### createViewFromTag(parent, name, context, attrs)
> 单个View布局的解析

```java
View createViewFromTag(View parent, String name, AttributeSet attrs, boolean inheritContext) {
        if (name.equals("view")) {
            name = attrs.getAttributeValue(null, "class");
        }

        Context viewContext;
        if (parent != null && inheritContext) {
            viewContext = parent.getContext();
        } else {
            viewContext = mContext;
        }

        // Apply a theme wrapper, if requested.
        final TypedArray ta = viewContext.obtainStyledAttributes(attrs, ATTRS_THEME);
        final int themeResId = ta.getResourceId(0, 0);
        if (themeResId != 0) {
            viewContext = new ContextThemeWrapper(viewContext, themeResId);
        }
        ta.recycle();

        if (name.equals("blink")) {
            return new BlinkLayout(viewContext, attrs);
        }

        View view;
        if (mFactory2 != null) {
            view = mFactory2.onCreateView(parent, name, viewContext, attrs);
        } else if (mFactory != null) {
            view = mFactory.onCreateView(name, viewContext, attrs);
        } else {
            view = null;
        }

        if (view == null && mPrivateFactory != null) {
            view = mPrivateFactory.onCreateView(parent, name, viewContext, attrs);
        }

        if (view == null) {
            final Object lastContext = mConstructorArgs[0];
            mConstructorArgs[0] = viewContext;
            try {
                if (-1 == name.indexOf('.')) {
                    view = onCreateView(parent, name, attrs);
                } else {
                    view = createView(name, null, attrs);
                }
            } finally {
                mConstructorArgs[0] = lastContext;
            }
        }

        return view;
    }
```
#### createView(name, prefix, attrs)
```java
View createView(String name, String prefix, AttributeSet attrs) {
    Constructor<? extends View> constructor = sConstructorMap.get(name);
    Class<? extends View> clazz = null;

    if (constructor == null) {
        // Class not found in the cache, see if it's real, and try to add it
        clazz = mContext.getClassLoader().loadClass(prefix != null ? (prefix + name) : name).asSubclass(View.class);

        if (mFilter != null && clazz != null) {
            boolean allowed = mFilter.onLoadClass(clazz);
            if (!allowed) {
                failNotAllowed(name, prefix, attrs);
            }
        }
        constructor = clazz.getConstructor(mConstructorSignature);
        sConstructorMap.put(name, constructor);
    } else {
        // If we have a filter, apply it to cached constructor
        if (mFilter != null) {
            // Have we seen this name before?
            Boolean allowedState = mFilterMap.get(name);
            if (allowedState == null) {
                // New class -- remember whether it is allowed
                clazz = mContext.getClassLoader().loadClass(prefix != null ? (prefix + name) : name).asSubclass(View.class);

                boolean allowed = clazz != null && mFilter.onLoadClass(clazz);
                mFilterMap.put(name, allowed);
                if (!allowed) {
                    failNotAllowed(name, prefix, attrs);
                }
            } else if (allowedState.equals(Boolean.FALSE)) {
                failNotAllowed(name, prefix, attrs);
            }
        }
    }

    Object[] args = mConstructorArgs;
    args[1] = attrs;

    constructor.setAccessible(true);
    final View view = constructor.newInstance(args);
    if (view instanceof ViewStub) {
        // Use the same context when inflating ViewStub later.
        final ViewStub viewStub = (ViewStub) view;
        viewStub.setLayoutInflater(cloneInContext((Context) args[0]));
    }
    return view;
}
```


1. LayoutInflater.inflate()
2. Resources.getLayout()(`I/O操作`)
3. inflate()中调用LayoutInflater.createViewFromTag()
4. LayoutInflater.Factory.onCreateView()
5. 如果没有Factory，则LayoutInflater.createView()(`反射创建View`)

### BlinkLayout
> 一个隔0.5秒闪烁一次的布局
```xml
<blink
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:layout_centerHorizontal="true"
    app:layout_constraintBottom_toBottomOf="parent"
    app:layout_constraintLeft_toLeftOf="parent"
    app:layout_constraintRight_toRightOf="parent"
    app:layout_constraintTop_toTopOf="parent">

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Android研习社"
        android:textColor="#157686"
        android:textSize="55sp" />
</blink>
```

