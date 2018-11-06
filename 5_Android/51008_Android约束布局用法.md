# 约束布局用法

## 当线性布局用

### 下面我们来实现一个常用的底部导航栏，5个导航栏

```xml
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

### 添加权重控制比例

```xml
layout_constraintHorizontal_weight：横向的权重

layout_constraintVertical_weight：纵向的权重
```

如果上文中的tab3要大于其他四个tab，只需要在tab3的View添加app:layout_constraintHorizontal_weight="2"，其他View设置为1，即可

```xml
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

## 当相对布局用

```xml
layout_constraintLeft_toLeftOf：当前View左边在某个View的左边，可以是parent与某个View的ID
layout_constraintLeft_toRightOf：当前View左边在某个View的右边，可以是parent与某个View的ID
layout_constraintRight_toRightOf：当前Viewr的右边在某个View的右边，可以是parent与某个View的ID
layout_constraintRight_toLeftOf：当前Viewr的右边在某个View的左边，可以是parent与某个View的ID
layout_constraintBottom_toBottomOf：当前Viewr的下边在某个View的下边，可以是parent与某个View的ID
layout_constraintBottom_toTopOf：当前Viewr的下边在某个View的上边，可以是parent与某个View的ID
layout_constraintTop_toTopOf：当前Viewr的上边在某个View的上边，可以是parent与某个View的ID
layout_constraintTop_toBottomOf：当前Viewr的上边在某个View的下边，可以是parent与某个View的ID
```

## 当百分比布局用

```
layout_constraintVertical_bias：垂直乖离率（bias有道翻译为乖离率），也就是垂直偏移率。
layout_constraintHorizontal_bias：水平乖离率（bias有道翻译为乖离率），也就是水平偏移率。
layout_constraintHeight_percent：高度百分比，占父类高度的百分比
layout_constraintWidth_percent：宽度百分比，占父类宽度的百分比
```