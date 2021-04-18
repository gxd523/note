[TOC]

## 基础
> WebView是一个基于webkit引擎、展现web页面的控件。Webview在低版本和高版本采用了不同的webkit版本内核，4.4后直接使用了Chrome。

### WebView状态
#### resume状态
* 激活WebView为活跃状态，能正常执行网页的响应
* webView.onResume()
* webView.resumeTimers()：恢复pauseTimers状态

#### pause状态
* 当页面被失去焦点被切换到后台不可见状态，需要执行onPause
* 通过onPause动作通知内核暂停所有的动作，比如DOM的解析、plugin的执行、JavaScript执行
* webView.onPause()
* webView.pauseTimers()
	* 当应用程序(存在webview)被切换到后台时，这个方法不仅仅针对当前的webview而是全局的全应用程序的webview
	* 它会暂停所有webview的layout，parsing，javascripttimer。降低CPU功耗。

### 销毁Webview
* 在关闭了Activity时，如果Webview的音乐或视频，还在播放。就必须销毁Webview
* 但是注意：webview调用destory时,webview仍绑定在Activity上
* 这是由于自定义webview构建时传入了该Activity的context对象
* 因此需要先从父容器中移除webview,然后再销毁webview:
```java
rootLayout.removeView(webView); 
webView.destroy();
```

### 前进/后退
方法 | 含义
--- | ---
Webview.canGoBack() | 是否可以后退
Webview.goBack() | 后退网页
Webview.canGoForward() | 是否可以前进
Webview.goForward() | 前进网页
Webview.goBackOrForward(intsteps) | 以当前的index为起始点前进或者后退到历史记录中指定的steps；如果steps为负数则为后退，正数则为前进

#### Back键控制网页后退
```java
问题：在不做任何处理前提下 ，浏览网页时点击系统的“Back”键,整个 Browser 会调用 finish()而结束自身
目标：点击返回后，是网页回退而不是推出浏览器

解决方案：在当前Activity中处理并消费掉该 Back 事件
public boolean onKeyDown(int keyCode, KeyEvent event) {
    if ((keyCode == KEYCODE_BACK) && mWebView.canGoBack()) { 
    mWebView.goBack();
    return true;
    }
    return super.onKeyDown(keyCode, event);
}
```

### 加载Url
* 加载一个网页：`webView.loadUrl("http://www.google.com/")`
* 加载apk包中的html页面：`webView.loadUrl("file:///android_asset/test.html")`
* 加载手机本地的html页面
：`webView.loadUrl("content://com.android.htmlfileprovider/sdcard/test.html")`

### WebView中长按保存图片
```kotlin
mWebView.setOnLongClickListener { v ->
    val result = (v as WebView).hitTestResult
    when (result.type) {
        HitTestResult.PHONE_TYPE -> true
        HitTestResult.EMAIL_TYPE -> true
        HitTestResult.GEO_TYPE -> true
        HitTestResult.SRC_ANCHOR_TYPE -> true
        HitTestResult.SRC_IMAGE_ANCHOR_TYPE -> true
        HitTestResult.IMAGE_TYPE -> {
            // 获取图片的路径
            val saveImgUrl = result.extra

            // 跳转到图片详情页，显示图片
            val i = Intent(this@MainActivity, MainActivity::class.java)
            i.putExtra("imgUrl", saveImgUrl)
            startActivity(i)
            true
        }
        HitTestResult.UNKNOWN_TYPE -> false
        else -> false
    }
}
```

### 替换404错误页面
```kotlin
override fun onReceivedError(view: WebView, request: WebResourceRequest?, error: WebResourceError?) {
    super.onReceivedError(view, request, error)
    view.loadDataWithBaseURL(null, "", "text/html", "utf-8", null)
    mErrorFrame.setVisibility(View.VISIBLE)
}
```

### WebView开启硬件加速
* `webView.setLayerType(View.LAYER_TYPE_HARDWARE, null)`

### 获取点击图片类型
```java
//首先判断点击的类型
WebView.HitTestResult result = ((WebView) v).getHitTestResult();
int type = result.getType();

//获取具体信息，图片这里就是图片地址
String imgurl = result.getExtra();
```

## WebSetttings
* javaScriptEnabled：允许与Js交互
* javaScriptCanOpenWindowsAutomatically：允许Js弹窗
* useWideViewPort：将图片调整到适合webview的大小
* loadWithOverviewMode：缩放至屏幕的大小
* setSupportZoom：支持缩放，默认为true。是下面那个的前提。
* builtInZoomControls：设置内置的缩放控件。若为false，则该WebView不可缩放
* displayZoomControls：隐藏原生的缩放控件
* webSettings.setCacheMode(WebSettings.LOAD_CACHE_ELSE_NETWORK); //关闭webview中缓存 
* webSettings.setAllowFileAccess(true)：设置可以访问文件 
* webSettings.setLoadsImagesAutomatically(true)：支持自动加载图片
* webSettings.setDefaultTextEncodingName("utf-8")：设置编码格式

### 缓存
* 当加载 html 页面时，WebView会在/data/data/包名目录下生成 database 与 cache 两个文件夹
* 请求的 URL记录保存在 WebViewCache.db
* 而 URL的内容是保存在 WebViewCache 文件夹下
* 设置缓存：webSettings.setCacheMode()
值 | 含义
--- | ---
LOAD_CACHE_ONLY | 不使用网络，只读取本地缓存数据
LOAD_DEFAULT | (默认)根据cache-control决定是否从网络上取数据。 
LOAD_NO_CACHE | 不使用缓存，只从网络获取数据.
LOAD_CACHE_ELSE_NETWORK | 只要本地有，无论是否过期，或者no-cache，都使用缓存中的数据

#### 离线加载
```java
if (NetStatusUtil.isConnected(getApplicationContext())) {//判断网络是否连接
    webSettings.setCacheMode(WebSettings.LOAD_DEFAULT);//根据cache-control决定是否从网络上取数据。
} else {
    webSettings.setCacheMode(WebSettings.LOAD_CACHE_ELSE_NETWORK);//没网，则从本地获取，即离线加载
}

webSettings.setDomStorageEnabled(true); // 开启 DOM storage API 功能
webSettings.setDatabaseEnabled(true);   //开启 database storage API 功能
webSettings.setAppCacheEnabled(true);//开启 Application Caches 功能

String cacheDirPath = getFilesDir().getAbsolutePath() + APP_CACAHE_DIRNAME;
webSettings.setAppCachePath(cacheDirPath); //设置  Application Caches 缓存目录
```

#### 清除缓存数据
//清除网页访问留下的缓存
//由于内核缓存是全局的因此这个方法不仅仅针对webview而是针对整个应用程序.
Webview.clearCache(true);

//清除当前webview访问的历史记录
//只会webview访问历史记录里的所有记录除了当前访问记录
Webview.clearHistory()；

//这个api仅仅清除自动完成填充的表单数据，并不会清除WebView存储到本地的数据
Webview.clearFormData()；

## WebViewClient
* shouldOverrideUrlLoading()：使得打开网页时不调用系统浏览器， 而是在本WebView中显示
	* `view.loadUrl(url); return true;`
* onPageStarted()：设定加载开始的操作
* onPageFinished()：设定加载结束的操作
* onLoadResource()：设定加载资源的操作
* onReceivedError()：该方法传回了错误码，根据错误类型可以进行不同的错误分类处理
* onReceivedSslError()：

## WebChromeClient
> 辅助 WebView 处理 Javascript 的对话框,网站图标,网站标题等等

* onProgressChanged()：
* onReceivedTitle()：

## 与Js交互
### Android调用Js的2种方式
方式 | 优点 | 缺点
--- | --- | ---
webView.loadUrl() | 写法简洁、兼容性好 | 效率低(要刷新页面)、没法获取返回值
webView.evaluateJavascript() | 效率高、可获取返回值 | 仅兼容4.4+

### Js调用Android的3种方式
方式 | 描述 | 缺点 | 优点
--- | --- | --- | --- 
webView.addJavascriptInterface() | 进行对象映射 | 4.2及以下存在漏洞 |方便简洁
shouldOverrideUrlLoading() | 回调拦截约定好的协议的url | 获取返回值需要loadUrl() | 没有安全漏洞
webChrimeClient.onJsPrompt() | 拦截Js的prompt() | 需要与Js协定约束 |没有安全漏洞

## 混淆
```
-keepattributes *Annotation*  
-keepattributes *JavascriptInterface*
-keep public class org.mq.study.webview.DemoJavaScriptInterface{
   public <methods>;
}
#假如是内部类，混淆如下：
-keepattributes *JavascriptInterface*
-keep public class org.mq.study.webview.webview.DemoJavaScriptInterface$InnerClass{
    public <methods>;
}
```

## 常见问题
* js弹窗不弹：要设置`WebChromeClient`
* js方法调用失败：要在`webViewClient.onPageFinished()`后调用

### 在WebView中播放音乐，退出Activity时音乐还在播放
* 方案一：销毁WebView，在`Activity.onDestroy()`中调用：
```kotlin
mWebView.loadDataWithBaseURL(null, "", "text/html", "utf-8", null)
mWebView.clearHistory()
(mWebView.parent as ViewGroup).removeView(mWebView)
mWebView.destroy()
super.onDestroy()
```
* 方案二：在Activity中调用
```kotlin
override fun onResume() {
    mWebView.onResume()
    mWebView.resumeTimers()
    super.onResume()
}

override fun onPause() {
    mWebView.onPause()
    mWebView.pauseTimers()
    super.onPause()
}
```

### 5.0以后的WebView加载的链接为Https开头，但是链接里面的内容，比如图片为Http链接，这时候，图片就会加载不出来
* WebView.Settings设置允许加载任何来源的内容
```kotlin
if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.LOLLIPOP) {
    // MIXED_CONTENT_ALWAYS_ALLOW 允许从任何来源加载内容，即使起源是不安全的
    // MIXED_CONTENT_NEVER_ALLOW 不允许Https加载Http的内容，即不允许从安全的起源去加载一个不安全的资源
    // MIXED_CONTENT_COMPLTIBILITY_MODE 当涉及到混合式内容时，WebView会尝试去兼容最新Web浏览器的风格
    mWebView.settings.mixedContentMode = WebSettings.MIXED_CONTENT_ALWAYS_ALLOW
    // 也可以mWebView.settings.mixedContentMode = mWebView.settings.mixedContentMode
}
```
* 如果认证证书不被Android设备接受，可通过重写`WebViewClient`的`onReceivedSslError()`来接受所有证书：
```kotlin
override fun onReceivedSslError(view: WebView?, handler: SslErrorHandler, error: SslError?) {
    // 注意一定要去除这行代码，否则设置无效：super.onReceivedSslError(view, handler, error)
    // Android默认的处理方式：handler.cancel()
    handler.proceed()// 接受所有网站的证书
}
```

### WebView调用手机系统相册来上传图片，开发过程中发现在很多机器上无法正常唤起系统相册来选择图片
* 重写WebChromeClient的以下几个方法：
```kotlin
override fun onShowFileChooser(
    webView: WebView?,
    filePathCallback: ValueCallback<Array<Uri>>?,
    fileChooserParams: FileChooserParams?
): Boolean {
    return super.onShowFileChooser(webView, filePathCallback, fileChooserParams)
}
```

### WebView白屏是什么原因？
```kotlin
if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.HONEYCOMB) {
    setLayerType(View.LAYER_TYPE_SOFTWARE, null);
}
```

### 花屏、文字重叠等问题可关闭硬件加速

### ViewPager里非首屏WebView点击事件不响应是什么原因？
* 这个问题的办法是继承WebView类，在子类覆盖onTouchEvent方法
```kotlin
@Override
public boolean onTouchEvent(MotionEvent ev) {
    if (ev.getAction() == MotionEvent.ACTION_DOWN) {
        onScrollChanged(getScrollX(), getScrollY(), getScrollX(), getScrollY());
    }
    return super.onTouchEvent(ev);
}
```