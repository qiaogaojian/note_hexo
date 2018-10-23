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

### ConstraintLayout

#### 当作 RelativeLayout 使用

```xml-dtd
layout_constraintLeft_toLeftOf:   当前View左边在某个View的左边,可以是parent与某个View的ID
```

![1](https://upload-images.jianshu.io/upload_images/3947109-1049c2442913ad75.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```xml-dtd
layout_constraintLeft_toRightOf：当前View左边在某个View的右边，可以是parent与某个View的ID
```

![2](https://upload-images.jianshu.io/upload_images/3947109-4425a7d4535375ba.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

那如果这两种属性都加上，那么当前View就应该是父View左右居中的，看效果

```xml-dtd
layout_constraintLeft_toLeftOf：当前View左边在某个View的左边，可以是parent与某个View的ID
layout_constraintLeft_toRightOf：当前View左边在某个View的右边，可以是parent与某个View的ID
```

![3](https://upload-images.jianshu.io/upload_images/3947109-9fc2bcd9a40e2d1b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```xml-dtd
layout_constraintRight_toRightOf：当前Viewr的右边在某个View的右边，可以是parent与某个View的ID
```

![4](https://upload-images.jianshu.io/upload_images/3947109-14370043499aa302.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```xml-dtd
layout_constraintRight_toLeftOf：当前Viewr的右边在某个View的左边，可以是parent与某个View的ID
```

![5](https://upload-images.jianshu.io/upload_images/3947109-3f8d69e85030a806.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```xml-dtd
layout_constraintBottom_toBottomOf：当前Viewr的下边在某个View的下边，可以是parent与某个View的ID
```

![6](https://upload-images.jianshu.io/upload_images/3947109-30b9ba65b73e7d63.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```xml-dtd
layout_constraintBottom_toTopOf：当前Viewr的下边在某个View的上边，可以是parent与某个View的ID
```

![7](https://upload-images.jianshu.io/upload_images/3947109-5fc8128cc47a7ea8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```xml-dtd
layout_constraintTop_toTopOf：当前Viewr的上边在某个View的上边，可以是parent与某个View的ID
```

![8](https://upload-images.jianshu.io/upload_images/3947109-fa90c68007406362.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```xml-dtd
layout_constraintTop_toBottomOf：当前Viewr的上边在某个View的下边，可以是parent与某个View的ID
```

![9](https://upload-images.jianshu.io/upload_images/3947109-fb5e417da0f6a584.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

    许多时候我们需要让子View与父View长度相同，只需要将layout_width或者layout_height设为0dp，让子View没有长度。这样便可以随着父View进入拉伸了

##### 下面我们来实现一个常用的底部导航栏，5个导航栏

![10](https://upload-images.jianshu.io/upload_images/3947109-8637b6569a1554f2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```xml-dtd
<?xml version="1.0" encoding="utf-8"?>
<android.support.constraint.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <TextView
        android:id="@+id/tab0"
        android:layout_width="0dp"
        android:layout_height="50dp"
        android:background="@color/colorPrimary"
        android:gravity="center"
        android:text="tab1"
        android:textColor="@color/colorWhite"
        android:textSize="20sp"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toLeftOf="@+id/tab1" />

    <TextView
        android:id="@+id/tab1"
        android:layout_width="0dp"
        android:layout_height="0dp"
        android:gravity="center"
        android:text="tab2"
        android:textSize="20sp"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintLeft_toRightOf="@+id/tab0"
        app:layout_constraintRight_toLeftOf="@+id/tab2"
        app:layout_constraintTop_toTopOf="@+id/tab0" />

    <TextView
        android:id="@+id/tab2"
        android:layout_width="0dp"
        android:layout_height="0dp"
        android:background="@color/colorAccent"
        android:gravity="center"
        android:text="tab3"
        android:textColor="@color/colorWhite"
        android:textSize="20sp"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintLeft_toRightOf="@+id/tab1"
        app:layout_constraintRight_toLeftOf="@id/tab3"
        app:layout_constraintTop_toTopOf="@+id/tab0" />

    <TextView
        android:id="@+id/tab3"
        android:layout_width="0dp"
        android:layout_height="0dp"
        android:gravity="center"
        android:text="tab4"
        android:textSize="20sp"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintLeft_toRightOf="@+id/tab2"
        app:layout_constraintRight_toLeftOf="@+id/tab4"
        app:layout_constraintTop_toTopOf="@+id/tab0" />

    <TextView
        android:id="@+id/tab4"
        android:layout_width="0dp"
        android:layout_height="0dp"
        android:background="@color/colorHoloOrangeLight"
        android:gravity="center"
        android:text="tab5"
        android:textColor="@color/colorWhite"
        android:textSize="20sp"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintLeft_toRightOf="@+id/tab3"
        app:layout_constraintRight_toRightOf="parent"
        app:layout_constraintTop_toTopOf="@+id/tab0" />
</android.support.constraint.ConstraintLayout>
```

#### 当作 LinearLayout 使用

```xml-dtd
layout_constraintHorizontal_weight：横向的权重

layout_constraintVertical_weight：纵向的权重
```

如果上文中的tab3要大于其他四个tab，只需要在tab3的View添加app:layout_constraintHorizontal_weight="2"，其他View设置为1，即可

![image.png](https://upload-images.jianshu.io/upload_images/3947109-5abaccf506af4f2f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```xml-dtd
 <TextView
    android:id="@+id/tab2"
    android:layout_width="0dp"
    android:layout_height="0dp"
    android:background="@color/colorAccent"
    android:gravity="center"
    android:text="tab3"
    android:textColor="@color/colorWhite"
    android:textSize="20sp"
    app:layout_constraintBottom_toBottomOf="parent"
    app:layout_constraintHorizontal_weight="2"
    app:layout_constraintLeft_toRightOf="@+id/tab1"
    app:layout_constraintRight_toLeftOf="@id/tab3"
    app:layout_constraintTop_toTopOf="@id/tab0" />

<TextView
    android:id="@+id/tab3"
    android:layout_width="0dp"
    android:layout_height="0dp"
    android:gravity="center"
    android:text="tab4"
    android:textSize="20sp"
    app:layout_constraintBottom_toBottomOf="parent"
    app:layout_constraintHorizontal_weight="1"
    app:layout_constraintLeft_toRightOf="@+id/tab2"
    app:layout_constraintRight_toLeftOf="@+id/tab4"
    app:layout_constraintTop_toTopOf="@+id/tab0" />

```

#### 三、当作FrameLayout使用

不建议如此使用，没有这样的需求吧，与frameLayout使用相同

#### 四、百分比布局（重点超大号字体）

百分比布局，意义非常重要，解决碎片化问题就是没有百分比的出现，现在我们来看一下，如何使用的：

```xml-dtd
layout_constraintVertical_bias：垂直乖离率（bias有道翻译为乖离率），也就是垂直偏移率。
layout_constraintHorizontal_bias：水平乖离率（bias有道翻译为乖离率），也就是水平偏移率。
layout_constraintHeight_percent：高度百分比，占父类高度的百分比
layout_constraintWidth_percent：宽度百分比，占父类宽度的百分比
```

假设一下场景，我们需要展示一个Banner，占屏幕的30%。

![image.png](https://upload-images.jianshu.io/upload_images/3947109-5d2e5f839e6e3a08.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```xml-dtd
<TextView
    android:id="@+id/view0"
    android:layout_width="0dp"
    android:layout_height="0dp"
    android:background="@color/colorPrimary"
    android:gravity="center"
    android:text="这是一个Banner"
    android:textColor="@color/colorWhite"
    android:textSize="30sp"
    app:layout_constraintBottom_toBottomOf="parent"
    app:layout_constraintHeight_percent="0.3"
    app:layout_constraintLeft_toLeftOf="parent"
    app:layout_constraintRight_toRightOf="parent"
    app:layout_constraintTop_toTopOf="parent"
    app:layout_constraintVertical_bias="0" />

```

只需要添加属性app:layout_constraintHeight_percent="0.3"占父类的30%，如果仅仅设置这一个属性，你会发现Banner居中了，你还差一个属性，表示从垂直的偏移量： app:layout_constraintVertical_bias="0"偏移量为0，如果就可以了。

使用百分比布局时，View必须要设置上下左右四个锚点，如果不设置就像射线一样，都不知道多大，如何百分比呢？

当锚点是parent（也就是屏幕），因为分辨率不一样，使用百分比的view占的位置、大小肯定是不相同的，720的50%等于360，而1080的50%是等于590，仅仅是看起来位置相同，实际并不相同，所以当百分比与固定大小结合实现布局时，应当注意锚点不要给错了，还可以给到某个固定大小的View身上，如果View宽度是跟随父View，也应当注意。

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

## 参考链接

[Android ConstraintLayout百分比布局使用详解](https://blog.csdn.net/Fy993912_chris/article/details/81909010)

[ConstraintLayout —— 约束布局 知识点整理](https://blog.csdn.net/OneDeveloper/article/details/82021197?utm_source=blogxgwz2)

[Android ConstraintLayout详解](https://www.jianshu.com/p/a8b49ff64cd3)

[How to make ConstraintLayout work with percentage values?
](https://stackoverflow.com/questions/37318228/how-to-make-constraintlayout-work-with-percentage-values)

[Use ConstraintLayout to design your Android views](https://codelabs.developers.google.com/codelabs/constraint-layout/#0)