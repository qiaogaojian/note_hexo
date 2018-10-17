# Android检测虚拟机的方法

安卓模拟器检测的方法有很多,一些简单的信息能通过软件伪造,但是全方位多维度的伪造就相当难了,这里集合了几乎所有的安卓模拟器检测方法,为了测试检测效果,我下了 **7** 款市面常见的模拟器进行测试,经过测试,所有的模拟器都能被正确识别. 通过 Leakcanary 测试,没有发现内存泄漏问题.

## 使用方法

把 EmulatorDetector 代码文件放入项目工具代码目录,代码如下:

```java
//简单检测 用到了3种核心的检测方法
EmulatorDetector.with(this)
        .detectSimple(new EmulatorDetector.OnEmulatorDetectorListener()
        {
            @Override
            public void onResult(final boolean isEmulator)
            {
                runOnUiThread(new Runnable()
                {
                    @Override
                    public void run()
                    {
                        if (isEmulator)
                        {
                            textTitle.setText("This device is emulator");
                        } else
                        {
                            textTitle.setText("This device is not emulator");
                        }
                    }
                });
            }
        });
// 全面检测 用到了所有的检测方法
EmulatorDetector.with(this)
        .setDebug(false)
        .setCheckQumeProps(false)  // 该属性控制的 getProps() 用到了反射 相对耗时 默认关闭
        .detect(new EmulatorDetector.OnEmulatorDetectorListener()
        {
            @Override
            public void onResult(final boolean isEmulator)
            {
                runOnUiThread(new Runnable()
                {
                    @Override
                    public void run()
                    {
                        if (isEmulator)
                        {
                            textTitle.setText("This device is emulator");
                            textView.setText(EmulatorDetector.getDeviceInfo());
                        } else
                        {
                            textTitle.setText("This device is not emulator");
                            textView.setText(EmulatorDetector.getDeviceInfo());
                        }
                    }
                });
            }
        });
```

## 性能测试

### 运行时间

    ~= 0.2秒

### CPU

![image.png](https://upload-images.jianshu.io/upload_images/3947109-8ec857b457dc70b4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 内存

![image.png](https://upload-images.jianshu.io/upload_images/3947109-85bf45b53924430b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 兼容性测试预览

### 模拟器

#### Android自带模拟器

![image.png](https://upload-images.jianshu.io/upload_images/3947109-cdf5ca5e36734544.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### mumu模拟器

![image.png](https://upload-images.jianshu.io/upload_images/3947109-d353fef34927df10.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 夜神模拟器

![image.png](https://upload-images.jianshu.io/upload_images/3947109-b689be6022e3fee9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 天天模拟器

![tiantian](https://upload-images.jianshu.io/upload_images/3947109-1b1e909e648cfc78.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 逍遥模拟器

![image.png](https://upload-images.jianshu.io/upload_images/3947109-25aa71c0259135cc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### Blue Stack

![blue stacks.png](https://upload-images.jianshu.io/upload_images/3947109-e80189639f8eb5c8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 海马玩

![image.png](https://upload-images.jianshu.io/upload_images/3947109-72d646519b39f1dc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 非模拟器

#### oneplus

![image.png](https://upload-images.jianshu.io/upload_images/3947109-998900e5742d5461.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### Horner

![Horner](https://upload-images.jianshu.io/upload_images/3947109-ce1095ae2488b2e1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 检测模拟器的方法

### 检测设备信息

Build 类用来从系统属性中提取设备硬件和版本信息 通过分析这些信息和市面已有模拟器的信息进行对比 就可以做出初步判断

```java
private boolean checkDeviceInfo()
{
    boolean result = Build.FINGERPRINT.startsWith("generic")     // 唯一识别码
            || Build.MODEL.contains("google_sdk")                // 版本 用户最终可以见的名称
            || Build.MODEL.toLowerCase().contains("droid4x")
            || Build.MODEL.contains("Emulator")
            || Build.MODEL.contains("Android SDK built for x86")
            || Build.MANUFACTURER.contains("Genymotion")         // 硬件制造商
            || Build.HARDWARE.equals("goldfish")                 // 硬件名称
            || Build.HARDWARE.equals("vbox86")
            || Build.PRODUCT.equals("sdk")                       // 整个产品的名称
            || Build.PRODUCT.equals("google_sdk")
            || Build.PRODUCT.equals("sdk_x86")
            || Build.PRODUCT.equals("vbox86p")
            || Build.BOARD.toLowerCase().contains("nox")         // 主板
            || Build.BOOTLOADER.toLowerCase().contains("nox")    // 系统启动程序版本号
            || Build.HARDWARE.toLowerCase().contains("nox")
            || Build.PRODUCT.toLowerCase().contains("nox")
            || Build.SERIAL.toLowerCase().contains("nox");       // 硬件序列号

    if (result) return true;
    result |= Build.BRAND.startsWith("generic") && Build.DEVICE.startsWith("generic");
    if (result) return true;
    result |= "google_sdk".equals(Build.PRODUCT);
    return result;
}
```

### 检测包名

根据常见模拟器包名 通过 PackageManager.getLaunchIntentForPackage(包名) 判断Intent能否被解析 从而判断当时是不是模拟器环境

```java
private boolean checkPackageName()
{
    if (!isCheckPackage || mListPackageName.isEmpty())
    {
        return false;
    }
    final PackageManager packageManager = mContext.getPackageManager();
    for (final String pkgName : mListPackageName)
    {
        final Intent tryIntent = packageManager.getLaunchIntentForPackage(pkgName);
        if (tryIntent != null)
        {
            final List<ResolveInfo> resolveInfos = packageManager.queryIntentActivities(tryIntent, PackageManager.MATCH_DEFAULT_ONLY);
            if (!resolveInfos.isEmpty())
            {
                return true;
            }
        }
    }
    return false;
}
```

### 检测移动网络运营商

虚拟机的手机运营商一般都是 android

```java
private boolean checkOperatorNameAndroid()
{
    if (ContextCompat.checkSelfPermission(mContext, Manifest.permission.READ_PHONE_STATE)
            == PackageManager.PERMISSION_GRANTED && isSupportTelePhony())
    {
        TelephonyManager telephonyManager = (TelephonyManager) mContext.getSystemService(Context.TELEPHONY_SERVICE);
        String           operatorName     = telephonyManager.getNetworkOperatorName();
        if (operatorName.equalsIgnoreCase("android"))
        {
            log("Check operator name android is detected");
            return true;
        }
        return false;
    }
    return false;
}
```

### 检测模拟器上特有文件

```java
private boolean checkFiles(String[] targets, String type)
{
    for (String pipe : targets)
    {
        File qemu_file = new File(pipe);
        if (qemu_file.exists())
        {
            log("Check " + type + " is detected");
            return true;
        }
    }
    return false;
}
```

### 检测模拟器属性

通过尝试查询设备系统属性中是否包含模拟器属性 来检测模拟器环境

```java
private boolean checkQEmuProps()
{
    int found_props = 0;

    for (Property property : PROPERTIES)
    {
        String property_value = getProp(mContext, property.name);
        if ((property.seek_value == null) && (property_value != null))
        {
            found_props++;
        }
        if ((property.seek_value != null) && (property_value.contains(property.seek_value)))
        {
            found_props++;
        }

    }

    if (found_props >= MIN_PROPERTIES_THRESHOLD)
    {
        log("Check QEmuProps is detected");
        return true;
    }
    return false;
}
```

### 检测虚拟机驱动

读取驱动文件, 检查是否包含已知的qemu驱动

```java
private boolean checkQEmuDrivers()
{
    for (File drivers_file : new File[]{new File("/proc/tty/drivers"), new File("/proc/cpuinfo")})
    {
        if (drivers_file.exists() && drivers_file.canRead())
        {
            byte[] data = new byte[1024];
            try
            {
                InputStream is = new FileInputStream(drivers_file);
                is.read(data);
                is.close();
            } catch (Exception exception)
            {
                exception.printStackTrace();
            }

            String driver_data = new String(data);
            for (String known_qemu_driver : QEMU_DRIVERS)
            {
                if (driver_data.contains(known_qemu_driver))
                {
                    log("Check QEmuDrivers is detected");
                    return true;
                }
            }
        }
    }
    return false;
}
```

### 检测网卡IP

Android模拟器默认的地址是10.0.2.15

```java
private boolean checkIp()
{
    boolean ipDetected = false;
    if (ContextCompat.checkSelfPermission(mContext, Manifest.permission.INTERNET)
            == PackageManager.PERMISSION_GRANTED)
    {
        String[]      args          = {"/system/bin/netcfg"};
        StringBuilder stringBuilder = new StringBuilder();
        try
        {
            ProcessBuilder builder = new ProcessBuilder(args);
            builder.directory(new File("/system/bin/"));
            builder.redirectErrorStream(true);
            Process     process = builder.start();
            InputStream in      = process.getInputStream();
            byte[]      re      = new byte[1024];
            while (in.read(re) != -1)
            {
                stringBuilder.append(new String(re));
            }
            in.close();

        } catch (Exception ex)
        {
            log(ex.toString());
        }

        String netData = stringBuilder.toString();
        log("netcfg data -> " + netData);

        if (!TextUtils.isEmpty(netData))
        {
            String[] array = netData.split("\n");

            for (String lan : array)
            {
                if ((lan.contains("wlan0") || lan.contains("tunl0") || lan.contains("eth0"))
                        && lan.contains(IP))
                {
                    ipDetected = true;
                    log("Check IP is detected");
                    break;
                }
            }

        }
    }
    return ipDetected;
}
```

### 检测光传感器

由于光传感器模拟器不容易伪造 在这里判断设备是否存在光传感器来判断是否为模拟器

```java
public static Boolean checkLightSensorManager(Context context)
{
    SensorManager sensorManager = (SensorManager) context.getSystemService(SENSOR_SERVICE);
    Sensor        sensor8       = sensorManager.getDefaultSensor(Sensor.TYPE_LIGHT); //光
    if (null == sensor8)
    {
        return true;
    } else
    {
        return false;
    }
}
```

## Build 类获取系统信息

### Product: 产品名称

### Manufacturer: 硬件制造商

### Brand: 品牌

### Device: 设备名称

### Model: 系统版本

### Hardware: 硬件名称

### FingerPrint: 唯一识别码

```java
// 获取设备信息
public static String getDeviceInfo()
{
    return "\tBuild.PRODUCT: \t" + Build.PRODUCT + "\n\n" +
            "\tBuild.MANUFACTURER: \t" + Build.MANUFACTURER + "\n\n" +
            "\tBuild.BRAND: \t" + Build.BRAND + "\n\n" +
            "\tBuild.DEVICE: \t" + Build.DEVICE + "\n\n" +
            "\tBuild.MODEL: \t" + Build.MODEL + "\n\n" +
            "\tBuild.HARDWARE: \t" + Build.HARDWARE + "\n\n" +
            "\tBuild.FINGERPRINT: \t" + Build.FINGERPRINT;
}
```

## 代码示例

### 工具类

```java
package com.eta.detectemulator;

import android.Manifest;
import android.annotation.SuppressLint;
import android.content.Context;
import android.content.Intent;
import android.content.pm.PackageManager;
import android.content.pm.ResolveInfo;
import android.hardware.Sensor;
import android.hardware.SensorManager;
import android.os.Build;
import android.support.v4.content.ContextCompat;
import android.telephony.TelephonyManager;
import android.text.TextUtils;
import android.util.Log;

import java.io.File;
import java.io.FileInputStream;
import java.io.InputStream;
import java.lang.reflect.Method;
import java.util.ArrayList;
import java.util.List;

import static android.content.Context.SENSOR_SERVICE;

public final class EmulatorDetector
{

    public interface OnEmulatorDetectorListener
    {
        void onResult(boolean isEmulator);
    }

    // Genymotion 模拟器特征文件
    private static final String[] GENY_FILES = {
            "/dev/socket/genyd",
            "/dev/socket/baseband_genyd"
    };
    // Andy 模拟器特征文件
    private static final String[] ANDY_FILES = {
            "fstab.andy",
            "ueventd.andy.rc"
    };
    //夜神模拟器特征文件
    private static final String[] NOX_FILES  = {
            "fstab.nox",
            "init.nox.rc",
            "ueventd.nox.rc"
    };
    // 模拟器特有文件
    private static final String[] PIPES      = {
            "/dev/socket/qemud",
            "/dev/qemu_pipe"
    };

    // Qemu模拟器环境驱动
    private static final String[] QEMU_DRIVERS = {"goldfish"};

    // 手机一般都是 arm 架构 如果存在 x86 相关文件 则是虚拟机
    private static final String[] X86_FILES = {
            "ueventd.android_x86.rc",
            "x86.prop",
            "ueventd.ttVM_x86.rc",
            "init.ttVM_x86.rc",
            "fstab.ttVM_x86",
            "fstab.vbox86",
            "init.vbox86.rc",
            "ueventd.vbox86.rc"
    };

    // 模拟器已知属性 格式为 [属性名,属性值] 用于校验当前是否为模拟器环境
    private static final Property[] PROPERTIES = {
            new Property("init.svc.qemud", null),
            new Property("init.svc.qemu-props", null),
            new Property("qemu.hw.mainkeys", null),
            new Property("qemu.sf.fake_camera", null),
            new Property("qemu.sf.lcd_density", null),
            new Property("ro.bootloader", "unknown"),
            new Property("ro.bootmode", "unknown"),
            new Property("ro.hardware", "goldfish"),
            new Property("ro.kernel.android.qemud", null),
            new Property("ro.kernel.qemu.gles", null),
            new Property("ro.kernel.qemu", "1"),
            new Property("ro.product.device", "generic"),
            new Property("ro.product.model", "sdk"),
            new Property("ro.product.name", "sdk"),
            new Property("ro.serialno", null)
    };

    // 模拟器默认 IP 地址
    private static final String IP = "10.0.2.15";

    // 一个阈值，因为所谓“已知”的模拟器属性并不完全准确，因此保持一定的阈值能让检测效果更好
    private static final int MIN_PROPERTIES_THRESHOLD = 0x5;

    private final Context      mContext;
    private       boolean      isDebug          = false;  // 是否开启 Debug
    private       boolean      isCheckPackage   = true;   // 是否检测包名
    private       boolean      isCheckQemuProps = false; // 是否检测Qume属性
    private       List<String> mListPackageName = new ArrayList<>();

    @SuppressLint("StaticFieldLeak")
    private static EmulatorDetector mEmulatorDetector;

    public static EmulatorDetector with(Context pContext)
    {
        if (pContext == null)
        {
            throw new IllegalArgumentException("Context 不能为空.");
        }
        if (mEmulatorDetector == null)
            mEmulatorDetector = new EmulatorDetector(pContext.getApplicationContext());
        return mEmulatorDetector;
    }

    private EmulatorDetector(Context pContext)
    {
        mContext = pContext;
        mListPackageName.add("com.google.android.launcher.layouts.genymotion");
        mListPackageName.add("com.bluestacks");
        mListPackageName.add("com.bignox.app");
    }

    public EmulatorDetector setCheckPackage(boolean chkPackage)
    {
        this.isCheckPackage = chkPackage;
        return this;
    }

    public EmulatorDetector addPackageName(String pPackageName)
    {
        this.mListPackageName.add(pPackageName);
        return this;
    }

    public EmulatorDetector addPackageName(List<String> pListPackageName)
    {
        this.mListPackageName.addAll(pListPackageName);
        return this;
    }

    public List<String> getPackageNameList()
    {
        return this.mListPackageName;
    }

    public EmulatorDetector setDebug(boolean isDebug)
    {
        this.isDebug = isDebug;
        return this;
    }

    public boolean isDebug()
    {
        return isDebug;
    }

    public EmulatorDetector setCheckQumeProps(boolean checkProps)
    {
        this.isCheckQemuProps = checkProps;
        return this;
    }

    public boolean isCheckQemuProps()
    {
        return isCheckQemuProps;
    }

    public void detect(final OnEmulatorDetectorListener pOnEmulatorDetectorListener)
    {
        new Thread(new Runnable()
        {
            @Override
            public void run()
            {
                boolean isEmulator = detect();

                log("This System is Emulator: " + isEmulator);
                if (pOnEmulatorDetectorListener != null)
                {
                    pOnEmulatorDetectorListener.onResult(isEmulator);
                }
            }
        }).start();
    }

    public void detectSimple(final OnEmulatorDetectorListener pOnEmulatorDetectorListener)
    {
        new Thread(new Runnable()
        {
            @Override
            public void run()
            {
                boolean isEmulator = detectSimple();

                log("This System is Emulator: " + isEmulator);
                if (pOnEmulatorDetectorListener != null)
                {
                    pOnEmulatorDetectorListener.onResult(isEmulator);
                }
            }
        }).start();
    }

    public boolean detectSimple()
    {
        boolean result = checkDeviceInfo()
                ||checkOperatorNameAndroid()
                ||checkLightSensorManager(mContext);

        return result;
    }

    private boolean detect()
    {
        boolean result = false;

        log(getDeviceInfo());

        result = checkDeviceInfo()
                ||checkOperatorNameAndroid()
                ||checkFiles(GENY_FILES, "Geny")
                ||checkFiles(ANDY_FILES, "Andy")
                ||checkFiles(NOX_FILES, "Nox")
                ||checkFiles(PIPES, "Pipes")
                ||checkQEmuDrivers()
                ||checkIp()
                ||checkLightSensorManager(mContext);

        if (!result)
        {
            if (isCheckQemuProps)
            {
                result = (checkQEmuProps() && checkFiles(X86_FILES, "X86"));
            }
            if (isCheckPackage)
            {
                result = checkPackageName();
            }
        }
        return result;
    }

    // 设备信息检测: Build 类用来从系统属性中提取设备硬件和版本信息
    // 通过分析这些信息和市面已有模拟器的信息进行对比 就可以做出初步判断
    private boolean checkDeviceInfo()
    {
        boolean result = Build.FINGERPRINT.startsWith("generic")     // 唯一识别码
                || Build.MODEL.contains("google_sdk")                // 版本 用户最终可以见的名称
                || Build.MODEL.toLowerCase().contains("droid4x")
                || Build.MODEL.contains("Emulator")
                || Build.MODEL.contains("Android SDK built for x86")
                || Build.MANUFACTURER.contains("Genymotion")         // 硬件制造商
                || Build.HARDWARE.equals("goldfish")                 // 硬件名称
                || Build.HARDWARE.equals("vbox86")
                || Build.PRODUCT.equals("sdk")                       // 整个产品的名称
                || Build.PRODUCT.equals("google_sdk")
                || Build.PRODUCT.equals("sdk_x86")
                || Build.PRODUCT.equals("vbox86p")
                || Build.BOARD.toLowerCase().contains("nox")         // 主板
                || Build.BOOTLOADER.toLowerCase().contains("nox")    // 系统启动程序版本号
                || Build.HARDWARE.toLowerCase().contains("nox")
                || Build.PRODUCT.toLowerCase().contains("nox")
                || Build.SERIAL.toLowerCase().contains("nox");       // 硬件序列号

        if (result) return true;
        result |= Build.BRAND.startsWith("generic") && Build.DEVICE.startsWith("generic");
        if (result) return true;
        result |= "google_sdk".equals(Build.PRODUCT);
        return result;
    }

    // 包名检测: 根据常见模拟器包名 通过 PackageManager.getLaunchIntentForPackage(包名)
    // 判断Intent能否被解析 从而判断当时是不是模拟器环境
    private boolean checkPackageName()
    {
        if (!isCheckPackage || mListPackageName.isEmpty())
        {
            return false;
        }
        final PackageManager packageManager = mContext.getPackageManager();
        for (final String pkgName : mListPackageName)
        {
            final Intent tryIntent = packageManager.getLaunchIntentForPackage(pkgName);
            if (tryIntent != null)
            {
                final List<ResolveInfo> resolveInfos = packageManager.queryIntentActivities(tryIntent, PackageManager.MATCH_DEFAULT_ONLY);
                if (!resolveInfos.isEmpty())
                {
                    return true;
                }
            }
        }
        return false;
    }

    // 检测移动网络运营商: 虚拟机的手机运营商一般都是 android
    private boolean checkOperatorNameAndroid()
    {
        if (ContextCompat.checkSelfPermission(mContext, Manifest.permission.READ_PHONE_STATE)
                == PackageManager.PERMISSION_GRANTED && isSupportTelePhony())
        {
            TelephonyManager telephonyManager = (TelephonyManager) mContext.getSystemService(Context.TELEPHONY_SERVICE);
            String           operatorName     = telephonyManager.getNetworkOperatorName();
            if (operatorName.equalsIgnoreCase("android"))
            {
                log("Check operator name android is detected");
                return true;
            }
            return false;
        }
        return false;
    }

    // 检测模拟器上特有文件
    private boolean checkFiles(String[] targets, String type)
    {
        for (String pipe : targets)
        {
            File qemu_file = new File(pipe);
            if (qemu_file.exists())
            {
                log("Check " + type + " is detected");
                return true;
            }
        }
        return false;
    }

    // 检测模拟器属性: 通过尝试查询设备系统属性中是否包含模拟器属性 来检测模拟器环境
    private boolean checkQEmuProps()
    {
        int found_props = 0;

        for (Property property : PROPERTIES)
        {
            String property_value = getProp(mContext, property.name);
            if ((property.seek_value == null) && (property_value != null))
            {
                found_props++;
            }
            if ((property.seek_value != null) && (property_value.contains(property.seek_value)))
            {
                found_props++;
            }

        }

        if (found_props >= MIN_PROPERTIES_THRESHOLD)
        {
            log("Check QEmuProps is detected");
            return true;
        }
        return false;
    }

    // 检测虚拟机驱动: 读取驱动文件, 检查是否包含已知的qemu驱动
    private boolean checkQEmuDrivers()
    {
        for (File drivers_file : new File[]{new File("/proc/tty/drivers"), new File("/proc/cpuinfo")})
        {
            if (drivers_file.exists() && drivers_file.canRead())
            {
                byte[] data = new byte[1024];
                try
                {
                    InputStream is = new FileInputStream(drivers_file);
                    is.read(data);
                    is.close();
                } catch (Exception exception)
                {
                    exception.printStackTrace();
                }

                String driver_data = new String(data);
                for (String known_qemu_driver : QEMU_DRIVERS)
                {
                    if (driver_data.contains(known_qemu_driver))
                    {
                        log("Check QEmuDrivers is detected");
                        return true;
                    }
                }
            }
        }
        return false;
    }

    // 检测网卡IP: Android模拟器默认的地址是10.0.2.15
    private boolean checkIp()
    {
        boolean ipDetected = false;
        if (ContextCompat.checkSelfPermission(mContext, Manifest.permission.INTERNET)
                == PackageManager.PERMISSION_GRANTED)
        {
            String[]      args          = {"/system/bin/netcfg"};
            StringBuilder stringBuilder = new StringBuilder();
            try
            {
                ProcessBuilder builder = new ProcessBuilder(args);
                builder.directory(new File("/system/bin/"));
                builder.redirectErrorStream(true);
                Process     process = builder.start();
                InputStream in      = process.getInputStream();
                byte[]      re      = new byte[1024];
                while (in.read(re) != -1)
                {
                    stringBuilder.append(new String(re));
                }
                in.close();

            } catch (Exception ex)
            {
                log(ex.toString());
            }

            String netData = stringBuilder.toString();
            log("netcfg data -> " + netData);

            if (!TextUtils.isEmpty(netData))
            {
                String[] array = netData.split("\n");

                for (String lan : array)
                {
                    if ((lan.contains("wlan0") || lan.contains("tunl0") || lan.contains("eth0"))
                            && lan.contains(IP))
                    {
                        ipDetected = true;
                        log("Check IP is detected");
                        break;
                    }
                }

            }
        }
        return ipDetected;
    }

    // 检测光传感器: 由于光传感器模拟器不容易伪造 在这里判断设备是否存在光传感器来判断是否为模拟器
    public static Boolean checkLightSensorManager(Context context)
    {
        SensorManager sensorManager = (SensorManager) context.getSystemService(SENSOR_SERVICE);
        Sensor        sensor8       = sensorManager.getDefaultSensor(Sensor.TYPE_LIGHT); //光
        if (null == sensor8)
        {
            return true;
        } else
        {
            return false;
        }
    }

    // 通过尝试查询指定的系统属性来检测模拟器环境 反射机制 谨慎使用
    private String getProp(Context context, String property)
    {
        try
        {
            ClassLoader classLoader      = context.getClassLoader();
            Class<?>    systemProperties = classLoader.loadClass("android.os.SystemProperties");

            Method get = systemProperties.getMethod("get", String.class);

            Object[] params = new Object[1];
            params[0] = property;

            return (String) get.invoke(systemProperties, params);
        } catch (Exception exception)
        {
            log(exception.toString());
        }
        return null;
    }

    // 检测系统是否支持 TelePhony
    private boolean isSupportTelePhony()
    {
        PackageManager packageManager = mContext.getPackageManager();
        boolean        isSupport      = packageManager.hasSystemFeature(PackageManager.FEATURE_TELEPHONY);
        log("Supported TelePhony: " + isSupport);
        return isSupport;
    }

    // 获取设备信息
    public static String getDeviceInfo()
    {
        return "\tBuild.PRODUCT: \t" + Build.PRODUCT + "\n\n" +
                "\tBuild.MANUFACTURER: \t" + Build.MANUFACTURER + "\n\n" +
                "\tBuild.BRAND: \t" + Build.BRAND + "\n\n" +
                "\tBuild.DEVICE: \t" + Build.DEVICE + "\n\n" +
                "\tBuild.MODEL: \t" + Build.MODEL + "\n\n" +
                "\tBuild.HARDWARE: \t" + Build.HARDWARE + "\n\n" +
                "\tBuild.FINGERPRINT: \t" + Build.FINGERPRINT;
    }

    // Log 类简单封装
    private void log(String str)
    {
        if (this.isDebug)
        {
            Log.d(getClass().getName(), str);
        }
    }
}

class Property
{
    public String name;
    public String seek_value;

    public Property(String name, String seek_value)
    {
        this.name = name;
        this.seek_value = seek_value;
    }
}
```

### 使用方法

```java
public class DetectActivity extends AppCompatActivity
{

    private TextView textTitle;
    private TextView textView;
    private Button   btnDetect2;

    @Override
    protected void onCreate(Bundle savedInstanceState)
    {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_detect);

        textTitle = (TextView) findViewById(R.id.textTitle1);
        textView = (TextView) findViewById(R.id.textView1);
        btnDetect2 = (Button) findViewById(R.id.button2);

        btnDetect2.setOnClickListener(new View.OnClickListener()
        {
            @Override
            public void onClick(View v)
            {
                detectEmulatorSimple();
            }
        });
        // 为确保准确性 detect() 是通过多维度的检测方法进行检测的
        // 考虑性能可以使用 detectSimple() 方法 经测试效果一致
        detectEmulatorSimple();
    }

    // 全面检测
    private void detectEmulator()
    {
        EmulatorDetector.with(this)
                .setDebug(false)
                .setCheckQumeProps(false)  // 该属性控制的 getProps() 用到了反射 相对耗时 默认关闭
                .detect(new EmulatorDetector.OnEmulatorDetectorListener()
                {
                    @Override
                    public void onResult(final boolean isEmulator)
                    {
                        runOnUiThread(new Runnable()
                        {
                            @Override
                            public void run()
                            {
                                if (isEmulator)
                                {
                                    textTitle.setText("This device is emulator");
                                    textView.setText(EmulatorDetector.getDeviceInfo());
                                } else
                                {
                                    textTitle.setText("This device is not emulator");
                                    textView.setText(EmulatorDetector.getDeviceInfo());
                                }
                            }
                        });
                    }
                });
    }
    // 简单检测
    private void detectEmulatorSimple()
    {
        EmulatorDetector.with(this)
                .detectSimple(new EmulatorDetector.OnEmulatorDetectorListener()
                {
                    @Override
                    public void onResult(final boolean isEmulator)
                    {
                        runOnUiThread(new Runnable()
                        {
                            @Override
                            public void run()
                            {
                                if (isEmulator)
                                {
                                    textTitle.setText("This device is emulator");
                                    textView.setText(EmulatorDetector.getDeviceInfo());
                                } else
                                {
                                    textTitle.setText("This device is not emulator");
                                    textView.setText(EmulatorDetector.getDeviceInfo());
                                }
                            }
                        });
                    }
                });
    }


    @Override
    protected void onDestroy()
    {
        super.onDestroy();
        textTitle = null;
        textView = null;
        btnDetect2 = null;
    }
}

```

## [Demo地址](https://gitee.com/qiaogaojian/android/tree/master/Demo/DetectEmulator)

## 参考链接

[How can I detect when an Android application is running in the emulator?
](https://stackoverflow.com/questions/2799097/how-can-i-detect-when-an-android-application-is-running-in-the-emulator)

[检测Android虚拟机的方法和代码实现](https://bbs.pediy.com/thread-225717.htm)

[Android判断模拟器还是真机的方法](https://blog.csdn.net/tianshuai4317618/article/details/78834683/)
