---
title: Android 组件化之 Application
date: 2022-11-05 13:32:39
categories: ['4.技能', 'Android']
tags: ['srcard', 'android', '组件化']
---

> 原文地址 [juejin.cn](https://juejin.cn/post/6844904031668666376)

[Android 组件化基础](../31328d4069eb994074352d0845812ad4dc9e3672)中笼统的总结了一下组件化开发的一些基础性问题，本篇文章继续组件化的学习，主要分如下三个方面介绍组件化中的 Application 如下：

1.  Application 的作用
2.  合并 Application
3.  动态配置 Application
  
  
## Application 的作用

Androuid 应用的启动的时候最先启动的就是 Application，每个 App 运行时仅创建唯一一个 Application，其生命周期就是 App 的生命周期，Application 中常用的回调方法如下：
  
-  **onCreate**：创建应用程序时回调，回调时机早于任何 Activity。
-  onTerminate：终止应用程序时调用，不能保证一定会被调用。
-  **onLowmemory**：当后台应用程序终止，但前台用用程序内存还不够时调用该方法，可在该方法中释放一些不必要的资源来应对这种情况。
-  **onConfigurationChanged**：配置发生变化时回调该方法，如手机屏幕旋转等
-  **onTrimMemory**：通知应用的不同内存情况，下面内存级别说明来自
  
其中附上一张来自 **Carson_Ho** 总结的 onTrimMemory 相关内存级别的说明如下：
  
![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6ca6608854f0460db834516d90f20b00~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)
<!--SR:!2022-11-16,8,250-->

Application 作为整个 App 的一个单例对象，其作用如下：
  
1.  作为 App 的入口，可用来<mark style="background: #fefe00A6;">初始化</mark> 基本配置，如第三方 SDK 的初始化。
2.  可以在 Application 中定义供<mark style="background: #fefe00A6;">全局使用的变量</mark> ，不过当应用被强杀之后有可能出现空指针的问题，导致再次打开应用的时候崩溃，如果确定要这样使用，一定要处理好这种情况。
3.  可以借助 Application <mark style="background: #fefe00A6;">管理 Activity 的生命周期</mark> 状态以及判断应用处于前台还是后台等，可根据内存优先级<mark style="background: #fefe00A6;">降低自身应用所占内存</mark> ，减小自身应用被系统强杀的可能性。
<!--SR:!2022-11-16,8,250-->
  
  
## 合并 Application

AndroidManifest 是每个 Module 的声明配置文件，对应的在生成一个 App 的时候也应该对应一份 AndroidManifest 文件，那么在多个 Module 彼此依赖的情况下就需要合并子 Module 的 AndroidManifest 文件内容到主 Module 的 AndroidManifest 文件中，最终会在 build 目录下 生成最终的 AndroidManifest 文件，编译生成的 AndroidManifest 文件的具体路径参考如下：

```java
app\build\intermediates\manifests\full\debug\AndroidManifest.xml

```

在合并子 Modulen 的 AndroidManifest 文件时，编译器会补全 use-sdk 的信息以及一些未设置的属性，在合并后如 Activity 等组件中的 name 属性都以包名 + 文件名来指定。

其中在合并 AndroidManifest 文件要对 Application 进行合并， Application 合并规则如下：

1.  如果子 Module 中有自定义的 Application，主 Module 中没有自定义 Application，则会将子 Module 中的 Application 合并到最终的 AndroidManifest 文件中。
2.  如果主 Module 有自定义 Application，子 Module 没有自定义的 Application，则会在最终合并的 AndroidManifest 文件中使用主 Module 中的 Application。
3.  如果多个子 Module 中都自定义了 Application，在解决冲突后则会在最终合并的 AndroidManifest 文件中使用最后编译的 Module 中的 Application。
4.  如果主 Module 中有自定义的 Application，子 Module 中也有自定义的 Application，此时会提示要在主 Module 的 AndroidManifest 文件中添加 tools:replace 属性，编译完成之后，合并后的 AndroidManifest 文件使用的是主 Module 中自定义的 Application。

在合并过程中如果不添加 tools:replace 属性，则会提示添加 tools:android 属性，提示的错误信息如下：
  
```java
Manifest merger failed : Attribute application@name value=(com.manu.module_one.OneApplication) from [:moduel_one] AndroidManifest.xml:13:9-58
	is also present at [:module_two] AndroidManifest.xml:13:9-58 value=(com.manu.module_two.TwoApplication).
	Suggestion: add 'tools:replace="android:name"' to <application> element at AndroidManifest.xml:6:5-21:19 to override.

```
  
比如这里就要在==子 Module 中==的 AndroidManifest 文件的 application 标签下添加 tools:replace 属性：
  
```java
tools:replace="android:name"

```
<!--SR:!2022-11-18,10,250-->
  
  
## 动态配置 Application

除了 Application 需要合并之外，在组件化过程中各个 Module 的初始化也非常重要，可以使用反射完成各个 Module 的初始化，就是在主 Module 中反射获取子 Module 的初始化对象，然后调用其初始化方法，为了方便定义一个类管理子 Module 的初始化类，参考如下：

```java

public class ModuleConfig {
    private static final String moduleOneInit = "com.manu.module_one.ModuleOneAppInit";
    private static final String moduleTwoInit = "com.manu.module_two.ModuleTwoAppInit";
    public static String[] moduleInits = {
            moduleOneInit,
            moduleTwoInit
    };
}

```

创建一个初始化的基类接口如下：

```java


public interface BaseAppInit {
    /**
     * 高优先级被初始化
     * @param application
     * @return
     */
    boolean onInitHighPriority(Application application);

    /**
     * 低优先级被初始化
     * @param application
     * @return
     */
    boolean onInitLowPriority(Application application);
}

```

为了使得每个子 Module 都能方便使用该初始化基类，应将其放在基类 Module 中，因为基类被所有的 Module 所依赖，然后在每个字 Module 中继承 BaseAppInit 实现自己 Module 的初始化类，参考如下：

```java


public class ModuleOneAppInit implements BaseAppInit {
    private static final String TAG = ModuleOneAppInit.class.getSimpleName();

    @Override
    public boolean onInitHighPriority(Application application) {
        Log.i(TAG, "ModuleOneAppInit---onInitHighPriority");
        return true;
    }

    @Override
    public boolean onInitLowPriority(Application application) {
        Log.i(TAG, "ModuleOneAppInit---onInitLowPriority");
        return true;
    }
}

```

最后在主 Module 的自定义的 Application 中通过反射创建各个子 Module 的初始化类对象，并调用其初始化方法，参考如下：

```java
/**
 * 高优先级初始化
 */
private void initModuleHighPriority(){
    for (String init: ModuleConfig.moduleInits){
        try {
            Class<?> clazz = Class.forName(init);
            BaseAppInit appInit = (BaseAppInit) clazz.newInstance();
            appInit.onInitHighPriority(this);
        } catch (ClassNotFoundException | IllegalAccessException | InstantiationException e) {
            e.printStackTrace();
        }
    }
}

/**
 * 低优先级初始化
 */
private void initModuleLowPriority(){
    for (String init: ModuleConfig.moduleInits){
        try {
            Class<?> clazz = Class.forName(init);
            BaseAppInit appInit = (BaseAppInit) clazz.newInstance();
            appInit.onInitLowPriority(this);
        } catch (ClassNotFoundException | IllegalAccessException | InstantiationException e) {
            e.printStackTrace();
        }
    }
}

```

运行日志如下：

```java
ModuleOneAppInit---onInitHighPriority 
ModuleTwoAppInit---onInitHighPriority
ModuleOneAppInit---onInitLowPriority
ModuleTwoAppInit---onInitLowPriority

```

此外，还可以在基类 Module 中创建初始化基类和 BaseApplication，然后在 BaseApplication 中反射调用调用具体的初始化方法，归根结底还是使用反射，只是另一种实现方式，首先在基类 moddule 中创建 BaseAppInit 如下：

```java

public abstract class BaseAppInit {

    private Application mApplication;

    public BaseAppInit() {
    }

    public void setApplication(@NonNull Application application) {
        this.mApplication = application;
    }

    public void onCreate(){}

    public void OnTerminate(){}

    public void onLowMemory(){}

    public void configurationChanged(Configuration configuration){}
}

```

在基类 Module 中创建 BaseApplication 如下：

```java


public abstract class BaseApplication extends Application {

    private List<Class<? extends BaseAppInit>> classInitList = new ArrayList<>();
    private List<BaseAppInit> appInitList = new ArrayList<>();

    @Override
    public void onCreate() {
        super.onCreate();
        appInit();
        initCreate();
    }

    protected abstract void appInit();

    protected void registerApplicationInit(Class<? extends BaseAppInit> classInit) {
        classInitList.add(classInit);
    }

    private void initCreate() {
        for (Class<? extends BaseAppInit> classInit : classInitList) {
            try {
                BaseAppInit appInit = classInit.newInstance();
                appInitList.add(appInit);
                appInit.onCreate();

            } catch (InstantiationException | IllegalAccessException e) {
                e.printStackTrace();
            }
        }
    }

    @Override
    public void onTerminate() {
        super.onTerminate();
        for (BaseAppInit appInit : appInitList) {
            appInit.OnTerminate();
        }
    }

    @Override
    public void onLowMemory() {
        super.onLowMemory();
        for (BaseAppInit appInit : appInitList) {
            appInit.onLowMemory();
        }
    }

    @Override
    public void onConfigurationChanged(Configuration newConfig) {
        super.onConfigurationChanged(newConfig);
        for (BaseAppInit appInit : appInitList) {
            appInit.configurationChanged(newConfig);
        }
    }
}

```

然后在子 Module 中实现具体的初始化类，参考如下：

```java


public class ModuleThreeAppInit extends BaseAppInit {
    private static final String TAG = ModuleThreeAppInit.class.getSimpleName();

    @Override
    public void onCreate() {
        Log.i(TAG, "ModuleThreeAppInit---onCreate");
    }
}

```

最后，在主 Module 中继承 BaseApplication 实现自定义的 Application，并注册每个字 Module 的初始化文件，参考如下：

```java


public class MApplication extends BaseApplication{
    @Override
    protected void appInit() {
        registerApplicationInit(ModuleThreeAppInit.class);
        registerApplicationInit(ModuleForeAppInit.class);
    }
}

```

运行日志如下：

```java
ModuleThreeAppInit---onCreate
ModuleForeAppInit---onCreate

```

如上两种方式都是使用了反射，反射在解耦的同时，也在一定程度上降低了应用的性能，当然组件化的目的就是要让各个组件或各个 Module 之间尽可能的解耦，如果牺牲一点儿性能，能够获取解耦的最大化也是可以接受的。


**Backlinks:**

- [Android 组件化之 ARouter](../8dc50973dc5ce0ffd2d6b51296243e2cfef87063)

{% pullquote mindmap mindmap-md %}
- Android 组件化之 Application
  - [Android 组件化基础](../31328d4069eb994074352d0845812ad4dc9e3672)
  - [Android 组件化之 ARouter](../8dc50973dc5ce0ffd2d6b51296243e2cfef87063)
{% endpullquote %}