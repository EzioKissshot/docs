# Shell

## 一行命令实现youtube下载视频到dropbox

命令：`expect ytb2db.exp https://www.youtube.com/watch\?v\=baE6CzzoCL8`，当然是使用了一个脚本啊~（笑

其实上面的youtube和dropbox可以替代成各种视频站和云服务，具体支持哪些要看youtube-dl和rclone支持哪些服务了。

为什么有这个需求呢，因为我本地下载youtube使用youtube-dl的proxy选项很慢而且会断掉，应该是有bug。然后我有个vps，dropbox是会员2T容量，所以可以通过dropbox中转一下，而且放在dropbox上可以多设备选择性同步，完美

于是本着不重复造轮子的思想，去github上找了一下

[ytdlrc](https://github.com/bardisty/ytdlrc)是一个使用[youtube-dl](https://rg3.github.io/youtube-dl/)下载视频，再用[rclone](http://rclone.org/)上传到云服务的shell脚本，具体支持哪些请看youtube-dl和rclone的文档

将ytdlrc在vps上设置好（具体也请参照文档
- git clone
- 安装coreutils、ffmpeg、rclone、youtube-dl等依赖
- 初始化设置rclone，绑定一个云服务
- 修改ytdlrc脚本中的rclone_destination等
- 先在远程机器上试下载

然后就来写一个脚本`ytb2db.exp`让本地可以一键下载
```
#!/usr/bin/expect
set user ***
set ipaddress **.**.**.*** 
set passwd ****  
set port ***** 
set timeout 30
set link [lindex $argv 0];
set snatch_list_loc /root/ytdlrc/snatch.list  # 远程机器上snatch.list地址
set ytdlrc_script_loc /root/app/ytdlrc/ytdlrc  # 远程机器上ytdlrc脚本地址

spawn ssh -p $port $user@$ipaddress "echo $link >> $snatch_list_loc && sh $ytdlrc_script_loc"
expect {
    "*password:" { send "$passwd\r" }
    "yes/no" { send "yes\r";exp_continue }
}
interact
```

为了自动输密码，用了`expect`，需要自行安装

接下来就可以用`expect ytb2db.exp https://www.youtube.com/watch\?v\=baE6CzzoCL8`下载视频啦

TODO:
- 支持多参
- fix youtube-dl的proxy（其实这才是最治本的方案吧？？

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