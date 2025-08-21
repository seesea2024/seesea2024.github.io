---
layout: post
title: vps + shadowsocks 上网
categories: [shadowsocks，上网，vps]
---

## vps
vps 可以从[vultr](https://my.vultr.com/)购买，5美元一个月，ubuntu日本（1CPU，1G内存，25G SSD，1000G网络带宽/月），访问速度还行。

## shadowsocks

### vps上安装shadowsocks服务端
```shell
apt-get update
apt-get install python-pip
pip install --upgrade pip
pip install setuptools
pip install shadowsocks
vim  /etc/shadowsocks.json
chmod 755 /etc/shadowsocks.json
apt-get install python-m2crypto
ssserver -c /etc/shadowsocks.json -d start
ssserver -c /etc/shadowsocks.json -d stop
```

shadowsocks.json
```
{
    "server":"0.0.0.0",
    "server_port":1024,
    "local_address": "127.0.0.1",
    "local_port":1080,
    "password":"mypassword",
    "timeout":300,
    "method":"aes-256-cfb"
}
```

### 客户端安装shadowsocks客户端

## 参考
https://www.flyzy2005.com/fan-qiang/shadowsocks/build-shadowsocks-on-vps/