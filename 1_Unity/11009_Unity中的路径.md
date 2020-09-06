# Unity 中的路径

## Application.persistentDataPath

```c#
string userPath = Application.persistentDataPath + "/user.json";
```

- Application.persistentDataPath 是最合适用来存放下载资源的地方

- 全平台可修可改可新建.

- 只有在项目第一次运行之后才能找到这个路径,所以不合适预先存放东西

- IOS 上该目录下的东西可以被 iCloud 自动备份

## Resources

```c#
Resources.Load()
```

- Resources 文件夹是一个只读的文件夹

- 建议这个文件夹下只放 Prefab 或者一些 Object 对象

- 放在这里的资源文件不管项目中有没有使用都会被打包.

## StreamingAssets

下面是 Unity 官方给的代码,适合读取文件,我用的就是这个

```c#
using UnityEngine;
using System.Collections;

public class ExampleClass : MonoBehaviour {
  public string filePath = System.IO.Path.Combine(Application.streamingAssetsPath, "MyFile");
  public string result = "";
  IEnumerator Example() {
    if (filePath.Contains("://")) {
      WWW www = new WWW(filePath);
      yield return www;
      result = www.text;
    } else{
      result = System.IO.File.ReadAllText(filePath);
    }
  }
}
```

下面是网上的代码,不太好,因为 Android 设备最好用 www 读取

```c#
#if UNITY_EDITOR
  string filepath = Application.dataPath +"/StreamingAssets"+"/my.xml";
#elif UNITY_IPHONE
  string filepath = Application.dataPath +"/Raw"+"/my.xml";
#elif UNITY_ANDROID
  string filepath = "jar:file://" + Application.dataPath + "!/assets/"+"/my.xml;
```

- StreamingAssets 一般用来存放视频等文件,文件不会被 Unity 加密

- 在 pc/Mac 电脑中可实现对文件实施“增删查改”等操作，但在移动端只支持读取操作。

- 只读的物件适合放这里

## 实例

### IOS

| 类型                              | 路径                                                              |
| --------------------------------- | ----------------------------------------------------------------- |
| Application.dataPath :            | Application/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/xxx.app/Data     |
| Application.streamingAssetsPath : | Application/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/xxx.app/Data/Raw |
| Application.persistentDataPath :  | Application/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Documents        |
| Application.temporaryCachePath :  | Application/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Library/Caches   |

### Android

| 类型                              | 路径                                          |
| --------------------------------- | --------------------------------------------- |
| Application.dataPath :            | /data/app/xxx.xxx.xxx.apk                     |
| Application.streamingAssetsPath : | jar:file:///data/app/xxx.xxx.xxx.apk/!/assets |
| Application.persistentDataPath :  | /data/data/xxx.xxx.xxx/files                  |
| Application.temporaryCachePath :  | /data/data/xxx.xxx.xxx/cache                  |

### Windows

| 类型                              | 路径                                                     |
| --------------------------------- | -------------------------------------------------------- |
| Application.dataPath :            | /Assets                                                  |
| Application.streamingAssetsPath : | /Assets/StreamingAssets                                  |
| Application.persistentDataPath :  | C:/Users/xxxx/AppData/LocalLow/CompanyName/ProductName   |
| Application.temporaryCachePath :  | C:/Users/xxxx/AppData/Local/Temp/CompanyName/ProductName |

### Mac

| 类型                              | 路径                                                                      |
| --------------------------------- | ------------------------------------------------------------------------- |
| Application.dataPath :            | /Assets                                                                   |
| Application.streamingAssetsPath : | /Assets/StreamingAssets                                                   |
| Application.persistentDataPath :  | /Users/xxxx/Library/Caches/CompanyName/Product Name                       |
| Application.temporaryCachePath :  | /var/folders/57/6b4_9w8113x2fsmzx_yhrhvh0000gn/T/CompanyName/Product Name |

### Windows Web Player

| 类型                              | 路径                                                                    |
| --------------------------------- | ----------------------------------------------------------------------- |
| Application.dataPath :            | file:///D:/MyGame/WebPlayer (即导包后保存的文件夹，html 文件所在文件夹) |
| Application.streamingAssetsPath : |                                                                         |
| Application.persistentDataPath :  |                                                                         |
| Application.temporaryCachePath :  |                                                                         |

## 参考文档

[参考文档](https://www.cnblogs.com/nanwei/p/8795949.html)
