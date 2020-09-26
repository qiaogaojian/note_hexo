# Android 进阶技巧

## 全局获取 Context

定制一个自己的 Application 类,以便于管理程序内的一些全局的状态信息,比如说全局 Context

```java
public class MyApplication extends Application
{
    private static Context context;

    @Override
    public void onCreate()
    {
        context = getApplicationContext();
    }

    public static Context getContext()
    {
        return context;
    }
}
```

用法

```java
MyApplication.getContext();
```

## 使用 Intent 传递对象

### Serializable 方式

```java
public class Person implements Serializable
{
    private String name;
    private int age;
    public String getName()
    {
        return name;
    }
    public void setName(String name)
    {
        this.name = name;
    }
    public int getAge()
    {
        return age;
    }
    public void setAge(int age)
    {
        this.age = age;
    }
}
```

使用

```java
Person person = new Person();
person.setName("Michael");
person.setAge(20);
Intent intent = new Intent (FirstActivity.this,SecondActivity.class);
intent.putExtra("person_data",person);
startActivity(intent);
```

### Parcelable 方式

```java
public class Person implements Parcelable
{
    private String name;
    private int age;
    public String getName()
    {
        return name;
    }
    public void setName(String name)
    {
        this.name = name;
    }
    public int getAge()
    {
        return age;
    }
    public void setAge(int age)
    {
        this.age = age;
    }

    @Override
    public int describeContents()
    {
        return 0;
    }

    @Override
    public void writeToParcel(Parcel dest,int flags)
    {
        dest.writeString(name);
        dest.writeInt(age);
    }

    public static final Parcelable.Creator<Person> CREATOR new Parcelable.Creator<Person>()
    {
        @Override
        public Person createFromParcel(Parcel source)
        {
            Person person = new Person();
            person.name = source.readString();
            person.age = source.readInt();
            return person;
        }
        @Override
        public Person[] newArray(int size)
        {
            return new Person[size];
        }
    }
}
```

## 定制自己的日志工具

level等于VERBOSE就可以把所有日志都打印出来,level等于NOTHING就可以把所有日志屏蔽掉

```java
public class LogUtil
{
    public static final int VERBOSE = 1;
    public static final int DEBUG = 2;
    public static final int INFO = 3;
    public static final int WARN = 4;
    public static final int ERROR = 5;
    public static final int NOTHING = 6;
    public static int level = VERBOSE;
    public static void v(String tag, String msg)
    {
        if(level <= VERBOSE)
        {
            Log.v(tag,msg);
        }
    }
    public static void d(String tag, String msg)
    {
        if(level <= DEBUG)
        {
            Log.v(tag,msg);
        }
    }
    public static void i(String tag, String msg)
    {
        if(level <= INFO)
        {
            Log.v(tag,msg);
        }
    }
    public static void w(String tag, String msg)
    {
        if(level <= WARN)
        {
            Log.v(tag,msg);
        }
    }
    public static void e(String tag, String msg)
    {
        if(level <= ERROR)
        {
            Log.v(tag,msg);
        }
    }
}
```

## 动态调试

使用 Attach debugger to Android process 调试方式更加高效 更常用

## 创建定时任务

### Alarm 机制

```java
public class LongRunningService extends Service
{
    @Override
    public IBinder onBind(Intent intent)
    {
        return null;
    }

    @Override
    public int onStart(Intent intent, int flags, int startId)
    {
        new Thread(new Runnable()
        {
            @Override
            public void run()
            {
                // 具体逻辑
            }
        }).start();
        AlarmManager manager = (AlarmManager) getSystemService(ALARM_SERVICE);
        int anHour = 60 * 60 * 1000;
        long triggerAtTime = SystemClock.elapsedRealtime() + anHour;
        Intent i = new Intent(this,LongRunndingService.class);
        PendingIntent pi = PendingIntent.getService(this,0,i,0);
        manager.set(AlarmManager.ELAPSED_REALTIME_WAKEUP,triggerAtTime,pi);
        return super.onStartCommand(intent,flags,startId);
    }
}
```

### Doze 模式

- setAndAllowWhileIdle()

- setExactAndAllowWhileIdle()

## 多窗口模式编程

### 禁用方式: <application android: resizeableActivity = "false"

### targetSdkVersion低于24 禁用方式: <activity android: screenOrientation = "portrait"

## Lambda 表达式

### gradle 配置

```gradle
defaultConfig
{
    ...
    jackOptions.enable = true;
}
compileOptions
{
    sourceCompatibility JavaVersion.VERSION_1_8
    targetCompatibility JavaVersion.VERSION_1_8
}
```

对比

```java
// 不使用
new Thread(new Runnable()
{
    @Override
    public void run()
    {
        // 具体逻辑
    }
}).start();

// 使用
new Thread(()->{
    // 具体逻辑
}).start();
```