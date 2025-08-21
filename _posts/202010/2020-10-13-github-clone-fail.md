---
layout: post
title: github clone RPC failed
categories: [git]
---
github clone 异常 `error: RPC failed; curl 18 transfer closed with outstanding read data remaining`

最近从github中clone项目时经常遇到下面异常。
```
╰─$ git clone https://github.com/shidongwa/xxl-job.git
Cloning into 'xxl-job'...
remote: Enumerating objects: 14703, done.
error: RPC failed; curl 18 transfer closed with outstanding read data remaining
fatal: the remote end hung up unexpectedly
fatal: early EOF
fatal: index-pack failed
```

参考[stackoverflow中解决方法](https://stackoverflow.com/questions/38618885/error-rpc-failed-curl-transfer-closed-with-outstanding-read-data-remaining)加入 `depth=1` 仍然没有卯用。报 `RPC failed; curl 56 LibreSSL SSL_read: SSL_ERROR_SYSCALL`
```
╭─donghua@ ~/git-project
╰─$ git clone https://github.com/shidongwa/xxl-job.git --depth=1
Cloning into 'xxl-job'...
remote: Enumerating objects: 532, done.
remote: Counting objects: 100% (532/532), done.
remote: Compressing objects: 100% (432/432), done.
RPC failed; curl 56 LibreSSL SSL_read: SSL_ERROR_SYSCALL
```

继续参考[stackoverflow](https://stackoverflow.com/questions/24952683/git-push-error-rpc-failed-result-56-http-code-200-fatal-the-remote-end-hun/36843260#36843260) 下载还是很慢。我用的时ss代理。关闭代理直连问题一样。用码云gitee非常快但是要所有项目都从github切换到码云也不是个事。

没有找到最终解决方案，建议下面的方式缓解一下
```
// 只clone git history中最近1个版本的文件，可以节省传输和存储的文件大小
// ssh代替http方式
// git config --global http.postBuffer 157286400 这种方式没有明显效果
git clone git@github.com:xuxueli/xxl-job.git --depth=1

```


