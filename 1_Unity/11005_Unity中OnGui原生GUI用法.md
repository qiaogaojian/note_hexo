# Unity中OnGui原生GUI用法

## Button

```C#
  void OnGUI()
    {
        if (GUI.Button(new Rect(10, 360, 200, 50), "开始新手引导"))
        {
            GuideManager.Instance.InitGuide();
        }
    }
```

### Rect.x Rect.y   按钮左下角坐标

### Rect.z Rect.w   按钮的宽和高