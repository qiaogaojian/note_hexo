# Android中onNewIntent方法获取新的intent数据

## 问题描述

Activity中重写onNewIntent(Intent intent)方法,结果intent中取不到需要的数据.

## 解决方法

``` java
    @Override
    protected void onNewIntent(Intent intent)
    {
        super.onNewIntent(intent);
        setIntent(intent);
    }

    @Override
    protected void onResume()
    {
        super.onResume();
        Intent intent = getIntent();
        // 这时intent已经是新的intent了

        // 这里写获取到数据后的逻辑

        // 末尾记得重置intent 防止无法返回
    }
```

## 参考链接

[getIntent() Extras always NULL](https://stackoverflow.com/questions/6352281/getintent-extras-always-null)