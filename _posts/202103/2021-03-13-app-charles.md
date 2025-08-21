---
layout: post
title: android app抓包
categories: [android charles]
---
抓包步骤

## mac电脑安装charles
1 下载v 4.6版本并安装
2 关闭电脑上其他VPN客户端，否则抓包失败
3 支持https
3.1 Help -> SSL Proxying -> Install Charles Root Certificate，选择“Always Trust”
3.2 Proxy -> SSL Proxy Settings SSL Proxying选项卡中location中配置域名和端口，否则抓包内容显示为unknown
3.3 客户端android设置代理和端口
代理IP和端口可以通过charles菜单知道
charles->help->SSL Proxying->Install Charles Root Certificate on a Mobile Device or Remote Browser
3.3 客户端android安装证书
andorid浏览器访问http://chls.pro/ssl下载crt证书文件，并点击安装

注意高版本的android虽然提升安装证书成功，但是实际上失败，抓包内容还是显示unknown。比如小米9 Android 10上面https抓包失败；另外vivo android 6上面抓包正常。


