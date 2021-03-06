---
title: "常见问题" 
---

### 如何快速了解 KubeSphere

1、作为新手，如何快速了解 KubeSphere 的使用？

答：我们提供了多个快速入门的示例包括工作负载和 DevOps 工程，建议从 [快速入门](../../zh-CN/quick-start/quick-start-guide) 入手，参考 **快速入门** 并实践和操作每一个示例。

### Multi-node 安装配置相关问题

2、[Multi-Node 模式](../installation/multi-node) 安装时，如果某些服务器的 **Ubuntu** 系统默认管理员用户为 `ubuntu`，若切换为 `root` 用户进行安装，应该如何配置和操作？

可通过命令 `sudo su` 切换为 root 用户后，在该节点查看是否能 ssh 连接到其他机器，如果 ssh 无法连接，则需要参考 `conf/hosts.ini` 的注释中 `non-root` 用户示例部分，如下面第二步 `hosts.ini` 配置示例所示，而最终执行安装脚本 `install.sh` 时建议以 `root` 用户执行安装。

第一步，查看是否能 ssh 连接到其他机器，若无法连接，则参考第二步配置示例。相反，如果 root 用户能够 ssh 成功连接到其它机器，则可以参考 Installer 中默认的 root 用户配置方式。

```
root@192.168.0.3 # ssh 192.168.0.2
Warning: Permanently added 'node1,192.168.0.2' (ECDSA) to the list of known hosts.
root@192.168.0.2's password: 
Permission denied, please try again.
```
第二步，如下示例使用 3 台机器，参考以下示例修改主机配置文件 `hosts.ini`。

**hosts.ini 配置示例**
```ini
[all]
master ansible_connection=local  ip=192.168.0.1  ansible_user=ubuntu  ansible_become_pass=Qcloud@123
node1  ansible_host=192.168.0.2  ip=192.168.0.2  ansible_user=ubuntu  ansible_become_pass=Qcloud@123
node2  ansible_host=192.168.0.3  ip=192.168.0.3  ansible_user=ubuntu  ansible_become_pass=Qcloud@123

[kube-master]
master 	  	 

[kube-node]
node1 	 
node2

[etcd]
master	 

[k8s-cluster:children]
kube-node
kube-master 

```
### 安装失败相关问题

3、安装过程中，如果遇到安装失败并且发现错误日志中有这类信息：`The following packages have pending transactions`，这种情况应该如何处理？

![安装问题](/faq-installation-1.png)

答：这是因为有些 transactions 操作没有完成，可以连接到安装失败的节点上，依次执行下列命令，并重新执行 `install.sh` 脚本：

```shell
$ yum install yum-utils -y
$ yum-complete-transaction
$ yum-complete-transaction --cleanup-only
```

<!-- 3、如果在操作 [示例六](../../devops/jenkinsfile-in-scm) 运行流水线时，遇到 `Could not resolve host: github.com` 这类情况造成流水线运行失败了，应该如何处理？

![无法解析github](/could-not-resolve-github.png)

答：可能是由于主机环境对 GitHub 域名解析配置的问题，可以在后台编辑配置文件 `/etc/resolv.conf`，将其中的 `search domain` 这一行命令注释掉，流水线即可正常运行。如下所示注释了第二行 `search pek3.qingcloud.com`。

```
# Generated by NetworkManager
# search pek3.qingcloud.com  
nameserver 100.64.9.5
···
``` -->

### 流水线运行报错相关问题

4、创建 Jenkins 流水线后，运行时报错怎么处理？

![流水线报错](/faq-pipeline-error.png)

答：最快定位问题的方法即查看日志，点击 **查看日志**，具体查看出错的阶段 (stage) 输出的日志。比如，在 **push image** 这个阶段报错了，如下图中查看日志提示可能是 DockerHub 的用户名或密码错误。

![查看日志](/faq-pipeline-log.png)

5、运行流水线失败时，查看日志发现是 Docker 镜像 push 到 DockerHub 超时问题 (Timeout)，比如以下情况，要怎么处理？

![docker超时问题](/pipeline-docker-timeout.png)

答：可能由于网络问题造成，建议尝试再次运行该流水线。

### 如何查看 kubeconfig 文件

6、如何查看 Kubeconfig 文件？

用户可以通过打开 Kubectl UI 查看 Kubeconfig 文件，仅管理员或拥有 Kubectl UI 权限的用户有权限。

![查看 Kubeconfig 文件](/view-kubeconfig.png)

### 如何访问 Jenkins 服务端

7、如何访问和登录 Jenkins 服务端？

Installer 安装将会同时部署 Jenkins Dashboard，该服务暴露的端口 (NodePort) 为 `30180`，确保外网流量能够正常通过该端口，然后访问公网 IP 和端口号 (${EIP}:${NODEPORT}) 即可。Jenkins 已对接了 KubeSphere 的 LDAP，因此可使用用户名 `admin` 和 KubeSphere 集群管理员的密码登录 Jenkins Dashboard。

> 说明：
> 若您在使用中遇到任何产品相关的问题，欢迎在 [GitHub Issue](https://github.com/kubesphere/docs.kubesphere.io/issues) 提问。