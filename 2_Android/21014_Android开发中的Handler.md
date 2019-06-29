# Android开发中的Handler

Handler机制是Android中相当经典的异步消息机制，在Android发展的历史长河中扮演着很重要的角色，无论是我们直接面对的应用层还是FrameWork层，使用的场景还是相当的多。

```java
private Button mBtnTest;
private Handler mTestHandler = new Handler(){
    @Override
    public void handleMessage(Message msg) {
        switch (msg.what){
            case 1:
                mBtnTest.setText("收到消息1");
        }
    }
};
@Override
protected void onCreate(final Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);
    mBtnTest = (Button) findViewById(R.id.btn_test);
    new Thread(new Runnable() {
        @Override
        public void run() {
            try {
                Thread.sleep(3000);
                mTestHandler.sendEmptyMessage(1);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }).start();
}
```

## 参考链接

[10分钟了解Android的Handler机制](http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2018/0522/9728.html)