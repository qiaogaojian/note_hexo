# 自定义Android软键盘enter键

## 问题描述

你在EditText上输入以后，想在下一行输入框输入，可能需要去点击下一行输入框，让它获取焦点，也可能要隐藏软键盘，在点击输入框，弹出软键盘。或者已经到了最后一行输入框，输入完毕以后，要点击登录，注册，或者链接按钮，可能要去隐藏它，感觉操作狠繁琐。用户体验不好，有没有解决办法呢？

## 解决办法

设置EditText的Ime Options属性。
软键盘，最常用的enter键事件有：   把EditText的Ime Options属性设置成不同的值，Enter键上可以显示不同的文字或图案
actionNone : 回车键，按下后光标到下一行
actionSend ： Send
actionNext ： Next
actionDone ： Done，隐藏软键盘，即使不是最后一个文本输入框
actionSearch ： search 搜索

**注意一定要设置android:singleLine="true"，否则回车会换行**

下面贴出代码：
xml文件

```java
<EditText
    android:id="@+id/tv_search"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:imeOptions="actionSearch"
    android:singleLine="true" >
</EditText>
```

Android代码：

```java
EditText.setOnEditorActionListener(new OnEditorActionListener() {
    @Override
    public boolean onEditorAction(TextView v, int actionId,
                KeyEvent event) {
        if (actionId == EditorInfo.IME_ACTION_SEARCH) {
            searchYanshan();
        }
        return false;
    }
});
```