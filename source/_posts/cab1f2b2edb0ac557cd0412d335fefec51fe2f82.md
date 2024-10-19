---
title: Unity3d 2048 核心算法讲解
date: 2022-11-25 19:17:14
categories: ['5.技能', 'Unity3d']
tags: []
---

2048是一款非常好玩的游戏，经常让人花费好几个小时来玩它。 游戏目标是将相同值的格子“合并”在一起，合并到一起后原来格子的值翻倍。 当玩家在她想要的方向上滑动时，格子被移动到一边并且生成新的格子。 如果玩家达到了2048，那么就赢了游戏。 这篇文档主要用来介绍如何在Unity中来制作这个游戏。
  
  
## 输入系统

游戏实现了通过键盘的箭头键来控制格子移动的方法, 我们通过一个枚举来获取用户的输入和一个必须由我们想要使用的每个输入方法实现的接口IInputDetector。

```
public enum InputDirection
{
    Left, Right, Top, Bottom
}

public interface IInputDetector
{
    InputDirection? DetectInputDirection();
}
```

DetectInputDirection方法的返回值已经实现为Nullable类型，因为用户可能根本没有输入。 现在，让我们访问第一个通过键盘输入的输入法。 如下所示，代码非常简单明了

```
public class ArrowKeysDetector : MonoBehaviour, IInputDetector
{
    public InputDirection? DetectInputDirection()
    {
        if (Input.GetKeyUp(KeyCode.UpArrow))
            return InputDirection.Top;
        else if (Input.GetKeyUp(KeyCode.DownArrow))
            return InputDirection.Bottom;
        else if (Input.GetKeyUp(KeyCode.RightArrow))
            return InputDirection.Right;
        else if (Input.GetKeyUp(KeyCode.LeftArrow))
            return InputDirection.Left;
        else
            return null;
        }
}
```
  
  
## 全局变量

Globals类包含有关Rows，Columns和AnimationDuration的静态变量。

```
public static class Globals
{
    public readonly static int Rows = 4;
    public readonly static int Columns = 4;
    public static readonly float AnimationDuration = 0.05f;
}
```
  
  
## 格子相关数据结构

ItemMovementDetails类用于携带有关即将移动和/或复制的对象的详细信息。 NewRow / NewColumn属性包含项目在数组中的位置信息，而GOToAnimateScale和GOToAnimatePosition属性包含有关即将移动和/或扩展的游戏对象的信息。 正常的过程是移动一个项目（改变它的位置），但如果这个项目将与另一个项目合并，那么这也将改变它的大小（然后消失）。

```
public class ItemMovementDetails
{
    public GameObject GOToAnimateScale { get; set; }
    public GameObject GOToAnimatePosition { get; set; }

    public int NewRow { get; set; }
    public int NewColumn { get; set; }

    public ItemMovementDetails(int newRow, int newColumn, GameObject goToAnimatePosition, GameObject goToAnimateScale)
    {
        NewRow = newRow;
        NewColumn = newColumn;
        GOToAnimatePosition = goToAnimatePosition;
        GOToAnimateScale = goToAnimateScale;
    }
}
```

Item类很简单

- Value属性包含项目的值（例如2,4,8,16等）

- row和column属性包含此项目所属的数组的相应行和列值

- GO属性包含对此Item引用的Unity GameObject的引用

- WasJustDuplicated值是指此项目在此移动中是否重复

```
public class Item
{
    public int Value { get; set; }
    public int Row { get; set; }
    public int Column { get; set; }
    public GameObject GO { get; set; }
    public bool WasJustDuplicated { get; set; }
}
```

ItemArray类包含一个私有成员，一个名为matrix的二维项目数组。 它还公开了一个索引器，以提供对此数组的访问。 如果项占据数组中的位置，则矩阵[row，column]项包含对它的引用。 否则，matrix [row，column]为null。

```
public class ItemArray
{
private Item[,] matrix = new Item[Globals.Rows, Globals.Columns];
public Item this[int row, int column]
{
    get
    {
        return matrix[row, column];
    }
    set
    {
        matrix[row, column] = value;
    }
}
```

此方法获取数组中的非null项。 它用于在每次移动后创建新格子。

```
public void GetRandomRowColumn(out int row, out int column)
{
    do
    {
        row = random.Next(0, Globals.Rows);
        column = random.Next(0, Globals.Columns);
    } while (matrix[row, column] != null);
}
```

每次滑动后都会调用此方法，并将所有WasJustDuplicated值设置为false。

```
private void ResetWasJustDuplicatedValues()
{
    for (int row = 0; row < Globals.Rows; row++)
        for (int column = 0; column < Globals.Columns; column++)
        {
            if (matrix[row, column] != null && matrix[row, column].WasJustDuplicated)
                matrix[row, column].WasJustDuplicated = false;
        }
}
```

此方法检查作为参数传递的两个项（通过其列/行索引）是否具有相同的值。首先，它检查传递的索引是否超出范围。然后它检查此数组位置中的项是否为空，以及它是否只是重复（即在当前滑动后它没有重复）。如果所有这些检查都是真的，那么

- 我们复制第一个项目值并将WasJustDuplicated字段设置为true

- 我们在保持对它的引用之后从数组中删除第二个项目，以便为它设置动画

- 我们返回一个ItemMovementDetails类的新实例，它携带项目的信息以使其位置具有动画效果，并使项目的比例为动画（并最终消失）。

关于根据用户的滑动项目的移动，我们有各种场景我们必须涵盖。请记住，数组中的空项表示空格。

因此，我们假设X是空列，2是值为“2”的列。我们还假设左滑动。可能发生的一些情况如下，以及滑动后的相应项目移动。

```
a）2 | 2 | X | X => 4 | X | X | X.

b）2 | X | 2 | X => 4 | X | X | X.

c）2 | 2 | X | 2 => 4 | 2 | X | X. //前两个'2'将合并，第三个将移动到第二列

d）X | 2 | 2 | 2 => 4 | 2 | X | X. //与先前选项相同的情况。前两个'2'合并，移动到第一列，第三个'2'移动到第二列。

e）4 | 2 | 2 | X => 4 | 4 | X | X.
```

```
private ItemMovementDetails AreTheseTwoItemsSame(
int originalRow, int originalColumn, int toCheckRow, int toCheckColumn)
{
    if (toCheckRow < 0 || toCheckColumn < 0 || toCheckRow >= Globals.Rows || toCheckColumn >= Globals.Columns)
    return null;

        if (matrix[originalRow, originalColumn] != null && matrix[toCheckRow, toCheckColumn] != null
        && matrix[originalRow, originalColumn].Value == matrix[toCheckRow, toCheckColumn].Value
        && !matrix[toCheckRow, toCheckColumn].WasJustDuplicated)
        {
            matrix[toCheckRow, toCheckColumn].Value *= 2;
            matrix[toCheckRow, toCheckColumn].WasJustDuplicated = true;
            var GOToAnimateScaleCopy = matrix[originalRow, originalColumn].GO;
            matrix[originalRow, originalColumn] = null;
            return new ItemMovementDetails(toCheckRow, toCheckColumn, matrix[toCheckRow, toCheckColumn].GO, GOToAnimateScaleCopy);
        }
        else
        {
            return null;
        }
}
```

此方法将项目移动到应该去的位置（基于值检查）。它将项目分配给新位置并“取消”旧项目。此外，它检查它旁边的项目是否具有相同的值。如果是这种情况，我们会返回此信息，而如果它们不同，我们只返回已移动项目的详细信息。

```
private ItemMovementDetails MoveItemToNullPositionAndCheckIfSameWithNextOne
(int oldRow, int newRow, int itemToCheckRow, int oldColumn, int newColumn, int itemToCheckColumn)
{
    matrix[newRow, newColumn] = matrix[oldRow, oldColumn];
    matrix[oldRow, oldColumn] = null;

    ItemMovementDetails imd2 = AreTheseTwoItemsSame(newRow, newColumn, itemToCheckRow,
    itemToCheckColumn);
    if (imd2 != null)
    {
        return imd2;
    }
    else
    {
        return new ItemMovementDetails(newRow, newColumn, matrix[newRow, newColumn].GO, null);
    }
}
```

此方法将项目移动到应该去的位置（基于值检查）。它将项目分配给新位置并“取消”旧项目。此外，它检查它旁边的项目是否具有相同的值。如果是这种情况，我们会返回此信息，而如果它们不同，我们只返回已移动项目的详细信息。

我们有两种移动项目的方法。滑动是水平时调用的一个，垂直滑动调用的一个。在编写代码时，我首先创建了一个“MoveLeft”方法。经过多次测试，修复等，我创建了“MoveRight”。然后，我很清楚它们可以合并为一个方法，所以我创建了MoveHorizontal方法。再次，经过多次测试和修复后，对方法进行了转换和调整，以创建MoveVertical方法。这些方法有很多共同点，它们当然可以合并为一个“Move”方法。但是，我强烈认为这会使本教程复杂化。因此，我决定将它们保留原样。现在，它们在功能上非常相似，所以我们只描述“MoveHorizontal”。

```C#
public List<ItemMovementDetails> MoveHorizontal(HorizontalMovement horizontalMovement)
{
    ResetWasJustDuplicatedValues();

    var movementDetails = new List<ItemMovementDetails>();

    int relativeColumn = horizontalMovement == HorizontalMovement.Left ? -1 : 1;
    var columnNumbers = Enumerable.Range(0, Globals.Columns);

    if (horizontalMovement == HorizontalMovement.Right)
    {
        columnNumbers = columnNumbers.Reverse();
    }
}
```

方法从重置所有WasJustDuplicated值开始。 然后，根据运动是左还是右，我们得到-1或1.这将有助于确定要比较的项目。 如果向左滑动，我们将剩下的所有项目移动，因此我们需要将每个项目与前一项目（-1一项）进行比较，以便测试相似性。 转移，我们使用Enumerable.Range方法来获取列索引。 此方法将返回包含[0,1,2,3，...，Globals.Columns-1]的列表。 如果滑动是正确的，那么我们颠倒columnNumbers列表的顺序。 这是因为我们需要以正确的方向循环colums。 如果向左滑动，我们将首先检查第一列是否为null，然后是第二列等。这就是为什么因为我们要将第一个非空项目从左侧开始移动到第一个空位置。 如果我们有正确的滑动，我们需要在相反的方向上执行此操作。 这就是我们反转columnNumbers列表的原因。

```C#
for (int row = Globals.Rows - 1; row >= 0; row--)
{
    foreach (int column in columnNumbers)
    {
        if (matrix[row, column] == null) continue;

        ItemMovementDetails imd = AreTheseTwoItemsSame(row, column, row, column + relativeColumn);
        if (imd != null)
        {
            movementDetails.Add(imd);
            continue;
        }
    }
}
```

在这里，我们开始循环。 当然，我们会检查所有行。 然后，我们遍历所有列，从columnNumbers列表中获取索引。 在遍历每一行时，我们首先检查每个项目是否为null。 如果它为null，我们继续检查下一个项目（通过检查下一列 - 下一个意味着-1或1，具体取决于滑动。当我们到达非空列时，我们检查此列是否与 再次，“旁边”表示-1或1，具体取决于滑动是左还是右。如果这些项是相同的，那么我们将此信息添加到movingDetails列表并继续循环 下一栏。

```C#
int columnFirstNullItem = -1;

int numberOfItemsToTake = horizontalMovement == HorizontalMovement.Left? column : Globals.Columns – column;

bool emptyItemFound = false;

columnFirstNullItem++)
foreach (var tempColumnFirstNullItem in columnNumbers.Take(numberOfItemsToTake))
{
        columnFirstNullItem = tempColumnFirstNullItem;
    if (matrix[row, columnFirstNullItem] == null)
    {
        emptyItemFound = true;
        break;
    }
}
```

如果这些项不相同，那么我们必须在当前的第一个空位置移动我们当前引用的项。 对于左侧滑动，如果项目是[row，column]，那么唯一可能的位置是从[row，0]到[row，column-1]，因此我们需要columnNumbers列表中的第一个列项。 对于右滑动，唯一可能的位置是从[row，Globals.Columns-1]到[row，column + 1]，因此我们需要第一个Globals.Columns  - 来自reverse columnNumbers列表的列项。 我们在这些列中执行循环（使用Take LINQ方法）保持对每个列号的引用（通过columnFirstNullItem变量）并检查每个项是否为null。 如果我们找到一个，我们退出循环。

```
    if (!emptyItemFound)
    {
        continue;
    }

    ItemMovementDetails newImd =MoveItemToNullPositionAndCheckIfSameWithNextOne
    (row, row, row, column, columnFirstNullItem, columnFirstNullItem + relativeColumn);

    movementDetails.Add(newImd);

        }
    }
    return movementDetails;
}
```

如果我们没有找到空项，则当前引用的项位于正确的位置，因此我们保持原样。 如果我们这样做，那么我们将当前引用的项移动到null位置，并创建ItemMovementDetails类的实例，以便携带动画信息。 在MoveHorizontal方法的末尾，我们返回movementDetails列表，其中包含必须执行的所有动画的信息。
  
  
## 游戏结束判定

```
public bool CheckGameOver()
{
    for (int row = 0; row < Globals.Rows; row++)
    {
        for (int column = 0; column < Globals.Columns; column++)
        {
            if (matrix[row, column] == null)
            {
                return false;
            }
        }
    }

    for (int x = 0; x < Globals.Rows; x++)
    {
        for (int y = 0; y < Globals.Columns - 1; y++)
        {
            if (matrix[x, y].Value == matrix[x, y + 1].Value)
            {
                return false;
            }
        }
    }

    for (int y = 0; y < Globals.Columns; y++)
    {
        for (int x = 0; x < Globals.Rows - 1; x++)
        {
            if (matrix[x, y].Value == matrix[x + 1, y].Value)
            {
                return false;
            }
        }
    }
    return true;
}
```

结束判定很简单,遍历看矩阵中有没有空 或 有没有两个相邻且相等的数