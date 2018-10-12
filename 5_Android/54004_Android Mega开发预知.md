# Android Mega开发预知

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

## Glide加载webp

## Fresco加载动态webp

## Rxjava2

## RxBus

## RxBinding

## Rxlifecyle

## Agentweb