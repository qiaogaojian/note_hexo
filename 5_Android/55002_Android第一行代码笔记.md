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

### Toast

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

### Menu

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

### TextView

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

### Button

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

### EditText

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

### ImageView

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

### ProgressBar

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

### AlertDialog

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

### ProgressDialog

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