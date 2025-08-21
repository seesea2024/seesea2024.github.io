---
layout: post
title: kube proxy
categories: [k8s]
---
## kube-proxy

All of the worker nodes run a daemon called kube-proxy, which watches the API server on the master node for the addition and removal of Services and endpoints. For each new Service, on each node, kube-proxy configures the iptables rules to capture the traffic for its ClusterIP and forwards it to one of the endpoints. When the service is removed, kube-proxy removes the iptables rules on all nodes as well.

![kube proxy](https://github.com/shidongwa/seesea2024.github.io/blob/master/images/kubeproxy.png?raw=true)