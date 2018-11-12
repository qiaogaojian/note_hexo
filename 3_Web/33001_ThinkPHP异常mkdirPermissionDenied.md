# ThinkPHP异常mkdirPermissionDenied

## 错误记录

在Application目录添加.htaccess后访问遇到 mkdir(): Permission denied 异常

## 解决方法

进入到thinkphp根目录 执行以下命令

```sh
chmod -R 777 runtime
```

## 参考链接

[thinkphp5的mkdir() Permission denied问题探讨](https://www.qiusuoweb.com/68.html)