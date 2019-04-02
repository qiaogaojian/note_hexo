# Android属性动画详解

## 单个View,单个动画

``` java
ObjectAnimator translation = ObjectAnimator.ofFloat(view, View.TRANSLATION_X, 0f, 100f);
translation.setDuration(500);
translation.setRepeatCount(3);

// 可以设置插值器,使之按照一定的规律运动
translation.setInterpolator(new AccelerateDecelerateInterpolator());
translation.start();
```

属性平移动画用的px,如果UI用的是dp,需要进行转换

``` java
/**
 * 根据手机的分辨率从 dp 的单位 转成为 px(像素)
 */
public static int dip2px(Context context, float dpValue)
{
    final float scale = context.getResources().getDisplayMetrics().density;
    return (int) (dpValue * scale + 0.5f);
}

/**
 * 根据手机的分辨率从 px(像素) 的单位 转成为 dp
 */
public static int px2dip(Context context, float pxValue)
{
    final float scale = context.getResources().getDisplayMetrics().density;
    return (int) (pxValue / scale + 0.5f);
}
```

## 单个View,多个动画

``` java
PropertyValuesHolder scale_x = PropertyValuesHolder.ofFloat( View.SCALE_X, 0f, 2f, 0.5f);
PropertyValuesHolder alpha = PropertyValuesHolder.ofFloat(View.ALPHA, 0f, 0.7f);

ObjectAnimator animator = ObjectAnimator.ofPropertyValuesHolder(tv, scale_x, alpha);
animator.setDuration(3000);
animator.start();
```

## 多个View,多个动画

``` java
PropertyValuesHolder tran  = PropertyValuesHolder.ofFloat(View.TRANSLATION_Y, 0f,dip2px(getContext(),51));
PropertyValuesHolder alpha = PropertyValuesHolder.ofFloat(View.ALPHA, 0f, 1f);

ObjectAnimator ani1 = ObjectAnimator.ofPropertyValuesHolder(activityRegBinding.etPw, tran, alpha);
ObjectAnimator ani2 = ObjectAnimator.ofPropertyValuesHolder(activityRegBinding.btnShowPw, tran, alpha);

AnimatorSet aniSet = new AnimatorSet();

// 一起执行
aniSet.playTogether(ani1, ani2);
aniSet.play(ani1).with(ani2);
// 先后执行
aniSet.playSequentially(ani1,ani2);
aniSet.play(ani2).after(ani1);

aniSet.setDuration(500);
aniSet.start();

```