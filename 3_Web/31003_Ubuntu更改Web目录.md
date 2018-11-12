# Ubuntu更改Web目录

- 配置文件

```sh
nano /etc/apache2/sites-enable/000-default
```

如下所示

```sh
 ServerAdmin webmaster@localhost
        DocumentRoot /var/www/html/thinkphp5/public

        # Available loglevels: trace8, ..., trace1, debug, info, notice, warn,
        # error, crit, alert, emerg.
        # It is also possible to configure the loglevel for particular
        # modules, e.g.
        #LogLevel info ssl:warn
```

修改 DocumentRoot 后面的目录为自己想要的Web目录

## 提醒

Windows环境下配置文件如下

```sh
..\apache\conf\httpd.conf
```

还是修改 DocumentRoot 对应的值