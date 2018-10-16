# Android检测虚拟机的方法

安卓模拟器检测的方法有很多,一些简单的信息能通过软件伪造,但是全方位多维度的伪造就相当难了,这里集合了几乎所有的安卓模拟器检测方法,为了测试检测效果,我下了 **7** 款市面常见的模拟器进行测试,经过测试,所有的模拟器都能被正确识别.

## 性能测试

### CPU

![image.png](https://upload-images.jianshu.io/upload_images/3947109-7f2c243be39c1d9b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

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

### 包名检测

### 默认电话

### 设备 Id

### 国际移动用户识别码 IMSI

### 光传感器

### 运营商名

### 虚拟操作系统模拟器 (QEMU) 属性

### QEMU驱动

### 网卡默认IP

## Build 类获取系统信息

### Product

### Manufacturer

### Brand

### Device

### Model

### Hardware

### FingerPrint

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

    private static final String[] PHONE_NUMBERS = {
            "15555215554", "15555215556", "15555215558", "15555215560", "15555215562", "15555215564",
            "15555215566", "15555215568", "15555215570", "15555215572", "15555215574", "15555215576",
            "15555215578", "15555215580", "15555215582", "15555215584"
    };

    private static final String[] DEVICE_IDS = {
            "000000000000000",
            "e21833235b6eef10",
            "012345678912345"
    };

    private static final String[] IMSI_IDS = {
            "310260000000000"
    };

    private static final String[] GENY_FILES = {
            "/dev/socket/genyd",
            "/dev/socket/baseband_genyd"
    };

    private static final String[] QEMU_DRIVERS = {"goldfish"};

    private static final String[] PIPES = {
            "/dev/socket/qemud",
            "/dev/qemu_pipe"
    };

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

    private static final String[] ANDY_FILES = {
            "fstab.andy",
            "ueventd.andy.rc"
    };

    private static final String[] NOX_FILES = {
            "fstab.nox",
            "init.nox.rc",
            "ueventd.nox.rc"
    };

    // 虚拟操作系统模拟器 QEMU 属性
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

    private static final String IP = "10.0.2.15";

    private static final int MIN_PROPERTIES_THRESHOLD = 0x5;

    private final Context      mContext;
    private       boolean      isDebug          = false;
    private       boolean      isTelephony      = false;
    private       boolean      isCheckPackage   = true;
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

    public EmulatorDetector setDebug(boolean isDebug)
    {
        this.isDebug = isDebug;
        return this;
    }

    public boolean isDebug()
    {
        return isDebug;
    }

    public boolean isCheckTelephony()
    {
        return isTelephony;
    }

    public boolean isCheckPackage()
    {
        return isCheckPackage;
    }

    public EmulatorDetector setCheckTelephony(boolean telephony)
    {
        this.isTelephony = telephony;
        return this;
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

    private boolean detect()
    {
        boolean result = false;

        log(getDeviceInfo());

        // 初步检测
        if (!result)
        {
            result = checkBasic();
            log("Check basic " + result);
        }

        // 深入检测
        if (!result)
        {
            result = checkAdvanced();
            log("Check Advanced " + result);
        }

        // 包名检测
        if (!result)
        {
            result = checkPackageName();
            log("Check Package Name " + result);
        }

        return result;
    }

    private boolean checkBasic()
    {
        boolean result = Build.FINGERPRINT.startsWith("generic")
                || Build.MODEL.contains("google_sdk")
                || Build.MODEL.toLowerCase().contains("droid4x")
                || Build.MODEL.contains("Emulator")
                || Build.MODEL.contains("Android SDK built for x86")
                || Build.MANUFACTURER.contains("Genymotion")
                || Build.HARDWARE.equals("goldfish")
                || Build.HARDWARE.equals("vbox86")
                || Build.PRODUCT.equals("sdk")
                || Build.PRODUCT.equals("google_sdk")
                || Build.PRODUCT.equals("sdk_x86")
                || Build.PRODUCT.equals("vbox86p")
                || Build.BOARD.toLowerCase().contains("nox")
                || Build.BOOTLOADER.toLowerCase().contains("nox")
                || Build.HARDWARE.toLowerCase().contains("nox")
                || Build.PRODUCT.toLowerCase().contains("nox")
                || Build.SERIAL.toLowerCase().contains("nox");

        if (result) return true;
        result |= Build.BRAND.startsWith("generic") && Build.DEVICE.startsWith("generic");
        if (result) return true;
        result |= "google_sdk".equals(Build.PRODUCT);
        return result;
    }

    private boolean checkAdvanced()
    {
        boolean result = checkTelephony()
                || checkFiles(GENY_FILES, "Geny")
                || checkFiles(ANDY_FILES, "Andy")
                || checkFiles(NOX_FILES, "Nox")
                || checkQEmuDrivers()
                || checkFiles(PIPES, "Pipes")
                || checkIp()
                || (checkQEmuProps() && checkFiles(X86_FILES, "X86"))
                || checkHasLightSensorManager(mContext);
        return result;
    }

    // 包名检测
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

    // 手机基础信息检测
    private boolean checkTelephony()
    {
        if (ContextCompat.checkSelfPermission(mContext, Manifest.permission.READ_PHONE_STATE)
                == PackageManager.PERMISSION_GRANTED && this.isTelephony && isSupportTelePhony())
        {
            return checkPhoneNumber()
                    || checkDeviceId()
                    || checkImsi()
                    || checkOperatorNameAndroid();
        }
        return false;
    }

    // 默认电话
    private boolean checkPhoneNumber()
    {
        TelephonyManager telephonyManager =
                (TelephonyManager) mContext.getSystemService(Context.TELEPHONY_SERVICE);

        @SuppressLint("HardwareIds") String phoneNumber = telephonyManager.getLine1Number();

        for (String number : PHONE_NUMBERS)
        {
            if (number.equalsIgnoreCase(phoneNumber))
            {
                log(" check phone number is detected");
                return true;
            }

        }
        return false;
    }

    // 设备id
    private boolean checkDeviceId()
    {
        TelephonyManager telephonyManager =
                (TelephonyManager) mContext.getSystemService(Context.TELEPHONY_SERVICE);

        @SuppressLint("HardwareIds") String deviceId = telephonyManager.getDeviceId();

        for (String known_deviceId : DEVICE_IDS)
        {
            if (known_deviceId.equalsIgnoreCase(deviceId))
            {
                log("Check device id is detected");
                return true;
            }

        }
        return false;
    }

    // Imsi
    private boolean checkImsi()
    {
        TelephonyManager telephonyManager =
                (TelephonyManager) mContext.getSystemService(Context.TELEPHONY_SERVICE);
        @SuppressLint("HardwareIds") String imsi = telephonyManager.getSubscriberId();

        for (String known_imsi : IMSI_IDS)
        {
            if (known_imsi.equalsIgnoreCase(imsi))
            {
                log("Check imsi is detected");
                return true;
            }
        }
        return false;
    }

    // 判断是否存在光传感器来判断是否为模拟器
    public static Boolean checkHasLightSensorManager(Context context)
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

    // 检测运营商
    private boolean checkOperatorNameAndroid()
    {
        String operatorName = ((TelephonyManager)
                mContext.getSystemService(Context.TELEPHONY_SERVICE)).getNetworkOperatorName();
        if (operatorName.equalsIgnoreCase("android"))
        {
            log("Check operator name android is detected");
            return true;
        }
        return false;
    }


    // 检测已知虚拟操作系统模拟器 (QEMU) 的驱动程序的列表
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

    // 检测模拟器上特有的几个文件
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

    // 检测虚拟操作系统模拟器 (QEMU) 属性
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
            if ((property.seek_value != null)
                    && (property_value.contains(property.seek_value)))
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

    // 检测网卡IP
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

                for (String lan :
                        array)
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

    private boolean isSupportTelePhony()
    {
        PackageManager packageManager = mContext.getPackageManager();
        boolean        isSupport      = packageManager.hasSystemFeature(PackageManager.FEATURE_TELEPHONY);
        log("Supported TelePhony: " + isSupport);
        return isSupport;
    }

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
public class MainActivity extends AppCompatActivity
{
    private TextView textTitle;
    private TextView textView;
    @Override
    protected void onCreate(Bundle savedInstanceState)
    {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        textTitle = (TextView) findViewById(R.id.textTitle);
        textView = (TextView) findViewById(R.id.textView);

        detectEmulator();
    }

    private void detectEmulator()
    {
        EmulatorDetector.with(this)
                .setDebug(true)
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
}

```

## 参考链接

[How can I detect when an Android application is running in the emulator?
](https://stackoverflow.com/questions/2799097/how-can-i-detect-when-an-android-application-is-running-in-the-emulator)

[检测Android虚拟机的方法和代码实现](https://bbs.pediy.com/thread-225717.htm)