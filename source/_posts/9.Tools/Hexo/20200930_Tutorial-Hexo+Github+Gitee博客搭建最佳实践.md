---
description: Hexo+Github+Gitee 博客搭建最佳实践
---

# Hexo+Github+Gitee 博客搭建最佳实践

基于 Hexo5.0+Next8.0
简化操作,更新博客时只需要把写好的 Markdown 文件或文件夹放入_Post 文件夹,文章自动生成,文章分类根据文件夹名字生成,Tag 自动生成,部署时会同时部署到 Github 和 Gitee.

> 所有命令都是在博客文件夹下gitbash中运行的

## 预览

[Github](http://qiaogaojian.github.io)

[Gitee](http://qiaogaojian.gitee.io)

## 注册 Github Gitee 账号

## 创建 Pages Repository

### Github

命名格式: username.github.io

### Gitee

命名格式: username (和个人主页路径中的 username 保持一致)

## 安装环境

### git

[下载地址](https://git-scm.com/downloads)

### node

[下载地址](https://nodejs.org/en/download/)

## Hexo 安装

```sh
> npm install -g hexo-cli
```

## Hexo 初始化

### 生成

新建 blog 存放目录,打开终端进入

```sh
> hexo init blog
```

### 测试

```sh
hexo g
hexo s

# hexo n "article" == hexo new "article"  #新建
# hexo g == hexo generate                 #生成
# hexo s == hexo server                   #预览
# hexo d == hexo deploy                   #部署
```

浏览器输入 http://localhost:4000/ 查看默认网页

## Hexo 配置

注意配置的':'后面一定要有空格

### 配置基本信息

```sh
title:          #Blog标题
subtitle:       #副标题
description:    #网页描述
author:         #作者
keywords:       #关键词
tags:           #标签 hexo-enhancer插件会根据这里的标签来匹配文章内容,用于生成文章的标签
language: zh-CN
timezone: Asia/Shanghai
```

### 配置部署

#### 安装 hexo-deployer-git

用于 Git 部署

```sh
> npm install hexo-deployer-git --save
```

#### 单部署

```sh
deploy:
  type: git
  repository: https://github.com/qiaogaojian/qiaogaojian.github.io
  branch: master
```

#### 多部署

```sh
deploy:
  type: git
  repo:
      gitee: https://gitee.com/qiaogaojian/qiaogaojian.git
      github: https://github.com/qiaogaojian/qiaogaojian.github.io
  branch: master
```

### 安装 hexo-enhancer

hexo-enhancer 是一个 Hexo 功能增强插件 [说明文档](https://sulin.me/2019/Z726F8.html)

最新版有 bug,这里使用 1.0.7

```sh
npm install hexo-enhancer@1.0.7 --save
```

建议文章命名格式:时间\_分类-名字 例:"20200929_Tutorial-Hexo+Github+Gitee 博客搭建"

### 更换主题(next)

```
> git clone https://github.com/next-theme/hexo-theme-next
```

为了方便配置和修改,这里克隆下主题后把 next 文件夹放在根目录 themes 文件夹下
然后将themes/next/_config.yml复制到博客根目录，重命名为_config.next.yml

#### 选择主题

打开_config.next.yml

``` sh
# Schemes
#scheme: Muse
#scheme: Mist
#scheme: Pisces
scheme: Gemini
```

#### 设置icon

打开_config.next.yml

```
favicon:
 small: /images/avatar.jfif
 medium: /images/avatar.jfif
 apple_touch_icon: /images/avatar.jfif
 safari_pinned_tab: /images/logo.svg
```

#### 设置菜单

打开_config.next.yml,用于设置菜单选项显示,font awesome图标可以自定义,[官网](https://fontawesome.com/)

```sh
menu:
 home: / || fa fa-home
 about: /about/ || fa fa-user
 tags: /tags/ || fa fa-tags
 categories: /categories/ || fa fa-th
 archives: /archives/ || fa fa-archive
 # guestbook: /guestbook/ || fa fa-book
 # schedule: /schedule/ || fa fa-calendar
 # sitemap: /sitemap.xml || fa fa-sitemap
 # commonweal: /404/ || fa fa-heartbeat
```

##### 新建分类和标签页面

```sh
> hexo new page categories
> hexo new page tags
```

设置categories页面属性(source目录下的md文件)

```
---
comments: false
type: "categories"
---

---
comments: false
type: "tags"
---
```

#### 设置已读进度条

打开_config.next.yml

```sh
reading_progress:
  enable: true
  # Available values: top | bottom
  position: top
  color: "#37c6c0"
  height: 3px
```

#### 搜索功能

##### 安装 hexo-generator-searchdb

```sh
> npm install hexo-generator-searchdb --save
```

##### 配置根目录下 \_config.yml

```sh
search:
  path: search.xml
  field: post
  format: html
  limit: 10000
```

##### 配置主题目录下 \_config.yml

```sh
local_search:
  enable: true
```

##### 重新部署

```sh
hexo clean
hexo g
hexo d
```

#### 添加评论

注册[leancloud](https://www.leancloud.cn/)账号,获取自己的appid,appkey

新建开发版应用

新建存储class "Counter"  设置权限为所有人可读可写

打开设置获取appid appkey

打开_config.next.yml

修改配置

```sh
valine:
    enable: true # 为true时启用评论
    appid:  # 这里填写上面得到的APP ID   注意空一格再输入ID和key,
    appkey:  # 这里填写上面得到的APP KEY
    notify: false #  邮件通知
    verify: false # 验证码
    placeholder:  #评论框中预设的文字,随意填写
    avatar: mm # gravatar style 头像,采用gravatar头像,到http://cn.gravatar.com/了解
    guest_info: nick,mail,link # custom comment header 访客信息,显示在评论框上面,三者可随意选择或全选
    pageSize: 10 # pagination size 评论分页大小
    visitor: false #
```

#### 添加打赏

打开_config.next.yml

```
reward:
   　　enable: true ＃开启
   　　comment: buy me a coffee
   　　wechatpay:   ＃图片地址
   　　alipay:      ＃图片地址
```

#### 隐藏底部 power by hexo

打开 themes/next/layout/_partials/footer.njk 注释下图所示代码

![](/images/2020-09-29-15-12-05.png)

#### 置顶功能

##### 更换原有 hexo-generator-index

```sh
> npm uninstall hexo-generator-index --save
> npm install hexo-generator-index-pin-top --save
```

##### 需要置顶的文章顶部添加

```
---
top: true
---
```

##### 设置置顶标志

打开文件 next/layout/_partials/post/poset-meta.njk

在`<div class="post-meta">`标签下插入如下代码：

```html
  {% if post.top %}
    <i class="fas fa-thumbtack"></i>
    <font color=7D26CD>置顶</font>
    <span class="post-meta-divider"></span>
  {% endif %}
```

##### 重新部署

```sh
hexo clean
hexo g
hexo d
```

#### 添加宠物

安装插件

```sh
> npm install --save hexo-helper-live2d
```

作者提供以下模型的[模型包](https://huaji8.top/post/live2d-plugin-2.0/)，模型包预览地址见下面的链接，选择你想用的模型，记住名字，选择对应的后缀模型包

```
live2d-widget-model-chitose
live2d-widget-model-epsilon2_1
live2d-widget-model-gf
live2d-widget-model-haru/01 (use npm install --save live2d-widget-model-haru)
live2d-widget-model-haru/02 (use npm install --save live2d-widget-model-haru)
live2d-widget-model-haruto
live2d-widget-model-hibiki
live2d-widget-model-hijiki
live2d-widget-model-izumi
live2d-widget-model-koharu
live2d-widget-model-miku
live2d-widget-model-ni-j
live2d-widget-model-nico
live2d-widget-model-nietzsche
live2d-widget-model-nipsilon
live2d-widget-model-nito
live2d-widget-model-shizuku
live2d-widget-model-tororo
live2d-widget-model-tsumiki
live2d-widget-model-unitychan
live2d-widget-model-wanko
live2d-widget-model-z16
```

选择好对应的模型，使用 npm install 模型的包名来安装，比如我选择的的是 live2d-widget-model-wanko 模型包

```
> npm install live2d-widget-model-wanko
```

打开hexo的 _config.yml 添加live2d配置

```sh
live2d:
  enable: true #????
  scriptFrom: local
  model:
    use: live2d-widget-model-wanko
  display:
    position: right
    width: 210
    height: 360
  mobile:
    show: true
```

重新部署预览

```sh
hexo clean
hexo g
hexo d
```

#### 调整字体大小

![调整字体大小](/images/2020-09-29-17-43-34.png)

#### 关闭动画

打开_config.next.yml

```sh
motion:
  enable: false
```

### 插入图片/视频和下载文件

#### 图片

图片统一放在 source/images 文件夹中,访问时路径如下

```sh
![imageName](/images/image.jpg)
```

#### 视频

```sh
# 方法1

<iframe src="（视频网址）" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true" width="100%"  height="580" quality="high" > </iframe>

# 方法2 支持手机自适应

<div style="position: relative; width: 100%; height: 0;padding-bottom: 75%;">
    <iframe src="//player.bilibili.com/player.html?aid=39807850&cid=69927212&page=1" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true" style="position: absolute; width: 100%; height: 100%; left: 0; top: 0;">
    </iframe>
</div>

```

#### 文件

文件下载需要把文件放入source/download文件夹下

[下载地址](/download/file)

## 错误记录

### YAMLException: end of the stream or a document separator is expected

文章头部添加

```sh
---
description: Title
---
```

## 参考

[hexo主题安装以及next8.0主题美化](https://zhuanlan.zhihu.com/p/251383216)

[hexo的next主题个性化配置](https://zhuanlan.zhihu.com/p/60424755)