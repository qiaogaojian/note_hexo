# Android富文本

## Html.fromHtml()

``` java
String nameContent = "<font color=#ffffff>您的 </font>" + "<font color=#FFBC55>" + cardName + "</font>" + "<font color=#ffffff> 还未到期<br>是否继续购买延长时间？</font>";
binding.tvName.setText(Html.fromHtml(nameContent));
```

## Spannable

``` java
String    prefix      = "您的 ";
String    suffix      = " 还未到期\n是否继续购买延长时间？";
String    nameContent = prefix + cardName + suffix;
Spannable spannable   = new SpannableString(nameContent);

// 这里的span只能用new 因为多次set同一个span 后面的会把前面的覆盖掉
ForegroundColorSpan yellowColorSpan = new ForegroundColorSpan(getActivity().getResources().getColor(R.color.dull_yellow));
spannable.setSpan(new ForegroundColorSpan(getActivity().getResources().getColor(R.color.white)), 0, prefix.length(), Spannable.SPAN_INCLUSIVE_EXCLUSIVE);
spannable.setSpan(yellowColorSpan, prefix.length(), (prefix + cardName).length(), Spannable.SPAN_EXCLUSIVE_EXCLUSIVE);
spannable.setSpan(new ForegroundColorSpan(getActivity().getResources().getColor(R.color.white)), (prefix + cardName).length(), nameContent.length(), Spannable.SPAN_EXCLUSIVE_EXCLUSIVE);
binding.tvName.setText(spannable, TextView.BufferType.SPANNABLE);
```

## 参考链接

[How can I change the color of a part of a TextView?](https://stackoverflow.com/questions/4032676/how-can-i-change-the-color-of-a-part-of-a-textview)

[Android富文本实现图文混排](https://www.jianshu.com/p/050ffa5b762c)