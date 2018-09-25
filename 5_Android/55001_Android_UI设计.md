# Android设计

## UI控件

### 文本类

#### TextView

#### EditText

#### AutoCompleteTextView

### 按钮类

#### Button

#### CheckBox

#### RadioButton

#### ToggleButton

#### ZoomButton

### 图片类

#### ImageView

### 时间控件

#### Datepicker

#### DigitalClock

#### AnalogClock

### 进度显示

#### ProcessBar

#### SeekBar

#### AbsSeekBar

### 导航

#### TabHost

#### TabWidget

## UI布局

### LinearLayout

#### 1. 方向

- 属性

  ``` xml-dtd
  android:orientation="horizontal"     //水平
  android:orientation="vertical"       //垂直
  ```

- 实例

  - 水平

    ```xml-dtd
    android:orientation="horizontal"
    ```

    ​

    ![水平](https://upload-images.jianshu.io/upload_images/5516419-eae5a492683a65c3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/473/format/webp)

  - 垂直

    ```xml-dtd
    android:orientation="vertical"
    ```

    ![垂直](https://upload-images.jianshu.io/upload_images/5516419-b4b547ea9c90deb0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/474/format/webp)

#### 2. 对齐方式

- 属性

  ``` xml-dtd
  android:gravity          //本元素的子元素 相对它的对齐方式
  android:layout_gravity   //本元素相对它的父元素的对齐方式  对于其他属性 有layout_前缀的 原则一样

  //常用属性值
  android:gravity="center_horizontal"         //子控件水平方向居中
  android:gravity="center_vertical"           //子控件竖直方向居中
  android:gravity="center"                    //子控件竖直方向和水平方向居中
  android:gravity= start || end || top || bottom //子控件左对齐 || 右对齐 || 顶部对齐 || 底部对齐
  android:gravity= left || right               //子控件左对齐 || 右对齐
  ```

  ​

- 实例

  - 第一个子空间设置水平垂直

  ```xml-dtd
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

  ```xml-dtd
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

#### 3. 子控件大小

- 属性

  ```xml-dtd
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

  ```xml-dtd
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

### RelativeLayout

#### 1. 属性值为true 或 false

- 属性

  ```xml-dtd
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

  ```xml-dtd
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

#### 2. 属性值为id

- 属性

  ``` xml-dtd
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

  ``` xml-dtd
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

#### 3.属性值为具体的像素值

- 属性

  ```xml-dtd
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

    ```xml-dtd
    android:paddingTop="8dp"
    android:paddingLeft="20dp"
    android:paddingStart="60dp"
    ```

    ![添加padding](https://upload-images.jianshu.io/upload_images/5516419-fb7f9d50895f29e8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/352/format/webp)

  - 给下面的图片添加magin

    ```xml-dtd
    android:layout_marginTop="8dp"
    android:layout_marginLeft="20dp"
    android:layout_marginStart="20dp"
    ```

    ![添加margin](https://upload-images.jianshu.io/upload_images/5516419-4cd0de8ef465727c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/404/format/webp)

### FrameLayout

可能是最简单的一种布局，没有任何定位方式，当我们往里面添加控件的时候，会默认把他们放到这块区域的左上角，帧布局的大小由控件中最大的子控件决定，如果控件的大小一样大的话，那么同一时刻就只能看到最上面的那个组件，后续添加的控件会覆盖前一个。由于帧布局的特性，它的应用场景并不是很多，不过它经常配合Fragment使用。

#### 1. 属性

```xml-dtd
android:foreground            //设置改帧布局容器的前景图像
android:foregroundGravity     //设置前景图像显示的位置
```

#### 2. 实例

```xml-dtd
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

### GridLayout

### FlexboxLayout 
