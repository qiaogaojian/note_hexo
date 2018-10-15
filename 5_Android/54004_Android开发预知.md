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

### 引入依赖

```java
    //RxJava
    implementation 'io.reactivex.rxjava2:rxandroid:2.0.1'

    // Because RxAndroid releases are few and far between, it is recommended you also

    // explicitly depend on RxJava's latest version for bug fixes and new features.
    implementation 'io.reactivex.rxjava2:rxjava:2.1.9'

    // Lifecycle handling APIs for Android apps using RxJava
    implementation 'com.trello.rxlifecycle2:rxlifecycle:2.2.1'

    // If you want to bind to Android-specific lifecycles
    implementation 'com.trello.rxlifecycle2:rxlifecycle-android:2.2.1'

    // If you want pre-written Activities and Fragments you can subclass as providers
    implementation 'com.trello.rxlifecycle2:rxlifecycle-components:2.2.1'

    // If you want to use Navi for providers
    implementation 'com.trello.rxlifecycle2:rxlifecycle-navi:2.2.1'

    //RxJava　adapter
    implementation 'com.jakewharton.retrofit:retrofit2-rxjava2-adapter:1.0.0'
    implementation 'com.squareup.retrofit2:adapter-rxjava2:2.2.0'

    //RxBinding
    implementation 'com.jakewharton.rxbinding2:rxbinding-support-v4:2.1.0'
    implementation 'com.jakewharton.rxbinding2:rxbinding-appcompat-v7:2.1.0'
    implementation 'com.jakewharton.rxbinding2:rxbinding-design:2.1.0'
    implementation 'com.jakewharton.rxbinding2:rxbinding-recyclerview-v7:2.1.0'
```

### 代码示例

```java
public class MainActivity extends AppCompatActivity
{

    private static final String TAG = "MainActivity";
    private String serverData;

    @Override
    protected void onCreate(Bundle savedInstanceState)
    {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        testRxjava1();
        testRxJava2();
    }

    private void testRxjava1()
    {
        Observable.create(new ObservableOnSubscribe<String>()
        {
            @Override
            public void subscribe(ObservableEmitter<String> emitter) throws Exception
            {
                emitter.onNext("TestRxJava1 => the string value passed");
                emitter.onError(new Exception("Error occur"));
            }
        }).subscribe(new Observer<String>()
        {
            @Override
            public void onSubscribe(Disposable d)
            {

            }

            @Override
            public void onNext(String s)
            {
                Log.d(TAG, "onNext: "+s);
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

    private void testRxJava2()
    {
        Observable.create(new ObservableOnSubscribe<String>()
        {
            @Override
            public void subscribe(ObservableEmitter<String> emitter) throws Exception
            {
                getDataFromServer(emitter);
            }
        }).subscribeOn(Schedulers.io())
        .observeOn(AndroidSchedulers.mainThread())
        .subscribeWith(new Observer<String>()
        {
            @Override
            public void onSubscribe(Disposable d)
            {

            }

            @Override
            public void onNext(String s)
            {
                TextView textView = (TextView) findViewById(R.id.textview);
                textView.setText(s);
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

    private void getDataFromServer(final ObservableEmitter<String> emitter)
    {
        OkHttpClient okHttpClient = new OkHttpClient();

        FormBody formBody = new FormBody.Builder()
                 .add("number","15710579216")
                .build();

        Request request = new Request.Builder()
                .url("http://cx.shouji.360.cn")
                .post(formBody)
                .build();

        okHttpClient.newCall(request).enqueue(new Callback()
        {
            @Override
            public void onFailure(Call call, IOException e)
            {

            }

            @Override
            public void onResponse(Call call, Response response) throws IOException
            {
                serverData = response.body().string();
                Log.d(TAG, "onResponse: " + serverData);
                emitter.onNext(serverData);
            }
        });
    }
}
```

### 常用操作符

- map()

    map()可以让你在最初的Observable和最终的Subscriber之间做任何转换

    ```java
    Observable.create(new Observable.OnSubscribe<Student>() {
            @Override
            public void call(Subscriber<? super Student> subscriber) {
                subscriber.onNext(getStudentInfo(123456777));
                subscriber.onCompleted();
            }
        }).map(new Func1<Student, String>() {

            @Override
            public String call(Student student) {
                return student.getName();
            }
        })
        .subscribeOn(Schedulers.io())
        .observeOn(AndroidSchedulers.mainThread())
        .subscribe(new Action1<String>() {
            @Override
            public void call(String s) {
                mTvMsg.setText(s);
            }
        });
    ```

- flatmap()

    假设软件开发公司A，有人数多于10名的项目经理，但是有同时进行的超过30个的项目，所以每个项目经理要同时负责不至1个软件项目，现在要打印出每个项目经理手下的开发人员的名字？怎么实现呢？先看看map的实现方式

    ```java
    String[] pm = new String[]{"PM1","PM2","PM3"};

    Observable.from(pm)
    .map(new Func1<String, List<String>>() {
        @Override
        public List<String> call(String s) {
            //通过项目经理名字得到他负责的项目列表
            List<String> projs = getProject(s);
            return projs;
        }
    }).subscribe(new Action1<List<String>>() {
        @Override
        public void call(List<String> projs) {

            Observable.from(projs)
                    .map(new Func1<String, List<String>>() {
                        @Override
                        public List<String> call(String proj) {
                            //通过项目名称得到项目组成员的名字
                            List<String> names = getPersonNames(proj);
                            return names;
                        }
                    }).subscribe(new Action1<List<String>>() {
                @Override
                public void call(List<String> names) {
                    //打印项目组的人员的名字
                    for (String name : names) {
                        Log.d(TAG, "person name:" + name);
                    }
                }
            });
        }
    });
    ```

    Observable传递过来的是一个数组，或者是集合。数组或者集合是没有办法直接转换成单一的类型对象的。这时候显示map()已经不太适应，而RxAndroid也提供了这种情况的解决方案。那就是flatmap().

    ```java
    Observable.from(pm)
        .map(new Func1<String, List<String>>() {
            @Override
            public List<String> call(String s) {
                //通过项目经理名字得到他负责的项目列表
                List<String> projs = getProject(s);
                return projs;
            }
        }).flatMap(new Func1<List<String>, Observable<List<String>>>() {
            @Override
            public Observable<List<String>> call(List<String> projs) {
                List<List<String>> names = null;
                for(String proj:projs){
                    //通过项目名称得到项目组成员的名字
                    names.add(getPersonNames(proj));
                }
                return  Observable.from(names);
            }
        })
        .subscribe(new Action1<List<String>>() {
            @Override
            public void call(List<String> names) {

                //打印项目组的人员的名字
                for (String name : names) {
                    Log.d(TAG, "person name:" + name);
                }
            }
        });
    ```

    我们在中间的部分运行了flatmap()进行了一此变换，将原来的Observable对象替换了一个新的Observable对象，然后由这个新的Observable对象来对接Subscriber，而这一切都神不知鬼不觉的，所谓移花接木。

- take()

    只发射指定个数的数据项。

    ```java
    Observable.just(1, 2, 3, 4, 5, 6, 7, 8)
          .take(4)
          .subscribe(new Subscriber<Integer>() {
        @Override
        public void onNext(Integer item) {
            System.out.println("Next: " + item);
        }

        @Override
        public void onError(Throwable error) {
            System.err.println("Error: " + error.getMessage());
        }

        @Override
        public void onCompleted() {
            System.out.println("Sequence complete.");
        }
    });
    ```

- filter()

    这个也非常实用。可以过滤我们不需要处理的数据项，阻止它们的发射

    ```java
    Observable.just(1, 2, 3, 4, 5)
          .filter(new Func1<Integer, Boolean>() {
              @Override
              public Boolean call(Integer item) {
                return( item < 4 );
              }
          }).subscribe(new Subscriber<Integer>() {
        @Override
        public void onNext(Integer item) {
            System.out.println("Next: " + item);
        }

        @Override
        public void onError(Throwable error) {
            System.err.println("Error: " + error.getMessage());
        }

        @Override
        public void onCompleted() {
            System.out.println("Sequence complete.");
        }
    });
    ```

    运行结果:
    ```java
    Next: 1
    Next: 2
    Next: 3
    Sequence complete.
    ```

### comsumer

```java
        /**
         * Consumer是简易版的Observer，他有多重重载，可以自定义你需要处理的信息，我这里调用的是只接受onNext消息的方法，
         * 他只提供一个回调接口accept，由于没有onError和onCompete，无法再 接受到onError或者onCompete之后，实现函数回调。
         * 无法回调，并不代表不接收，他还是会接收到onCompete和onError之后做出默认操作，也就是监听者（Consumer）不在接收
         * Observable发送的消息，下方的代码测试了该效果。
         */
        final Consumer<String> consumer = new Consumer<String>() {
            @Override
            public void accept(@NonNull String s) throws Exception {
                Log.d("MainActivity", Thread.currentThread().getName() + " String:" + s);
            }
        };

        Observable<String> observable = Observable.create(new ObservableOnSubscribe<String>() {
            @Override
            public void subscribe(@NonNull ObservableEmitter<String> e) throws Exception {
                Log.d("MainActivity", Thread.currentThread().getName() + "emit Hello");
                e.onNext("Hello");
                Log.d("MainActivity", Thread.currentThread().getName() + "emit Complete");
                e.onComplete();
                Log.d("MainActivity", Thread.currentThread().getName() + "emit World");
                e.onNext("World");
            }
        });
```

## RxBus

## RxBinding

## Rxlifecyle