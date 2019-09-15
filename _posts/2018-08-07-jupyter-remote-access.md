---
layout: post
keywords: jupyter, jupyter notebook, jupyter lab, ssh
description: Jupyter 远程访问笔记
title: "远程访问 Jupyter"
categories: [python]
tags: [jupyter, jupyter-lab, jupyter-notebook, ssh]
group: archive
icon: compass
---
{% include mathsyouth/setup %}


### Jupyter 远程访问设置

1. 在本地机器上进行 ssh 端口映射
```shell
ssh  -p port_num -L 8008:localhost:8888 remote_user_name@remote_server_ip
```
其中，`-p port_num` 表示远程访问的端口，`-L 8008:localhost:8888` 表示将远程服务器的 Jupyter Lab 端口 `8888` 映射到本地机器的 `8008` 端口， `localhost` 指向 `remote_server_ip`。

2. 在远程服务器上运行命令
```shell
jupyter lab --ip=0.0.0.0 --no-browser 
```
其中，`--ip=0.0.0.0` 表示任意 IP 访问，也就是说可以通过远程的浏览器访问，`--no-browser` 不启动本地浏览器。返回的 URL 为：
```
  http://remote_server_name:8888/?token=b734514c7d82ac3f4c550280c4bab2f495474fb9dcad026d&token=b734514c7d82ac3f4c550280c4bab2f495474fb9dcad026d
```
复制 Token，也就是 `b734514c7d82ac3f4c550280c4bab2f495474fb9dcad026d`。

3. 在本地浏览器输入地址 `127.0.0.1:8008`，将上面的 Token 复制到输入框中，就可以正常显示。


### 参考资料

1. [实战 SSH 端口转发](https://www.ibm.com/developerworks/cn/linux/l-cn-sshforward/index.html)
2. [SSH隧道技术简介](https://blog.csdn.net/detrox/article/details/5542414)
3. [SSH原理与运用（一）：远程登录](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html)
4. [SSH原理与运用（二）：远程操作与端口转发](http://www.ruanyifeng.com/blog/2011/12/ssh_port_forwarding.html)
