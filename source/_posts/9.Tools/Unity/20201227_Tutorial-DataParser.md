# 数据转换工具文档

## 功能

1. 使用Unity3d打开工程

2. 打开 Tools 菜单 DataParser 选项

    ![](/images/2020-12-27-21-45-45.png)

  - Byte2Excel 选项: 把游戏的原始二进制数据转为Excel文档

  - Excel2Json 选项: 把调整后的Excel转为Json数据, 游戏真正使用的是Json数据

## 使用

1. 修改 GJCS_beta/Data/ExcelData 中的Excel 文档

2. 使用Unity工程的Excel2Json 选项转 Excel 文档为游戏内使用的 Json 数据, 然后就可以进游戏看效果了.
转换数据后会自动写入到 GJCS_beta/Asset/Resources/jsondata 目录下, 游戏默认读取这里数据.

3. 可以通过控制 LocalModelManager.cs 中的 DataType 来控制加载的数据类型

    ``` c#
	public class LocalModelManager
	{

        ......

        /// <summary>
        /// 设置加载文件类型
        /// </summary>
        /// <param name="dataType"> 1 json 2 bytes 3 excel </param>
        public int DataType { get;  set; } = 1;

        ......
    }
    ```