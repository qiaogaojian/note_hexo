---
title: Fish 命令行工具
date: 2022-11-12 00:42:56
categories: ['6.工具', '开发', '开发环境']
tags: ['linux', 'shell', '开发环境', '开发', '工具']
---

deepin 的终端命令行没有智能提示，感觉不好用，推荐安装 fish，命令行辅助工具。
  
  
### 安装

```bash
sudo apt install fish
```
  
  
### 启用

```bash
chsh -s /usr/bin/fish
```
  
  
### 关闭欢迎词

```bash
set -U fish_greeting
```
  
  
### 设置

```bash
fish_config
```

推荐的颜色主题：Tomorrow Night，选中点右上角的“Set Theme”按钮(灰底浅灰色字，请仔细找)。
推荐的提示符：Informative Vcs，选中以后点右上角的“Set Prompt”按钮(灰底浅灰色字，请仔细找)。
  
  
### 使用方法

使用方法等可以参考我以前的博客“[Fish 入门指南](../295d228c58beb58d3e477c857137241300856830)”。
  
  
### 安装 Fisher 插件

参考:[GitHub - jorgebucaran/fisher: A plugin manager for Fish.](https://github.com/jorgebucaran/fisher)
```sh
curl -sL https://raw.githubusercontent.com/jorgebucaran/fisher/main/functions/fisher.fish | source && fisher install jorgebucaran/fisher
```
  
  
### 注意事项

在极少数情况下，fish 环境执行脚本会报错，这时可以临时切换到 bash 执行那个脚本(.sh 文件)。通常不会遇到，万一遇到了临时切换一下就可以了。


**Backlinks:**

- [开发环境](../8ed3626f24d1fafe372135071b6d2bc66a7b7436)

{% pullquote mindmap mindmap-md %}
- 🔵
  - [开发环境](../8ed3626f24d1fafe372135071b6d2bc66a7b7436)
  - [Fish 入门指南](../295d228c58beb58d3e477c857137241300856830)
{% endpullquote %}