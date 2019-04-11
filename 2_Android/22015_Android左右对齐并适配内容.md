# Android左右对齐并适配内容

## LinearLayout能实现这个需求代码如下

``` xml
<LinearLayout
android:id="@+id/choose_prefix_layout"
android:layout_width="wrap_content"
android:layout_height="40dp"
android:layout_marginRight="3dp"
android:background="@drawable/reg_edit_bg_left_radius"
app:layout_constraintLeft_toLeftOf="@id/gl_left"
app:layout_constraintRight_toLeftOf="@id/et_phone_number"
app:layout_constraintTop_toTopOf="@id/gl_top">

<TextView
    android:id="@+id/choose_prefix"
    android:layout_width="wrap_content"
    android:layout_height="match_parent"
    android:layout_marginLeft="8dp"
    android:layout_marginRight="9dp"
    android:gravity="center_vertical"
    android:textColor="#FFBC54"
    android:textSize="12sp"
    android:typeface="@{@string/typeface}"
    tools:text="+86" />

<!-- 中间放一个空的view 并且 layout_weight = 1 -->
<TextView
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:layout_weight="1" />

<TextView
    android:id="@+id/choose_prefix_icon"
    android:layout_width="wrap_content"
    android:layout_height="match_parent"
    android:layout_gravity="end"
    android:layout_marginRight="10dp"
    android:gravity="center_vertical"
    android:textColor="#ffffff"
    android:textSize="12sp"
    android:typeface="@{@string/typeface}"
    tools:text="中国"    />
</LinearLayout>
```