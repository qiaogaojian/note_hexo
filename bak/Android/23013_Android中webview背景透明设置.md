# Android中 agentweb 背景透明设置

> agentweb在webview的基础上包了一层view 因此需要设置webview以及它的父级view才能实现透明,否则默认是白色背景

``` java
mAgentWeb.getWebCreator().getWebView().setBackgroundColor(0x00000000);
mAgentWeb.getWebCreator().getWebParentLayout().setBackgroundColor(0x00000000);
```