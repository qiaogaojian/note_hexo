# vscode+golang环境配置

## 前置条件

安装git vscode go

## 操作步骤

1. 打开vscode设置配置文件

2. 添加go环境路径

    ``` go
    "go.goroot": "C:\\Program Files\\Go",
    "go.gopath": "C:\\Users\\PC-QGJ\\go",
    ```

    如果不确定路径是多少 命令行输入go env把对应的路径输入

3. 打开go工程文件夹配置debug设置