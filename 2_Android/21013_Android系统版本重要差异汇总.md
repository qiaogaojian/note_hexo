# Android系统版本重要差异汇总

## 一、Android 5.x

- Material design

    Material design算是Android 系统风格的里程碑，其3D UI风格新颖，贴近人机交互；
- 改善通知栏，提升可视化、亲近性、可编辑性。同时支持手机在锁屏状态也可接收到通知，用户可以在锁屏状态下，设置接收全部应用的通知或者接收部分应用的通知或者不接收所有应用的通知；

- 系统由以往的Dalvik模式改为采用ART（Android Runtime）模式，实现ahead-of-time (AOT)静态编译与just-in-time (JIT)动态编译交互进行；

- V7中引入CardView和RecycleView等新控件；

- 支持64位系统；

## 二、Android 6.x

- 新增运行时权限概念
    Android6.0或以上版本，用户可以完全控制应用权限。

    最佳实践是使用tedpermission插件

    ``` gradle
    compile 'gun0912.ted:tedpermission:2.2.2'
    ```

    代码示例

    ``` java
    PermissionListener permissionlistener = new PermissionListener()
    {
        @Override
        public void onPermissionGranted()
        {
            postUpdateUrl();
        }

        @Override
        public void onPermissionDenied(List<String> deniedPermissions)
        {
            dismissProgressDialog();
            finish();
            System.exit(0);
        }
    };

    TedPermission.with(this)
                    .setPermissionListener(permissionlistener)
                    .setDeniedMessage("如果拒绝权限，则不能使用消息推送服务\n\n请点击 [设置] > [权限] > [存储]")
                    .setPermissions(Manifest.permission.READ_EXTERNAL_STORAGE, Manifest.permission.WRITE_EXTERNAL_STORAGE)
                    .setDeniedCloseButtonText("关闭")
                    .setGotoSettingButtonText("设置")
                    .check();
    ```

    当用户安装一个app时，系统默认给app授权部分基础权限，其他敏感权限，需要开发者自己注意，当涉及敏感权限时，开发者需要手动请求系统授予权限，系统这时会弹框给用户，倘若用户拒绝，如果没有保护，app将直接崩溃，倘若有保护，app也无法使用相关功能。

- 新增瞌睡模式和待机模式

    瞌睡模式：当不碰手机，手机自动关闭屏幕后，过一会，手机将进入瞌睡模式。在瞌睡模式下，设备只会定期的唤醒，然后继续执行等待中的任务接着又进入瞌睡；

    待机模式：假如用户一段时间不触碰手机，设备将进入待机模式。在这个模式下，系统会认为所有app是闲置的，这时系统会关闭网络，并且暂停app之前正在执行的任务。

- 移除对Apache HTTP client的支持
    建议使用HttpURLConnection。如果还是想用Apache HTTP client，那么需要在build.gradle中添加

    ```gradle
    android
    {
        useLibrary 'org.apache.http.legacy'
    }
    ```

## 三、Android 7.x

- FileProvider

    intent不允许包名之外的文件Uri,需要使用File Provider

    ```java
    File file = new File(Environment.getExternalStorageDirectory(), m_appNameStr);
    Uri  uri  = FileProvider.getUriForFile(LoadingActivity.this, "com.sdbean.recommendstock.Tool.GenericFileProvider", file);
    ```

- 引入全新的JIT编译器，使得App安装速度快了75%，编译代码的规模减少了50%

- 安全：更安全的加密模式，可以对单独的文件进行加密，android系统启动加密

## 四、Android O（安卓8.0）

- Notification需要设置channel

    ``` java
    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O)
    {
        CharSequence name       = "My New Channel";
        @SuppressLint("WrongConstant") NotificationChannel channel = new NotificationChannel(CHANNEL_ID, name, android.app.NotificationManager.IMPORTANCE_HIGH);
        mNotifyMgr.createNotificationChannel(channel);
        Notification notification = new Notification.Builder(context)
                .setSmallIcon(R.drawable.xg_push_icon)
                .setContentTitle(message.getTitle())
                .setContentText(message.getExtra().get("alert"))
                .setContentIntent(pIntent)
                .setChannelId(CHANNEL_ID)
                .setAutoCancel(true)
                .build();
        mNotifyMgr.notify(message.getNotifyId(), notification);
    } else
    {
        Notification notification = new Notification.Builder(context)
                .setSmallIcon(R.drawable.xg_push_icon)
                .setContentTitle(message.getTitle())
                .setContentText(message.getExtra().get("alert"))
                .setContentIntent(pIntent)
                .setAutoCancel(true)
                .build();
        mNotifyMgr.notify(message.getNotifyId(), notification);
    }
    ```

- 后台进程限制

    谷歌表示一直在优化安卓Android的后台应用限制策略，以最大程度减小后台应用对电池的消耗和对资源的占用。在Android O的更新中，当应用被置入后台后，Android O将自动智能限制后台应用活动，主要会限制应用的广播、后台运行和位置，但应用的整体进程并没有被杀掉。不过，部分层级比较重要的应用可以不受限制，但总的来说，Android O将严格限制后台进程对手机资源的调用。

## 参考链接

[Sharing files through Intents: are you ready for Nougat?](https://proandroiddev.com/sharing-files-though-intents-are-you-ready-for-nougat-70f7e9294a0b)

[android各个版本的新特性](https://www.jianshu.com/p/88409d6f5795)