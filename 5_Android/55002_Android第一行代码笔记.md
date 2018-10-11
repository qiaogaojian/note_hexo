# 第一行代码笔记

## 1.简介

### 系统架构

1. Linux内核层
2. 系统运行库层
3. 应用框架层
4. 应用层

### 日志工具

```java
Log.v()  // 简写logv 按tab键
Log.d()
Log.i()
Log.w()
Log.e()
```

## 2.活动

### 加载布局

```java
public class FirstActivity extends AppCompatActivity
{
    @Override
    protected void onCreate(Bundle savedInstanceState)
    {
        Super.onCreate(savadInstanceState);
        setContentView(R.layout.first_layout); // 传入布局id 加载相应布局
    }
}
```

### 注册活动

 ```xml
 <application
 ...>
     <activity
         android:name = ".FirstActivity"
         android:label = "This is FirstActivity">
         <intent-filter>
             <action android:name = "android.intent.action.MAIN"/>
             <category android:name = "android.intent.category.LAUNCHER"/>
         </intent-filter>
     </activity>
 </application>
 ```

### 销毁活动

 ```java
 btnFinish.setOnClickListener(new View.OnClickListener(){
     @Override
     public void onClick(View v)
     {
         finish(); // 销毁当前view 从活动栈中移除
     }
 })
 ```

### 活动跳转

Intent是Android程序中各组件之间进行交互的一种重要方式,它不仅可以指明当前组件想要执行的动作,还可以在不同组件之间传递数据.
Intent一般用于启动活动,启动服务,发送广播等场景.

1. 显式Intent

     ```java
     btnJumptoSecond.setOnClickListener(new View.OnClickListener()
     {
         @Override
         public void onClick(View v)
         {
             Intent intent = new Intent(FirstActivity.this,SecondActivity.class);
             startActivity(intent);
         }
     });
     ```

2. 隐式Intent

    - 配置目标 Activity 的 intent-filter

    ```java
    <activity android:name = ".SecondActivity">
        <intent-filter>
            <action android:name = "com.example.activitytest.ACTION_START"/>
            <action android:name = "android.intent.category.DEFAULT"/>
            <action android:name = "com.example.activitytest.MY_CATEGORY"/>
        </intent-filter>
    </activity>
    ```

    - 同时匹配Action和Category

     ```java
     btnJumptoSecond.setOnClickListener(new View.OnClickListener()
     {
         @Override
         public void onClick(View v)
         {
             Intent intent = new Intent("com.example.activitytest.ACTION_START");
             intent.addCategory("com.example.activitytest.MY_CATEGORY");
             startActivity(intent);
         }
     });
     ```

3. 其他Intent用法

    - android:scheme   协议:http
    - android:host     主机名:www.google.com
    - android:port     端口
    - android:path     主机名和端口之后的部分
    - android:mimeType 数据类型

    - view: 显示网页
    - geo : 显示地理位置
    - tel : 拨打电话

    ```java
    <activity android:name=".ThirdActivity">
        <intent-filter>
            <action android:name="android.intent.action.VIEW"/>
            <category android:name="android.intent.category.DEFAULT"/>
            <data android:scheme="http"/>
        </intent-filter>
    </activity>
    ```

### 传递数据

- FirstActivity

    ```java
    btnJumptoSecond.setOnClickListener(new View.OnClickListener()
    {
        @Override
        public void onClick(View v)
        {
            // intent 自带传值
            String data = "Hello SecondActivity";
            Intent intent = new Intent(FirstActivity.this,SecondActivity.class);
            intent.puyExtra("extra_data",data);
            startActivity(intent);

            // 传 Bundle 写法
            Bundle bundle = new Bundle();
            bundle.putString("key1",value1);
            bundle.putString("key2",value2);

            Intent intent = new Intent(FirstActivity.this,SecondActivity.class);
            intent.putExtras(bundle);
            startActivity(intent);
        }
    });
    ```
- SecondActivity
    ```java
    public class SecondActivity extends AppCompatActivity
    {
        @Override
        protected void onCreate(Bundle savedInstanceState)
        {
            super.onCreate(savadInstanceState);
            setContentView(R.layout.second_layout);

            // intent 自带传值
            Intent intent = gentIntent();
            String data = intent.getStringExtra("extra_data");
            Log.d("SecondActivity",data);

            // 传 bundle 写法
            Intent intent = gentIntent();
            Bundle bundle = intent.getExtras();
            String data = bundle.getString("key1");
            Log.d("SecondActivity",data);
        }
    }
    ```

### 返回数据

- FirstActivity 以startActivityForResult的形式启动目标Activity
    ```java
    btnJumptoSecond.setOnClickListener(new View.OnClickListener()
    {
        @Override
        public void onClick(View v)
        {
            Intent intent = new Intent(FirstActivity.this,SecondActivity.class);
            startActivityForResult(intent,115);
        }
    });

    ```
- SecondActivity 设置返回数据
    ```java
    public class SecondActivity extends AppCompatActivity
    {
        @Override
        protected void onCreate(Bundle savedInstanceState)
        {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.second_layout);

            Buttton btnReturnFirst = (Button) findViewById(R.id.btn_ReturnFirst);
            btnReturnFirst.setOnClickListener(new View.OnClickListener()
            {
                @Override
                public void onClick(View v)
                {
                    Intent intent = new Intent();
                    intent.putExtra("data_return","Hello FirstActivity");
                    setResult(RESULT_OK,intent);
                    finish();
                }
            });
        }
    }
    ```
- 在 FirstActivity 中重写onActivityResult()方法获得返回数据
    ```java
    @Override
    protected void onActivityResult(int requestCode,int resultCode,Intent data)
    {
        switch (requestCode)
        {
            case 115:
                if(resultCode == RESULT_OK)
                {
                    String returnedData = data.getStringExtra("data_return");
                    Log.d("FirstActivity",returnedData);
                }
                break;
            default:
                break;
        }
    }
    ```

### 生命周期

1. 活动状态

    - 运行状态
    - 暂停状态
    - 停止状态
    - 销毁状态

2. 活动生存期

    - onCreate()
    - onStart()
    - onResume()
    - onPause()
    - onStop()
    - onDestroy()
    - onRestart()

    ![lifecycle](https://upload-images.jianshu.io/upload_images/3994917-019104c9fc5cb373.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/513/format/webp)

- ### 活动回收时临时数据的处理

    重写onSavaInstanceState方法 保存临时数据
    ```java
    @Override
    protected void onSavaInstanceState(Bundle outState)
    {
        super.onSaveInstanceState(outState);
        String tempData = "Something you just typed.";
        outState.putString("data_key",tempData);
    }
    ```
    被回收后返回调用onCreate()方法取回临时数据
    ```java
    @Override
    protected void onCreate(Bundle savadIntanceState)
    {
        super.onCreate(savedInstanceState);
        setContentView(R.layout_activity_main);

        if (savedInstanceState != null)
        {
            String tempData = savedInstanceState.getString("data_key");
            Log.d("Temp Data:",tempData);
        }
    }
    ```

### 活动的启动模式

- 修改AndroidMainifest.xml中Activity的启动模式

    ```xml
    <activity
        android:name=".FirstActivity"
        android:launchMode="singleTop"
        android:label="This is FirstActivity">
        <intent-filter>
            <action android:name="android.intent.action.MAIN"/>
            <category android:name="android.intent.category.LAUNCHER"/>
        </intent-filter>
    </activity>
    ```

    1. standard

        Android的默认启动模式,这种模式下,Activity可以有多个实例,每次启动Activity,无论任务栈中是否已经有这个Activity的实例,系统都会创建一个新的Activity实例.

    2. singleTop

        singleTop和standard模式非常相似,主要区别就是当一个singleTop模式的Activity已经位于任务栈的栈顶,再去启动它时,不会在创建新的实例,如果不位于栈顶,就会创建新的实例. 位于栈顶时启动会调用onNewIntent()函数 .

    3. singleTask

        singleTask模式的Activity在同一Task内只有一个实例,如果Activity已经位于栈顶,系统不会创建新的Activity实例,和singleTop模式一样.但Activity已经存在但不位于栈顶时,系统就会把该Activity移动到栈顶,并把它上面的Activity出栈,singleTask是Task内单例的,需要单例才会设置这个模式.

    4. singleInstance

        也是单例,和singleTask不同的是,singleTask只是任务栈内单例,系统是可以有多个singleTask Activity实例的,而singleInstance Activity在整个系统中只有一个实例,启动singleInstance Activity时,系统会创建一个新的任务栈,并且这个任务栈只有他一个Activity.

### 活动最佳实践

1. 创建BaseActivity 扩展Activity功能

    ```java
    public class BaseActivity extends AppCompatActivity
    {
        @Override
        protected void onCreate(Bundle savedInstanceState)
        {
            super.onCreate(savedInstanceState);
            Log.d("BaseActivity",getClass().getSimpleName); // 实现所有活动创建时打印名字

                ActivityCollector.addActivity(this); // 对所有Activity进行管理
            }

        @Override
        protected void onDestroy()
        {
            super.onDestroy();
            ActivityCollector.removeActivity(this); // 对所有Activity进行管理
        }
    }
    ```

2. 创建ActivityCollector管理所有活动

    ```java
    pubLic class ActivityCollector
    {
        public static List<Activity> activities = new ArrayList<>();
        public static void addActivity(Activity activity)
        {
            activities.add(activity);
        }
        public static void removeActivity(Activity activity)
        {
            activities.remove(activity);
        }
        public static void finishAll()
        {
            for(Activity activity:activities)
            {
                if(!activity.isFinishing())
                {
                    activity.finish();
                }
            }
        }
    }
    ```
    一键关闭
    ```java
    protected void onCreate(Bundle savedInstanceState)
    {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.finish_layout);
        Button btnFinish = (Button) findViewById(R.id.btn_Finish);
        btnFinish.setOnClickListener(new View.OnClickListener()
        {
            @Override
            public void onClick(View v)
            {
                ActivityCollector.finishAll(); // 关闭所有Activity
                android.os.Process.killProcess(android.os.Process.myPid()); // 杀死当前进程
            }
        });
    }
    ```

3. 启动活动

    目标活动启动代码
     ```java
     public class SecondActivity extends BaseActivity
     {
         public static void actionStart(Context context, String data1,String data2)
         {
             Intent intent = new Intent(context, SecondActivity.class);
             intent.putExtra("key1",data1);
             intent.putExtra("key2",data2);
             context.startActivity(intent);
         }
     }
     ```
     初始活动代码
     ```java
     btnJump.setOnClickListener(new OnClickListener()
     {
         @Override
         public void onClick(View v)
         {
             SecondAcvity.actionStart(FirstActivity.this,"data1","data2");
         }
     });
     ```

## 3.UI组件

### UI组件

#### Toast

```java
btnToast.setOnClickListener(new View.OnClickListener()
{
    @Override
    public void onClick(View v)
    {
        Toast.makeText(FirstActivity.this,"Toast Test",Toast.LENGTH_SHORT).show();
    }
});
```

#### Menu

  首先在res目录下新建一个menu文件夹,右击menu文件夹->new->Menu resource file 文件名输入main 加以下代码:

  ```xml
  <menu xmln:android="http://schemas.android.com/apk/res/android">
      <item
      android:id="@+id/add_item"
      android:title="Add"
      />
      <item
      android:id="@+id/remove_item"
      android:title="Remove"
      />
  </menu>
  ```

  回到Activity中重写onCreateOptionsMenu()方法

```java
public boolean onCreateOptionsMenu(Menu menu)
{
    getMenuInflater().inflate(R.menu.main,menu);
    return true;
}
```

Activity中重写onOptionsItemSelected()方法,响应点击事件

```java
public boolean onOptionsItemSelected(MenuItem item)
{
    switch(item.getItemId())
    {
        case R.id.add_item:
            Toast.makeText(this,"You clicked Add",Toast.LENGTH_SHORT).show();
            break;
        case R.id.remove_item:
            Toast.makeText(this,"You clicked Remove",Toast.LENGTH_SHORT).show();
            break;
        default:
            break;
    }
    return ture;
}
```

#### TextView

```xml
<TextView
    android:id="@+id/text_view"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:gravity="center"
    android:textSize="24sp"
    android:textColor="#00ff00"
    android:text="This is TextView"
/>
```

#### Button

布局文件

```xml
<Button
android:id="@+id/button"
android:layout_width="match_parent"
android:layout_height="wrap_content"
android:text="Button"
android:textAllCaps="false"
/>
```

点击事件

```java
// 方法1
Button button = (Button) findVeiwById(R.id.button);
button.setOnClickListener(new View.OnClickListener()
{
    @Override
    public void onClick(View v)
    {
        // 添加点击事件逻辑
    }
});

// 方法2 需要实现View.OnClickListener接口
public class MainActivity extends AppCompatActivity implements View.OnClickListener
{
    @Override
    protected void onCreate(Bundle savedInstanceState)
    {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        Button button = (Button) findViewById(R.id.button);
        button.setOnClickListener(this);
    }

    @Override
    public void onClick(View v)
    {
        switch (v.getId())
        {
            case R.id.button:
                // 添加点击事件逻辑
                break;
            default:
                break;
        }
    }
}
```

#### EditText

```xml
<EditText
    android:id="@+id/edit_text"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:hint="Type something here"
    android:maxLines="3"
/>
```

代码示例

```java
public class MainActivity extends AppCompatActivity implements View.OnClickListener
{
    private EditText editText;
    @Override
    protected void onCreate(Bundle savedInstanceState)
    {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        Button button = (Button) findViewById(R.id.button);
        editText = (EditText) findViewById(R.id.exit_text);
        button.setOnClickListener(this);
    }
    @Override
    public void onClick(View v)
    {
        switch(v.getId())
        {
            case R.id.button:
                String inputText = editText.getText().toString();
                Toast.makeText(MainActivity.this,inputText,Toast.LENGTH_SHORT).show();
                break;
            default:
                break;
        }
    }
}
```

#### ImageView

```xml
<ImageView
    android:id="@+id/image_view"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:src="@drawable/img_1"
/>
```

示例代码

```java
public class MainActivity extends AppCompatActivity implements View.OnClickListener
{
    private ImageView imageView;
    @Override
    protected void onCreate(Bundle savedInstanceState)
    {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        Button button = (Button) findViewById(R.id.button);
        imageView = (ImageView) findViewById(R.id.image_view);
        button.setOnClickListener(this);
    }
    @Override
    public void onClick(View v)
    {
        switch(v.getId())
        {
            case R.id.button:
                imageView.setImageResource(R.drawable.img_2);
                break;
            default:
                break;
        }
    }
}
```

#### ProgressBar

```xml
<ProgressBar
    android:id="@+id/progress_bar"
    android:layout_width="math_parent"
    androdi:layout_height="wrap_content"
    style="?android:attr/progressBarStyleHorizontal"
    android:max="100"
/>
```

示例代码

```java
public class MainActivity extends AppCompatActivity implements View.OnClickListener
{
    private ProgressBar progressBar;
    @Override
    protected void onCreate(Bundle savedInstanceState)
    {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        Button button = (Button) findViewById(R.id.button);
        progressBar = (ProgressBar) findViewById(R.id.progress_bar);
        button.setOnClickListener(this);
    }
    @Override
    public void onClick(View v)
    {
        switch(v.getId())
        {
            case R.id.button:
                // 设置显隐
                if(progressBar.getVisibility() == View.GONE)
                {
                    progressBar.setVisibility(View.VISIBLE);
                }
                else
                {
                    progressBar.setVisibility(View.GONE);
                }
                // 设置进度
                int progress = progressBar.getProgress();
                progress = progress + 10;
                progressBar.setProgress(progress);
                break;
            default:
                break;
        }
    }
}
```

#### AlertDialog

```java
public class MainActivity extends AppCompatActivity implements View.OnClickListener
{
    ...
    @Override
    public void onClick(View v)
    {
        switch(v.getId())
        {
            case R.id.button:
            AlertDialog.Builder dialog = new AlertDialog.Builder(MainActivity.this);
            dialog.setTittle("This is Dialog");
            dialog.setMessage("Something important.");
            dialog.setCancelable(false);
            dialog.setPositiveButton("OK", new DialogInterface.OnClickListener()
            {
                @Override
                public void onClick(DialogInterface dialog,int which)
                {
                    // 确认的点击事件
                }
            });
            dialog.setNegativeButton("Cancle",new DialogInterface.OnClickListener()
            {
                @Override
                public void onClick(DialogInterface dialog,int which)
                {
                    // 取消的点击事件
                }
            });
            dialog.show();
            break;
        default:
            break;
        }
    }
}
```

#### ProgressDialog

ProgressDialog和AlertDialog有点相似,都能屏蔽其他控件的交互能力,不同的是ProgressDialog会在对话框中显示一个进度条

```java
public class MainActivity extends AppCompatActivity implements View.OnClickListener
{
    ...
    @Override
    public void onClick(View v)
    {
        switch(v.getId())
        {
            case R.id.button:
                ProgressDialog progressDialog = new ProgressDialog(MainActivity.this);
                progressDialog.setTitle("This is ProgressDialog");
                progressDialog.setMessage("Loading...");
                progressDialog.setCanclable("true");
                progressDialog.show();
                break;
            default:
                break;
        }
    }
}
```

如果在setCancelable()中传入了false,表示ProgressDialog是不能通过Back键取消的,这时需要做好代码控制,加载完成后调ProgressDialog的dismiss()方法来关闭对话框.

### UI布局

#### LinearLayout

##### 1. 方向

- 属性

  ``` xml
  android:orientation="horizontal"     //水平
  android:orientation="vertical"       //垂直
  ```

- 实例

  - 水平

    ```xml
    android:orientation="horizontal"
    ```
   ​
    ![水平](https://upload-images.jianshu.io/upload_images/5516419-eae5a492683a65c3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/473/format/webp)

  - 垂直

    ```xml
    android:orientation="vertical"
    ```

    ![垂直](https://upload-images.jianshu.io/upload_images/5516419-b4b547ea9c90deb0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/474/format/webp)

##### 2. 对齐方式

- 属性

    ```xml
    android:gravity          //本元素的子元素 相对它的对齐方式
    android:layout_gravity   //本元素相对它的父元素的对齐方式  对于其他属性 有layout_前缀的 原  则一样

    //常用属性值
    android:gravity="center_horizontal"         //子控件水平方向居中
    android:gravity="center_vertical"           //子控件竖直方向居中
    android:gravity="center"                    //子控件竖直方向和水平方向居中
    android:gravity= start || end || top || bottom //子控件左对齐 || 右对齐 || 顶部对齐 ||   底部对齐
    android:gravity= left || right               //子控件左对齐 || 右对齐
    ```
 ​
- 实例
  - 第一个子空间设置水平垂直

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:tools="http://schemas.android.com/tools"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical"
        tools:context="com.example.icephone_1.layouttest.MainActivity">
        <TextView
            android:id="@+id/text_1"
            android:textSize="30sp"
            android:layout_gravity="center_horizontal"  //子控件设置水平垂直
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Hello World!" />
        <TextView
            android:id="@+id/text_2"
            android:textSize="30sp"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Hello World!" />
    </LinearLayout>
    ```

  - 设置LinearLayout水平垂直

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:tools="http://schemas.android.com/tools"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical"
        android:gravity="center_horizontal"    //设置LinearLayout水平垂直
        tools:context="com.example.icephone_1.layouttest.MainActivity">

        <TextView
            android:id="@+id/tx_one"
            android:textSize="30sp"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Hello World!" />

        <TextView
            android:id="@+id/tx_two"
            android:textSize="30sp"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Hello World!" />

    </LinearLayout>
    ```

  ​

##### 3. 子控件大小

- 属性

  ```xml
  layout_height   //高
  layout_width    //宽
  layout_weight   //权重

  //常用属性值
  layout_height = "wrap_content"  //根据控件内容的大小决定大小
  layout_height = "match_parent"  //子控件填满父级控件
  layout_height = "10dp"          //直接设置大小

  //当控件大小为"0dp"时,需要配合weight使用,表示比例
  layout_height = "0"
  layout_weight = "1"
  ```

- 实例

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
      xmlns:tools="http://schemas.android.com/tools"
      android:layout_width="match_parent"
      android:layout_height="match_parent"
      android:orientation="horizontal"
      android:gravity="center"
      tools:context="com.example.icephone_1.layouttest.MainActivity">

      <TextView
          android:id="@+id/tx_one"
          android:textSize="30sp"
          android:layout_width="0dp"
          android:layout_height="wrap_content"
          android:layout_weight="1"        //设置占比例为1
          android:text="Hello World!"
          android:background="#9c9292"/>

      <TextView
          android:id="@+id/tx_two"
          android:textSize="30sp"
          android:layout_width="0dp"
          android:layout_height="wrap_content"
          android:layout_weight="1"        //设置占比例为1
          android:text="Hello World!"
          android:background="#0d6074"/>

  </LinearLayout>
  ```

  ![1:1](https://upload-images.jianshu.io/upload_images/5516419-0a9b39e283f6e1ef.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/478/format/webp)

  当tx_one的layout_weight 属性改为"2"时:

  ![2:1](https://upload-images.jianshu.io/upload_images/5516419-f34d18a7030caec6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/474/format/webp)

#### RelativeLayout

##### 1. 属性值为true 或 false

- 属性

  ```xml
  android:layout_centerHrizontal   //水平居中
  android:layout_centerVertical    //垂直居中
  android:layout_centerInparent    //相对于父元素完全居中
  android:layout_alignParentBottom //贴紧父元素的下边缘
  android:layout_alignParentLeft   //贴紧父元素的左边缘
  android:layout_alignParentRight  //贴紧父元素的右边缘
  android:layout_alignParentTop    //贴紧父元素的上边缘
  ```

  ​

- 实例

  把三个TextView上中下排列

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
      xmlns:tools="http://schemas.android.com/tools"
      android:layout_width="match_parent"
      android:layout_height="match_parent"
      tools:context="com.example.icephone_1.layouttest.MainActivity">

      <TextView
          android:id="@+id/tx_one"
          android:textSize="30sp"
          android:layout_width="wrap_content"
          android:layout_height="wrap_content"

          android:layout_alignParentStart="true"
          android:layout_alignParentLeft="true"

          android:text="Hello World!"
          android:background="#9c9292" />

      <TextView
          android:id="@+id/tx_two"
          android:textSize="30sp"
          android:layout_width="wrap_content"
          android:layout_height="wrap_content"

          android:layout_alignParentBottom="true"
          android:layout_alignParentEnd="true"
          android:layout_alignParentRight="true"

          android:text="Hello World!"
          android:background="#0d6074" />

      <TextView
          android:id="@+id/tx_three"
          android:textSize="30sp"
          android:layout_width="wrap_content"
          android:layout_height="wrap_content"

          android:layout_centerInParent="true"

          android:text="Hello World!"
          android:background="#a73956" />

  </RelativeLayout>
  ```

  - 使用的属性用两个空行标示

  - 这里需要注意的是，在最新版本的Andorid中，单独使用包含Start或者End属性的话，会报错，提示需要再加入Left和Right属性；而单独使用Left和Right属性，会提示一个warning，提示推荐加入Start或者End属性

    ![上 中 下](https://upload-images.jianshu.io/upload_images/5516419-9c2e15084893f0db.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/474/format/webp)

##### 2. 属性值为id

- 属性

  ``` xml
  android:layout_below     //在某元素的下方
  android:layout_above     //在某元素的的上方
  android:layout_toLeftOf  //在某元素的左边
  android:layout_toRightOf //在某元素的右边
  android:layout_alignTop  //本元素的上边缘和某元素的的上边缘对齐
  android:layout_alignLeft //本元素的左边缘和某元素的的左边缘对齐
  android:layout_alignBottom//本元素的下边缘和某元素的的下边缘对齐
  android:layout_alignRight //本元素的右边缘和某元素的的右边缘对齐
  ```

  根据另一个控件的位置来确定控件的位置

- 实例

  把三个控件排成阶梯状

  ``` xml
  <?xml version="1.0" encoding="utf-8"?>
  <RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
      xmlns:tools="http://schemas.android.com/tools"
      android:layout_width="match_parent"
      android:layout_height="match_parent"
      tools:context="com.example.icephone_1.layouttest.MainActivity">

      <TextView
          android:id="@+id/tx_one"
          android:textSize="30sp"
          android:layout_width="250dp"
          android:layout_height="wrap_content"

          android:layout_alignStart="@+id/tx_three"
          android:layout_alignLeft="@+id/tx_three"
          android:layout_above="@+id/tx_three"

          android:text="Hello World!"
          android:background="#9c9292" />

      <TextView
          android:id="@+id/tx_two"
          android:textSize="30sp"
          android:layout_width="250dp"
          android:layout_height="wrap_content"

          android:layout_below="@+id/tx_three"
          android:layout_alignEnd="@+id/tx_three"
          android:layout_alignRight="@+id/tx_three"

          android:text="Hello World!"
          android:background="#0d6074" />

      <TextView
          android:id="@+id/tx_three"
          android:textSize="30sp"
          android:layout_width="wrap_content"
          android:layout_height="wrap_content"

          android:layout_centerInParent="true"

          android:text="Hello World!"
          android:background="#a73956" />

  </RelativeLayout>
  ```

  ​

  ![阶梯状](https://upload-images.jianshu.io/upload_images/5516419-b72029e4d7d71590.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/475/format/webp)

##### 3.属性值为具体的像素值

- 属性

  ```xml
  android:layout_marginBottom  //离某元素底边缘的距离
  android:layout_marginLeft    //离某元素左边缘的距离
  android:layout_marginRight   //离某元素右边缘的距离
  android:layout_marginTop     //离某元素上边缘的距离
  ```

  ![布局](https://upload-images.jianshu.io/upload_images/5516419-1e886a01233869a7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/771/format/webp)

- 实例

  - 原图上面和下面的图片一样

    ![原图](https://upload-images.jianshu.io/upload_images/5516419-fb7f9d50895f29e8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/352/format/webp)

  - 给下面的图片添加padding

    ```xml
    android:paddingTop="8dp"
    android:paddingLeft="20dp"
    android:paddingStart="60dp"
    ```

    ![添加padding](https://upload-images.jianshu.io/upload_images/5516419-fb7f9d50895f29e8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/352/format/webp)

  - 给下面的图片添加magin

    ```xml
    android:layout_marginTop="8dp"
    android:layout_marginLeft="20dp"
    android:layout_marginStart="20dp"
    ```

    ![添加margin](https://upload-images.jianshu.io/upload_images/5516419-4cd0de8ef465727c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/404/format/webp)

#### FrameLayout

可能是最简单的一种布局，没有任何定位方式，当我们往里面添加控件的时候，会默认把他们放到这块区域的左上角，帧布局的大小由控件中最大的子控件决定，如果控件的大小一样大的话，那么同一时刻就只能看到最上面的那个组件，后续添加的控件会覆盖前一个。由于帧布局的特性，它的应用场景并不是很多，不过它经常配合Fragment使用。

##### 1. 属性

```xml
android:foreground            //设置改帧布局容器的前景图像
android:foregroundGravity     //设置前景图像显示的位置
```

##### 2. 实例

```xml
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context="com.example.icephone_1.layouttest.MainActivity">

    <TextView
        android:id="@+id/tx_one"
        android:textSize="30sp"
        android:layout_width="300dp"
        android:layout_height="300dp"
        android:text="Hello World!"
        android:background="#9c9292" />

    <TextView
        android:id="@+id/tx_two"
        android:textSize="30sp"
        android:layout_width="200dp"
        android:layout_height="200dp"
        android:text="Hello World!"
        android:background="#1c7649" />

    <TextView
        android:id="@+id/tx_three"
        android:textSize="30sp"
        android:layout_width="100dp"
        android:layout_height="100dp"
        android:text="Hello World!"
        android:background="#a73956" />

</FrameLayout>
```

![FrameLayout](https://upload-images.jianshu.io/upload_images/5516419-a1c0b6b504dc9bf1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/479/format/webp)

#### ConstraitLayout

#### GridLayout

#### FlexboxLayout

### 自定义控件

#### 引入布局

自定义标题栏布局

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:background="@drawable/title_bg">

    <Button
        android:id="@+id/title_back"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center"
        android:layout_margin="5dp"
        android:background="@drawable/back_bg"
        android:text="Back"
        android:textColor="fff"
    />
    <TextView
        android:id="@+id/title_text"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_gravity="center"
        android:layout_weight="1"
        android:gravity="center"
        android:text="Title Text"
        android:textColor="#fff"
        android:textSize="24sp"
    />
    <Button
        android:id="@+id/title_edit"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center"
        android:layout_margin="5dp"
        android:background="@drawable/edit_bg"
        android:text="Edit"
        android:textColor="#fff"
    />
</LinearLayout>
```

把自定义布局引入activity_main

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <include layout="@layout/title"/>
<LinearLayout>
```

隐藏系统自带标题栏

```java
public class MainActivity extends AppCompatActivity
{
    @Override
    protected void onCreate(Bundle savedInstanceState)
    {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        ActionBar actionbar = getSupportActionBar();
        if(actionbar != null)
        {
            actionbar.hide();
        }
    }
}
```

#### 创建自定义控件

新建TitleLayout继承自LinearLayout,让它成为我们自动一的标题栏控件

```java
public class TitleLayout extends LinearLayout
{
    public TitleLayout(Context context, AttributeSet attrs)
    {
        super(context,attrs);
        LayoutInflater.from(context).inflate(R.layout.title,this);
    }
}
```

现在自定义控件已经创建好了,然后我们需要在布局文件中添加这个自定义控件,修改activity_main.xml中的代码

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <com.example.uicustomviews.TittleLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
    />
</LinearLayout>
```

添加自定义控件和添加普通控件的方式基本是一样的,只不过需要指明控件的完整类名,重新运行程序,此时的效果和使用引入布局的效果是一样的.
下面未标题栏按钮注册点击事件,修改TitleLayout中的代码

```java
public TitleLayout(Context context,AttributeSet attrs)
{
    super(context,attrs);
    LayoutInflater.from(context).inflate(R.layout.title,this);

    Button titleBack = (Button) findViewById(R.id.title.title_back);
    Button titleEdit = (Button) findViewById(R.id.title.title_edit);
    titleBak.setOnClickListener(new OnClickListener()
    {
        @Override
        public void onClick(View v)
        {
            ((Activity) getContext()).finish();
        }
    });
    titleEdit.setOnClickListener(new OnClickListener()
    {
        @Override
        public void onClick(View v)
        {
            Toast.makeText(getContext(),"You clicked Edit button",Toast.LENGTH_SHORT).show();
        }
    });
}
```

### ListView

- Item数据模型

    ```java
    public class Fruit
    {
        private String name;
        private int imageId;
        public Fruit(String name,int imageId)
        {
            this.name = name;
            this.imageId = iamgeId;
        }
        public String getName()
        {
            return name;
        }
        public int getImageId()
        {

        }
    }
    ```

- Item布局

    ```xml
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="wrap_content">

        <ImageView
            android:id="@+id/fruit_image"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"/>

        <TextView
            android:id="@+id/fruit_name"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_gravity="center_vertical"
            android:layout_marginLeft="10dp"/>

    </LinearLayout>
    ```

- ListView适配器

    ```java
    public class FruitAdapter extends ArrayAdapter<Fruit>
    {
        private int resourceId;

        public FruitAdapter(Context context, int textViewResourceId, List<Fruit> objects)
        {
            super(context,textViewResourceId,objects);
            resourceId = textViewResourceId;
        }
        @Override
        public View getView(int position, View convertView, ViewGroup parent)
        {
            Fruit fruit = getItem(position);
            // View view = LayoutInflater.from(getContext()).inflate(resourceId,parent,false);
            // 提升效率
            View view;
            ViewHolder viewHolder;
            if(convertView == null)
            {
                view = LayoutInflater.from(getContext()).inflate(resourceId,parent,false);
                viewHolder = new ViewHolder();
                viewHolder.fruitImage = (ImageView) view.findViewById(R.id.fruit_image);
                viewHolder.fruitName = (TextView) view.findViewById(R.id.fruit_name);
                view.setTag(viewHolder);
            }
            else
            {
                view = convertView;
                viewHolder = (ViewHolder) view.getTag();
            }

            viewHolder.fruitImage.setImageResource(fruit.getImageId());
            viewHolder.fruitNmae.setText(fruit.getName());

            return view;
        }
        class ViewHolder
        {
            ImageView fruitImage;
            TextView fruitName;
        }
    }
    ```

- MainActivity

    ```java
    public class MainActivity extends AppCompatActivity
    {
        private List<Fruit> fruitList = new ArrayList<>();

        @Override
        protected void onCreate(Bundle savedInstanceState)
        {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);

            initFruits();
            FruitAdapter adapter = new FruitAdapter(MainActivity.this,R.layout.fruit_item,fruitList);

            ListView listView = (ListView) findViewById(R.id.list_view);
            listView.setAdapter(adapter);
            // item点击事件
            listView.setOnItemClickListener(new AdapterView.OnItemClickListener()
            {
                @Override
                public void onItemClick(AdapterView<?> parent,View view,int position,long id)
                {
                    Fruit fruit = fruitList.get(position);
                    Toast.makeText(MainActivity.this,fruit.getName(),Toast.LENGTH_SHORT).show();
                }
            });
        }

        private void initFruits()
        {
            Fruit apple = new Fruit("Apple",R.drawable.apple_pic);
            fruitList.add(apple);
        }
    }
    ```

### RecyleView

## 4.碎片

## 5.广播

## 6.数据持久化

## 7.跨程序共享数据

## 8.多媒体

## 9.网络技术

## 10.服务

## 11.位置服务

## 12.MaterialDesign设计

## 13.进阶技巧

## 14.实战演练

## 15.应用发布
