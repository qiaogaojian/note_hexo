# C#基于VSCode开发环境的搭建

## 下载安装 .NET Core SDK

[下载链接]( https://www.microsoft.com/net/download/core)

## 新建dotnet core 工程

### 打开控制台

### 进入工程根目录

```sh
cd d:/workspace
```

### 创建工程目录

```sh
mkdir c#
```

### 第一次运行

```sh
dotnet new
```

### 列举可以创建的工程类型

```sh
dotnet new --help
```

### 新建一个console工程

```sh
dotnet new console
```

### 新建一些配置文件

```sh
dotnet restore
```

### 创建完成

```sh
dotnet run
```

## 用VSCode打开工程文件夹

## 根据提示安装插件后重启

## 下方状态栏调试器选择 .NET Core Launch(console)(C#)

## 运行

## 根据提示创建Task.json

## 根据提示修改launch.json 把target预留位置填入相应的dll

```json
"program" : "${workspaceFolder}/bin/Debug/netcoreapp2.1/C#.dll",
```

## 再次运行调试

## 智能提示

    只需要下方状态栏选择相应的解决方案即可