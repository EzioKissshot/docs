# 自动直播录制&&上传dropbox

## 使用工具
- [bilibili-go](https://github.com/hr3lxphr6j/bililive-go)：自动轮询是否开播、并录制直播（使用docker版本）
- [rclone](https://rclone.org/)：将文件上传到dropbox（或其他云）

## 具体操作

1. 安装docker

2. 写一个bilibili-go的config.yml，放在一个合适的地方，之后要挂到docker的实例上去
    ```
    # config.yml
    rpc: 
    enable: true           # 是否开启API
    port: :9090    # 监听地址
    token: "xxxx"               # token
    tls:                    # tls配置
        enable: false
        cert_file: ""
        key_file: ""
    debug: false              # debug模式
    interval: 15              # 直播间状态查询间隔时间（秒）
    out_put_path: /srv/bililive          # 输出文件路径
    live_rooms:               # 直播间url
        - https://live.bilibili.com/281 # 枯水
        - https://live.bilibili.com/2004599 # debug
    ```
    
3. 用以下命令开启自动录制直播
    ```
    docker run -v ~/app/bililive/bililive/records:/srv/bililive -v ~/app/bililive/bililive/config:/etc/bililive-go -d -p 9090:9090 chigusa/bililive-go
    ```
    用两个volumn，将本机和docker实例的存储直播路径和config.yml路径分别关联起来，API暴露在9090，[api-doc](https://github.com/hr3lxphr6j/bililive-go/blob/master/docs/API.md)。

    docker的volumn命令是local:remote，并且必须是绝对路径

4. 配置rclone

    安装好rclone后，配置一个remote，这里是`ezio-dropbox`

5. 写一个python脚本，访问API获取直播间，先判断下是否在直播，如果不在直播则可以将之前的直播视频上传到dropbox。（为了保证逻辑的简单，这里rclone用的是move，如果不判断的话会导致视频的一部分丢失）

    ```
    #-*- coding: UTF-8 -*-
    import requests
    import os.path
    import os
    from string import Template
    records_root_path = "/root/app/bililive/bililive/records"
    r = requests.get('http://0.0.0.0:9090/lives', {'token':'xxxx'})
    # print(r.json())
    if r.status_code == 200 and r.json()['err_no'] == 0:
        rj = r.json()
        for live in rj['data']:
            if live['status'] == False:
                up_path = os.path.join(records_root_path, live['platform_cn_name'] ,live['host_name'])
                print('try to move ' + live['host_name'])
                # print repr(up_path)
                # print repr(live['host_name'])
                # shell执行rclone将直播移到dropbox相应文件夹/lives/up主名
                os.system('rclone move ' + up_path.encode('UTF-8') + ' ezio-dropbox:/lives/' + live['host_name'].encode('UTF-8'))
    ```

6. 写一个定时任务

    sync-live.cron
    ```
    0 6 * * * root python /root/app/bililive/bililive/move-live-everyday.py
    ```

7. 将定时任务注册到cron

    ```
    crontab sync-live.cron
    ```