---
layout: post
keywords: macOS Sierra, proxychains-ng
description: macOS Sierra 下安装 proxychains-ng, 方便终端连上代理
title: "macOS Sierra 下科学上网"
categories: [tool]
tags: [macOS Sierra, proxychains-ng]
group: archive
icon: key
---
{% include mathsyouth/setup %}


### 直接配置 macOS 代理上网

假设代理的 **IP Address: 192.168.21.2, Port: 3128, Socks version: 5**。

配置步骤为：打开网络偏好设置 > 以太网（已连接）> 高级 > 代理，配置「网页代理（HTTP）」、「安全网页代理（HTTPS）」和 「SOCKS 代理」为：`192.168.21.2：3128`。

### proxychains-ng 配置和使用

#### 安装 proxychains-ng

```
brew install proxychains-ng
```

#### 配置 proxychains-ng

修改配置文件 `/usr/local/etc/proxychains.conf` 的最后一个配置项 `[ProxyList]`：

```
[ProxyList]
socks5 192.168.21.2   3128
http   192.168.21.2   3128
```

#### 使用 proxychains-ng

需要通过代理，在命令前面加 `proxychains4` 即可，比如可以通过测试连接 twitter来验证 proxychains-ng 是否配置成功：

```
proxychains4 curl twitter.com
```
