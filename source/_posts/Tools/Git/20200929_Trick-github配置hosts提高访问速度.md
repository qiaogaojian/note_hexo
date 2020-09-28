# github配置hosts提高访问速度

## 修改 Hosts

- 进入目录 C:\Windows\System32\drivers\etc

- 打开hosts文件并添加以下内容

```sh
# GitHub Start
192.30.253.112 github.com
192.30.253.113 github.com
192.30.253.119 gist.github.com
151.101.185.194 github.global.ssl.fastly.net
151.101.100.133 assets-cdn.github.com
151.101.100.133 raw.githubusercontent.com
151.101.100.133 gist.githubusercontent.com
151.101.100.133 cloud.githubusercontent.com
151.101.100.133 camo.githubusercontent.com
151.101.100.133 avatars0.githubusercontent.com
151.101.100.133 avatars1.githubusercontent.com
151.101.100.133 avatars2.githubusercontent.com
151.101.100.133 avatars3.githubusercontent.com
151.101.100.133 avatars4.githubusercontent.com
151.101.100.133 avatars5.githubusercontent.com
151.101.100.133 avatars6.githubusercontent.com
151.101.100.133 avatars7.githubusercontent.com
151.101.100.133 avatars8.githubusercontent.com
# GitHub End
```

## 刷新 DNS 缓存

CMD输入命令

```sh
ipconfig /flushdns
```