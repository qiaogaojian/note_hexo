# ����ת�������ĵ�

## ����

1. ʹ��Unity3d�򿪹���

2. �� Tools �˵� DataParser ѡ��

    ![](/images/2020-12-27-21-45-45.png)

  - Byte2Excel ѡ��: ����Ϸ��ԭʼ����������תΪExcel�ĵ�

  - Excel2Json ѡ��: �ѵ������ExcelתΪJson����, ��Ϸ����ʹ�õ���Json����

## ʹ��

1. �޸� GJCS_beta/Data/ExcelData �е�Excel �ĵ�

2. ʹ��Unity���̵�Excel2Json ѡ��ת Excel �ĵ�Ϊ��Ϸ��ʹ�õ� Json ����, Ȼ��Ϳ��Խ���Ϸ��Ч����.
ת�����ݺ���Զ�д�뵽 GJCS_beta/Asset/Resources/jsondata Ŀ¼��, ��ϷĬ�϶�ȡ��������.

3. ����ͨ������ LocalModelManager.cs �е� DataType �����Ƽ��ص���������

    ``` c#
	public class LocalModelManager
	{

        ......

        /// <summary>
        /// ���ü����ļ�����
        /// </summary>
        /// <param name="dataType"> 1 json 2 bytes 3 excel </param>
        public int DataType { get;  set; } = 1;

        ......
    }
    ```