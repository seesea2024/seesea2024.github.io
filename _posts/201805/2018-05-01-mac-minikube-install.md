# MBP install minikube

## 背景
MBP can access google via shadowsocks locally. 本来想用mac docker-for-desktop方案，参考https://github.com/maguowei/k8s-docker-for-mac，没有成功。最后还是用minikube方案。

参考了minikube官网手册，https://kubernetes.io/docs/getting-started-guides/minikube/， 但是在下面这一步时一直过不去
```
curl $(minikube service hello-minikube --url)
```
通过查看minikube日志,发现应该是kube组件不能访问google。

```
minikube logs
```

```
E0430 13:25:24.853253 2025 kubelet_node_status.go:106] Unable to register node "docker-for-desktop" with API server: Post https://192.168.65.3:6443/api/v1/nodes: dial tcp 192.168.65.3:6443: getsockopt: connection refused
```

## 前提
### 安装hyperkit
[driver installation](https://git.k8s.io/minikube/docs/drivers.md#hyperkit-driver)
```
curl -LO https://storage.googleapis.com/minikube/releases/latest/docker-machine-driver-hyperkit \
&& chmod +x docker-machine-driver-hyperkit \
&& sudo mv docker-machine-driver-hyperkit /usr/local/bin/ \
&& sudo chown root:wheel /usr/local/bin/docker-machine-driver-hyperkit \
&& sudo chmod u+s /usr/local/bin/docker-machine-driver-hyperkit
```

### 安装privoxy
privoxy作用是socks5转http代理。minikube支持的是http代理，我们访问google用的是shadowsocks sock5.所以需要做一次转换。参考（https://javasgl.github.io/transfer_socks5_to_http_proxy/）

```
vim /usr/local/etc/privoxy/config
```
这里需要特别注意的是地址**192.168.64.1**必须是host本机提供给minikube的那个ip（相当与网关,其他ip地址貌似不行）。可以通过 ```minikube ip``` 命令得到kube ip，再从host```ifconfig```中找出对应的host ip。
```
forward-socks5  /  127.0.0.1:1080 .
listen-address  192.168.64.1:8118
```
MBP环境下安装过程中如果遇到下面异常
```
Warning: privoxy 3.0.26 is already installed, it's just not linked
You can use `brew link privoxy` to link this version.
╭─Donghua@ ~
╰─$ brew link privoxy
Linking /usr/local/Cellar/privoxy/3.0.26...
Error: Could not symlink sbin/privoxy
/usr/local/sbin is not writable.
```
可以通过下面方式解决
```shell
sudo mkdir /usr/local/sbin
sudo chown -R `whoami`:admin /usr/local/sbin
```

**注意这里不用启动privoxy，只需要配置好。下一步minikube start的时候会自动启动privoxy**


## 关键步骤
```
# 清理
minikube delete
# 启动
minikube start --docker-env http_proxy='http://192.168.64.1:8118' --docker-env https_proxy='http://192.168.64.1:8118'  --vm-driver=hyperkit
export no_proxy=$no_proxy,$(minikube ip)
# 验证
curl $(minikube service hello-minikube --url)
minikube dashboard
```
