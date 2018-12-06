# Android中WebView闪烁问题

## 解决方法

webView默认隐藏然后在onPageFinished回调中显示

```java
public void onPageFinished(WebView view, String url)
{//页面加载完成

    mBinding.webviewTrans.setBackgroundColor(Color.TRANSPARENT);
    mBinding.webviewTrans.setLayerType(WebView.LAYER_TYPE_SOFTWARE, null);
    mBinding.webviewTrans.setVisibility(View.VISIBLE);
}
```

在loadUrl之前 设置webView的背景为透明的

```java
    mBinding.webviewTrans.setBackgroundColor(Color.argb(1, 0, 0, 0));
    mBinding.webviewTrans.loadUrl(webUrl);
```