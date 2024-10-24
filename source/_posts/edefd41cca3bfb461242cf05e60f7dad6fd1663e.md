---
title: Python 开发环境
date: 2022-11-12 00:42:56
categories: ['6.工具', '开发', '开发环境']
tags: ['开发环境', '开发', 'python', 'srcard', '工具']
---
  
  
## 安装

  
  
### Win10

安装 python 3.9 最新版本

 [python 下载地址](https://www.python.org/downloads/)

按默认选项安装后, 默认同时安装 pip 和配置环境变量
 
**升级 Pip**
  
```node
// 进入python安装目录
.\python.exe  -m pip install --upgrade pip
```
<!--SR:!2027-10-31,1190,250-->
  
  
### Deepin

Deepin 自带 python2 和 python3

**Pip 安装**
```sh
sudo apt install python3-pip
```

**升级 Pip**
  
```sh
pip3 install --upgrade pip
```
<!--SR:!2027-02-23,1040,250-->
  
  
## 配置

  
  
### pip 常用命令

  
```node
// pip 安装命令
pip install packagename
// pip 卸载包命令
pip uninstall packagename
// pip 检测更新命令
pip list –outdated
// pip 升级包命令
pip install --upgrade packagename  
```
<!--SR:!2024-10-14,523,250-->

**pip 安装特定版本库?**
  
```sh
# pip install packagename==version
pip install scikit-learn==0.18.0	# 下载scikit-learn的0.18.0版本
```
<!--SR:!2026-07-31,810,230-->

**pip 镜像源修改**: [Python 修改 pip 源为国内镜像源](../e85089d47d0a9a1e5419aad022437f772a987bd7)
  
  
### vscode python 配置

设置以当前文件路径为工作路径
  
```python
"python.terminal.executeInFileDir": true,
```
<!--SR:!2027-05-14,1088,250-->

launch.json 设置调试时以当前文件路径为工作路径
  
```python
{
  "version": "0.2.0",
  "configurations": [
    {
      ...
      "cwd": "${fileDirname}"
    }
  ]
}
```
<!--SR:!2026-07-24,910,250-->
  
  
## 使用

- [Python 项目管理最佳实践 Poetry](../acc2d6da5dd37affe3f03e94d2997ae7cd02bc92)
- [说说 Python 的命名规范](../f4d9b39cffbb9a542e360bd81bf53fa67120f26c)


**Backlinks:**

- [开发环境](../0c32955781debd23d9593f3ed51d05fde4a7304f)

{% pullquote mindmap mindmap-md %}
- 🔵
  - [Python 项目管理最佳实践 Poetry](../acc2d6da5dd37affe3f03e94d2997ae7cd02bc92)
  - [开发环境](../0c32955781debd23d9593f3ed51d05fde4a7304f)
  - [Python 修改 pip 源为国内镜像源](../e85089d47d0a9a1e5419aad022437f772a987bd7)
  - [说说 Python 的命名规范](../f4d9b39cffbb9a542e360bd81bf53fa67120f26c)
{% endpullquote %}