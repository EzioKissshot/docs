# Shell

## ssh

### ssh超时断开解决方法

修改客户端配置

~/.ssh/config

```
Host * 
    ServerAliveInterval 5
```

Host * #表示需要启用该规则的服务端（域名或ip）
ServerAliveInterval 5 #表示每5秒去给服务端发起一次请求消息（这个设置好就行了）
ServerAliveCountMax 3 #表示最大连续尝试连接次数（这个基本不用设置）

[具体原因、其他方案参考](http://bluebiu.com/blog/linux-ssh-session-alive.html)

### ssh走代理
```
ssh -p 29827 -o ProxyCommand='nc -x 127.0.0.1:1086 %h %p' root@xx.xx.xx.xxx
```

## electron base app (discord) 走代理

- PC：https://zhuanlan.zhihu.com/p/47048247
- MAC: open /Applications/Discord.app --args --a=--proxy-server=http://127.0.0.1:1086