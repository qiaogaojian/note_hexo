# Android 开发预知

## MVVM

## OKHttp3

### 添加依赖

```java
dependencies {
    implementation 'com.squareup.okhttp3:okhttp:3.9.1'
}
```

### 添加权限

```xml
<!--网络权限-->
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<uses-permission android:name="android.permission.CHANGE_NETWORK_STATE" />
```

### 异步Get  异步Post

```java
public class MainActivity extends AppCompatActivity
{
    TextView textView;
    private String responseBody;
    private String TAG = "MainActivity";

    @Override
    protected void onCreate(Bundle savedInstanceState)
    {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        Button btnGet  = (Button) findViewById(R.id.button_get);
        Button btnPost = (Button) findViewById(R.id.button_post);

        textView = (TextView) findViewById(R.id.textview);

        btnGet.setOnClickListener(new View.OnClickListener()
        {
            @Override
            public void onClick(View v)
            {
                requestGet();
            }
        });

        btnPost.setOnClickListener(new View.OnClickListener()
        {
            @Override
            public void onClick(View v)
            {
                requestPost();
            }
        });
    }


    private void requestGet()
    {
        // step 1: 创建 okHttpClient 对象
        OkHttpClient okHttpClient = new OkHttpClient();

        // step 2: 创建一个请求,不指定方法时默认是GET
        Request request = new Request.Builder()
                .url("http:www.baidu.com")
                .method("GET",null) //可以省略
                .build();

        // step 3: 创建 Call 对象
        Call call = okHttpClient.newCall(request);

        // step 4: 开始异步请求
        call.enqueue(new Callback()
        {
            @Override
            public void onFailure(Call call, IOException e)
            {
                Toast.makeText(MainActivity.this, "请求失败", Toast.LENGTH_SHORT).show();
            }

            @Override
            public void onResponse(Call call, final Response response) throws IOException
            {
                responseBody = "Get:  \n" + response.body().string();
                Log.d(TAG, "Get onResponse:" + responseBody);

                MainActivity.this.runOnUiThread(new Runnable() // 在 UI 线程中更新 UI
                {
                    @Override
                    public void run()
                    {
                        textView.setText(responseBody);
                    }
                });
            }
        });
    }

    private void requestPost()
    {
        // step 1: 创建一个OkHttpClient对象
        OkHttpClient okHttpClient = new OkHttpClient();

        // step 2: 创建 FormBody
        FormBody formBody = new FormBody.Builder()
                .add("name", "Michael")
                .build();
        // step 3: 创建request
        Request request = new Request.Builder()
                .url("http://www.baidu.com")
                .post(formBody)
                .build();

        // step 4: 创建Call对象 并 开始异步请求
        okHttpClient.newCall(request).enqueue(new Callback()
        {
            @Override
            public void onFailure(Call call, IOException e)
            {
                Toast.makeText(MainActivity.this, "请求失败", Toast.LENGTH_SHORT).show();
            }

            @Override
            public void onResponse(Call call, Response response) throws IOException
            {
                responseBody = "Post  \n" + response.body().string();
                Log.d(TAG, "Post onResponse:" + responseBody);

                MainActivity.this.runOnUiThread(new Runnable()
                {
                    @Override
                    public void run()
                    {
                        textView.setText(responseBody);
                    }
                });
            }
        });
    }
}
```

## Retrofit2

### 添加依赖

```java
//网络请求
implementation 'com.squareup.okhttp3:okhttp:3.9.1'
implementation 'com.squareup.retrofit2:retrofit:2.3.0'
implementation 'com.squareup.retrofit2:converter-gson:2.3.0'
implementation 'com.squareup.retrofit2:adapter-rxjava2:2.2.0'

//RxJava
implementation 'io.reactivex.rxjava2:rxjava:2.0.0'
implementation 'io.reactivex.rxjava2:rxandroid:2.0.0'
```

### 添加权限

```xml
<!--网络权限-->
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<uses-permission android:name="android.permission.CHANGE_NETWORK_STATE" />
```

### 实体类

```java
public class PhoneResult
{
    /**
     * code : 0
     * data : {"province":"辽宁","city":"沈阳","sp":"移动"}
     */

    private int code;
    private PhoneData data;

    public int getCode()
    {
        return code;
    }

    public void setCode(int code)
    {
        this.code = code;
    }

    public PhoneData getData()
    {
        return data;
    }

    public void setData(PhoneData data)
    {
        this.data = data;
    }
}

public class PhoneData
{
    /**
     * province : 辽宁
     * city : 沈阳
     * sp : 移动
     */

    private String province;
    private String city;
    private String sp;

    public String getProvince()
    {
        return province;
    }

    public void setProvince(String province)
    {
        this.province = province;
    }

    public String getCity()
    {
        return city;
    }

    public void setCity(String city)
    {
        this.city = city;
    }

    public String getSp()
    {
        return sp;
    }

    public void setSp(String sp)
    {
        this.sp = sp;
    }
}
```

### 普通请求

#### Retrofit接口

```java
public interface ApiService
{
    // Retrofit使用接口和注解的形式来发起请求的
    // 请求 API 是: http://cx.shouji.360.cn/phonearea.php?number=13188888888
    // 把资源定位的部分放在GET注解内
    // Call<PhoneResult> 指明请求返回的数据将转换为PhoneResult
    // getPhoneResult 方法是发起请求时回调的方法,参数 number 对应API请求中的 number 参数
    @GET("/phonearea.php")
    Call<PhoneResult> getPhoneResult(@Query("number")String number);
}
```

#### 代码示例

```java
private void requestPhoneNumber()
{
    // step1 : 创建 Retrofit 实例
    Retrofit retrofit = new Retrofit.Builder()
            .addConverterFactory(GsonConverterFactory.create())
            .baseUrl("http://cx.shouji.360.cn")
            .build();

    // step2 : 创建 ApiService 和 Call 对象
    ApiService        apiService = retrofit.create(ApiService.class);
    Call<PhoneResult> call       = apiService.getPhoneResult(editText.getText().toString());

    // step3 : 开始异步请求
    call.enqueue(new Callback<PhoneResult>()
    {
        @Override
        public void onResponse(Call<PhoneResult> call, Response<PhoneResult> response)
        {
            PhoneResult result = response.body();
            PhoneData   data   = result.getData();

            textTitle.setText(editText.getText().toString());
            textProvince.setText(data.getProvince());
            textCity.setText(data.getCity());
            textSp.setText(data.getSp());
        }

        @Override
        public void onFailure(Call<PhoneResult> call, Throwable t)
        {

        }
    });
}
```

### RxJava请求

#### RxRetrofit接口

```java
public interface RxApiService
{
    @GET("/phonearea.php")
    Observable<PhoneResult> getPhoneResult(@Query("number") String number);
}
```

#### 代码示例

```java
private void requestByRxJava()
{
    Retrofit retrofit = new Retrofit.Builder()
            .baseUrl("http://cx.shouji.360.cn")
            .addConverterFactory(GsonConverterFactory.create())
            .addCallAdapterFactory(RxJava2CallAdapterFactory.create())
            .build();

    RxApiService rxApiService = retrofit.create(RxApiService.class);
    rxApiService.getPhoneResult(editText.getText().toString())
            .subscribeOn(Schedulers.io())
            .observeOn(AndroidSchedulers.mainThread())
            .subscribe(new Observer<PhoneResult>()
            {
                @Override
                public void onSubscribe(Disposable d)
                {

                }

                @Override
                public void onNext(PhoneResult value)
                {
                    PhoneResult result = value;
                    PhoneData   data   = result.getData();

                    textTitle.setText(editText.getText().toString());
                    textProvince.setText(data.getProvince());
                    textCity.setText(data.getCity());
                    textSp.setText(data.getSp());
                }

                @Override
                public void onError(Throwable e)
                {

                }

                @Override
                public void onComplete()
                {

                }
            });
}
```

## Glide4 加载 webp

### 引入依赖

```java
//glide 图片加载
implementation 'com.github.bumptech.glide:glide:4.7.1'
annotationProcessor 'com.github.bumptech.glide:compiler:4.7.1'
implementation 'com.github.bumptech.glide:okhttp3-integration:4.7.1'
//webpdecoder
implementation 'com.zlc.glide:webpdecoder:1.2.4.7.1'
```

### 申请权限

```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<uses-permission android:name="android.permission.CHANGE_NETWORK_STATE" />

<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
```

### 代码示例

```java
public class MainActivity extends AppCompatActivity
{

    @Override
    protected void onCreate(Bundle savedInstanceState)
    {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        ImageView imageView = (ImageView) findViewById(R.id.imageView);

        Glide.with(this)
                .load("http://asgard.image.mucang.cn/asgard/2017/12/28/10/45ab2f68a13546c1914b61d54160b8ae.webp")
                .into(imageView);
    }
}
```

## Fresco 加载动态 webp

### 引入依赖

```java
//加载webp专用插件
implementation 'com.facebook.fresco:fresco:1.5.0'
implementation 'com.facebook.fresco:animated-webp:1.5.0'
implementation 'com.facebook.fresco:webpsupport:1.5.0'
implementation 'com.facebook.fresco:animated-gif:1.5.0'
```

### Application 初始化

```java
public class MyApplication extends Application
{
    @Override
    public void onCreate()
    {
        super.onCreate();
        Fresco.initialize(this);
    }
}
```

### 添加权限

```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<uses-permission android:name="android.permission.CHANGE_NETWORK_STATE" />

<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
```

### 注册 Application name

```xml
<application
    android:allowBackup="true"
    android:icon="@mipmap/ic_launcher"
    android:label="@string/app_name"
    android:name=".MyApplication"  //这里注册
    android:roundIcon="@mipmap/ic_launcher_round"
    android:supportsRtl="true"
    android:theme="@style/AppTheme">
    <activity android:name=".MainActivity">
        <intent-filter>
            <action android:name="android.intent.action.MAIN" />

            <category android:name="android.intent.category.LAUNCHER" />
        </intent-filter>
    </activity>
</application>
```

### 代码示例

```java
public class MainActivity extends AppCompatActivity
{

    @Override
    protected void onCreate(Bundle savedInstanceState)
    {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        // 静态图
        SimpleDraweeView sdv = (SimpleDraweeView) findViewById(R.id.sdv);
        sdv.setImageURI("http://asgard.image.mucang.cn/asgard/2017/12/28/10/45ab2f68a13546c1914b61d54160b8ae.webp");

        // 动态图
        SimpleDraweeView sdva = (SimpleDraweeView) findViewById(R.id.sdva);
        Uri              uri  = Uri.parse("http://asgard.image.mucang.cn/asgard/2017/12/28/10/45ab2f68a13546c1914b61d54160b8ae.webp");
        DraweeController controller = Fresco.newDraweeControllerBuilder()
                .setUri(uri)
                .setAutoPlayAnimations(true)
                .build();
        sdva.setController(controller);

    }
}
```

## Agentweb

### 引入依赖

```java
//webView
implementation 'com.just.agentweb:agentweb:2.0.1'
```

### 申请权限

```xml
<uses-permission android:name="android.permission.INTERNET"/>
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
```

### UI布局

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.constraint.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:id="@+id/container"
    tools:context=".MainActivity">

</android.support.constraint.ConstraintLayout>
```

### 代码示例

```java
public class MainActivity extends AppCompatActivity
{
    protected AgentWeb agentWeb;
    private ConstraintLayout constraintLayout;

    @Override
    protected void onCreate(Bundle savedInstanceState)
    {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        constraintLayout = (ConstraintLayout) findViewById(R.id.container);


        agentWeb = AgentWeb.with(this)
                .setAgentWebParent(constraintLayout,new ConstraintLayout.LayoutParams(-1,-1))
                .useDefaultIndicator()
                .defaultProgressBarColor()
                .createAgentWeb()
                .ready()
                .go("http://www.jd.com");
    }
}
```

## Rxjava2

## RxBus

## RxBinding

## Rxlifecyle