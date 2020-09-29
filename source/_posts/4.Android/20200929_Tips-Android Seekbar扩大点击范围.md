# Seekbar扩大点击范围

## 代码

```java
  SCOPE = 200; // 范围

  public static void extendSeekbar(View parent, SeekBar seekBar,int scope){
        parent.setOnTouchListener(new View.OnTouchListener() {
            @Override
            public boolean onTouch(View v, MotionEvent event) {
                Rect seekBarRect = new Rect();
                seekBar.getHitRect(seekBarRect);
                if (event.getY() >= seekBarRect.top - scope
                        && event.getY() <= seekBarRect.bottom + scope
                        && event.getX() >= seekBarRect.left
                        && event.getX() <= seekBarRect.right) {
                    MotionEvent obtain = MotionEvent.obtain(event.getDownTime(),
                            event.getEventTime(), event.getAction(),
                            event.getX() - seekBarRect.left,
                            seekBarRect.top + seekBarRect.height() / 2.0f,
                            event.getMetaState());
                    return seekBar.onTouchEvent(obtain);
                }
                return false;
            }
        });
    }
```