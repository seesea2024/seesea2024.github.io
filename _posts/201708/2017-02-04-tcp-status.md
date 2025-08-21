---
title: TCP状态
date: 2017-02-04 11:53:04 Z
categories:
- blog
layout: post
---

### TCP连接状态

* 统计各种TCP状态的链接数
  
  ```
  netstat -n | awk '/^tcp/ {++S[$NF]} END {for(a in S) print a, S[a]}' 
  ```

![TCP握手](https://raw.githubusercontent.com/shidongwa/seesea2024.github.io/master/images/tcp-status.png)
