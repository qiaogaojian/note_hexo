# 不忽略git设置

## 忽略所有文件除了某些类型

```sh
*/*
!.cs
```

## 假设只跟踪 Scripts 目录和脚本/md文件

编辑.gitignore文件(.gitignore)
忽略所有文件，注意放在开头

```sh
# 忽略所有文件和目录
/*
*.*
# 除Scripts文件夹 和脚本/md文件外
!/Scripts
!*.cs
!*.md
```
