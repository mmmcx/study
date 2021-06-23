# Kubernetes相关

# 什么是 Kubernetes

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/#本节视频)本节视频

[【（千锋教育）服务网格化 Service Mesh】Kubernetes-简介](https://www.bilibili.com/video/av52359802/?p=2)

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/#概述)概述

![img](https://www.funtl.com/assets1/clipart1469859.png)

Kubernetes 是 **Google 2014 年创建管理的**，是 Google 10 多年大规模容器管理技术 Borg 的开源版本。

Kubernetes 是容器集群管理系统，是一个开源的平台，可以实现容器集群的自动化部署、自动扩缩容、维护等功能。使用 Kubernetes 我们可以：

- 快速部署应用
- 快速扩展应用
- 无缝对接新的应用功能
- 节省资源，优化硬件资源的使用

Kubernetes 的目标是促进完善组件和工具的生态系统，以减轻应用程序在公有云或私有云中运行的负担。

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/#特点)特点

- **可移植：** 支持公有云，私有云，混合云，多重云（多个公共云）
- **可扩展：** 模块化，插件化，可挂载，可组合
- **自动化：** 自动部署，自动重启，自动复制，自动伸缩/扩展

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/#从传统到容器化部署)从传统到容器化部署

![img](https://www.funtl.com/assets1/why_containers.svg)

### [#](https://www.funtl.com/zh/service-mesh-kubernetes/#传统的部署方式)传统的部署方式

传统的应用部署方式是通过插件或脚本来安装应用。这样做的缺点是应用的运行、配置、管理、所有生存周期将与当前操作系统绑定，这样做并不利于应用的升级更新/回滚等操作，当然也可以通过创建虚机的方式来实现某些功能，但是虚拟机非常重，并不利于可移植性。

### [#](https://www.funtl.com/zh/service-mesh-kubernetes/#容器化部署的优势)容器化部署的优势

- **快速创建/部署应用：** 与虚拟机相比，容器镜像的创建更加容易。
- **持续开发、集成和部署：** 提供可靠且频繁的容器镜像构建/部署，并使用快速和简单的回滚(由于镜像不可变性)。
- **开发和运行相分离：** 在 build 或者 release 阶段创建容器镜像，使得应用和基础设施解耦。
- **开发，测试和生产环境一致性：** 在本地或外网（生产环境）运行的一致性。
- **云平台或其他操作系统：** 可以在 Ubuntu、RHEL、CoreOS、on-prem、Google Container Engine 或其它任何环境中运行。
- **分布式，弹性，微服务化：** 应用程序分为更小的、独立的部件，可以动态部署和管理。
- **资源隔离**
- **资源利用更高效**

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/#为什么需要-kubernetes)为什么需要 Kubernetes

可以在物理或虚拟机的 Kubernetes 集群上运行容器化应用，Kubernetes 能提供一个以 **“容器为中心的基础架构”**，满足在生产环境中运行应用的一些常见需求，如：

- 多个进程协同工作
- 存储系统挂载
- 应用健康检查
- 应用实例的复制
- 自动伸缩/扩展
- 注册与发现
- 负载均衡
- 滚动更新
- 资源监控
- 日志访问
- 调试应用程序
- 提供认证和授权



#  Kubernetes 安装前的准备

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/安装前的准备.html#本节视频)本节视频

[【（千锋教育）服务网格化 Service Mesh】Kubernetes-安装前的准备](https://www.bilibili.com/video/av52359802/?p=3)

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/安装前的准备.html#概述)概述

本次安装采用 Ubuntu Server X64 18.04 LTS 版本安装 kubernetes 集群环境，集群节点为 1 主 2 从模式，此次对虚拟机会有些基本要求，如下：

- OS：Ubuntu Server X64 18.04 LTS（16.04 版本步骤相同，再之前则不同）
- CPU：最低要求，1 CPU 2 核
- 内存：最低要求，2GB
- 磁盘：最低要求，20GB

创建三台虚拟机，分别命名如下：

- Ubuntu Server 18.04 X64 Kubernetes Master
- Ubuntu Server 18.04 X64 Kubernetes Slave1
- Ubuntu Server 18.04 X64 Kubernetes Slave2

对虚拟机系统的配置：

- 关闭交换空间：`sudo swapoff -a`
- 避免开机启动交换空间：注释 `/etc/fstab` 中的 `swap`
- 关闭防火墙：`ufw disable`

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/安装前的准备.html#使用-apt-安装-docker)使用 APT 安装 Docker

### [#](https://www.funtl.com/zh/service-mesh-kubernetes/安装前的准备.html#安装)安装

```bash
# 更新软件源
sudo apt-get update
# 安装所需依赖
sudo apt-get -y install apt-transport-https ca-certificates curl software-properties-common
# 安装 GPG 证书
curl -fsSL http://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | sudo apt-key add -
# 新增软件源信息
sudo add-apt-repository "deb [arch=amd64] http://mirrors.aliyun.com/docker-ce/linux/ubuntu $(lsb_release -cs) stable"
# 再次更新软件源
sudo apt-get -y update
# 安装 Docker CE 版
sudo apt-get -y install docker-ce
```

### [#](https://www.funtl.com/zh/service-mesh-kubernetes/安装前的准备.html#验证)验证

```bash
docker version
Client:
 Version:           18.09.6
 API version:       1.39
 Go version:        go1.10.8
 Git commit:        481bc77
 Built:             Sat May  4 02:35:57 2019
 OS/Arch:           linux/amd64
 Experimental:      false

Server: Docker Engine - Community
 Engine:
  Version:          18.09.6
  API version:      1.39 (minimum version 1.12)
  Go version:       go1.10.8
  Git commit:       481bc77
  Built:            Sat May  4 01:59:36 2019
  OS/Arch:          linux/amd64
  Experimental:     false
```

### [#](https://www.funtl.com/zh/service-mesh-kubernetes/安装前的准备.html#配置加速器)配置加速器

对于使用 **systemd** 的系统，请在 `/etc/docker/daemon.json` 中写入如下内容（如果文件不存在请新建该文件）

```json
{
  "registry-mirrors": [
    "https://registry.docker-cn.com"
  ]
}
```



> 注意，一定要保证该文件符合 JSON 规范，否则 Docker 将不能启动。

验证加速器是否配置成功：

```bash
sudo systemctl restart docker
docker info
...
# 出现如下语句即表示配置成功
Registry Mirrors:
 https://registry.docker-cn.com/
...
```

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/安装前的准备.html#修改主机名)修改主机名

在同一局域网中主机名不应该相同，所以我们需要做修改，下列操作步骤为修改 **18.04** 版本的 Hostname，如果是 16.04 或以下版本则直接修改 `/etc/hostname` 里的名称即可

**查看当前 Hostname**

```bash
# 查看当前主机名
hostnamectl
# 显示如下内容
   Static hostname: ubuntu
         Icon name: computer-vm
           Chassis: vm
        Machine ID: 33011e0a95094672b99a198eff07f652
           Boot ID: dc856039f0d24164a9f8a50c506be96d
    Virtualization: vmware
  Operating System: Ubuntu 18.04.2 LTS
            Kernel: Linux 4.15.0-48-generic
      Architecture: x86-64
```

**修改 Hostname**

```bash
# 使用 hostnamectl 命令修改，其中 kubernetes-master 为新的主机名
hostnamectl set-hostname kubernetes-master
```

**修改 cloud.cfg**

如果 `cloud-init package` 安装了，需要修改 `cloud.cfg` 文件。该软件包通常缺省安装用于处理 cloud

```bash
# 如果有该文件
vi /etc/cloud/cloud.cfg

# 该配置默认为 false，修改为 true 即可
preserve_hostname: true
```

**验证**

```bash
root@kubernetes-master:~# hostnamectl
   Static hostname: kubernetes-master
         Icon name: computer-vm
           Chassis: vm
        Machine ID: 33011e0a95094672b99a198eff07f652
           Boot ID: 8c0fd75d08c644abaad3df565e6e4cbd
    Virtualization: vmware
  Operating System: Ubuntu 18.04.2 LTS
            Kernel: Linux 4.15.0-48-generic
      Architecture: x86-64
```



# 安装 kubeadm

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/安装-kubeadm.html#本节视频)本节视频

[【（千锋教育）服务网格化 Service Mesh】Kubernetes-安装 kubeadm](https://www.bilibili.com/video/av52359802/?p=4)

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/安装-kubeadm.html#概述)概述

kubeadm 是 kubernetes 的集群安装工具，能够快速安装 kubernetes 集群。

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/安装-kubeadm.html#配置软件源)配置软件源

```bash
# 安装系统工具
apt-get update && apt-get install -y apt-transport-https
# 安装 GPG 证书
curl https://mirrors.aliyun.com/kubernetes/apt/doc/apt-key.gpg | apt-key add -
# 写入软件源；注意：我们用系统代号为 bionic，但目前阿里云不支持，所以沿用 16.04 的 xenial
cat << EOF >/etc/apt/sources.list.d/kubernetes.list
> deb https://mirrors.aliyun.com/kubernetes/apt/ kubernetes-xenial main
> EOF
```

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/安装-kubeadm.html#安装-kubeadm，kubelet，kubectl)安装 kubeadm，kubelet，kubectl

```bash
# 安装
apt-get update  
apt-get install -y kubelet kubeadm kubectl

# 安装过程如下，注意 kubeadm 的版本号
Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following additional packages will be installed:
  conntrack cri-tools kubernetes-cni socat
The following NEW packages will be installed:
  conntrack cri-tools kubeadm kubectl kubelet kubernetes-cni socat
0 upgraded, 7 newly installed, 0 to remove and 96 not upgraded.
Need to get 50.6 MB of archives.
After this operation, 290 MB of additional disk space will be used.
Get:1 http://mirrors.aliyun.com/ubuntu bionic/main amd64 conntrack amd64 1:1.4.4+snapshot20161117-6ubuntu2 [30.6 kB]
Get:2 http://mirrors.aliyun.com/ubuntu bionic/main amd64 socat amd64 1.7.3.2-2ubuntu2 [342 kB]
Get:3 https://mirrors.aliyun.com/kubernetes/apt kubernetes-xenial/main amd64 cri-tools amd64 1.12.0-00 [5,343 kB]
Get:4 https://mirrors.aliyun.com/kubernetes/apt kubernetes-xenial/main amd64 kubernetes-cni amd64 0.7.5-00 [6,473 kB]
Get:5 https://mirrors.aliyun.com/kubernetes/apt kubernetes-xenial/main amd64 kubelet amd64 1.14.1-00 [21.5 MB]
Get:6 https://mirrors.aliyun.com/kubernetes/apt kubernetes-xenial/main amd64 kubectl amd64 1.14.1-00 [8,806 kB]
Get:7 https://mirrors.aliyun.com/kubernetes/apt kubernetes-xenial/main amd64 kubeadm amd64 1.14.1-00 [8,150 kB]
Fetched 50.6 MB in 5s (9,912 kB/s) 
Selecting previously unselected package conntrack.
(Reading database ... 67205 files and directories currently installed.)
Preparing to unpack .../0-conntrack_1%3a1.4.4+snapshot20161117-6ubuntu2_amd64.deb ...
Unpacking conntrack (1:1.4.4+snapshot20161117-6ubuntu2) ...
Selecting previously unselected package cri-tools.
Preparing to unpack .../1-cri-tools_1.12.0-00_amd64.deb ...
Unpacking cri-tools (1.12.0-00) ...
Selecting previously unselected package kubernetes-cni.
Preparing to unpack .../2-kubernetes-cni_0.7.5-00_amd64.deb ...
Unpacking kubernetes-cni (0.7.5-00) ...
Selecting previously unselected package socat.
Preparing to unpack .../3-socat_1.7.3.2-2ubuntu2_amd64.deb ...
Unpacking socat (1.7.3.2-2ubuntu2) ...
Selecting previously unselected package kubelet.
Preparing to unpack .../4-kubelet_1.14.1-00_amd64.deb ...
Unpacking kubelet (1.14.1-00) ...
Selecting previously unselected package kubectl.
Preparing to unpack .../5-kubectl_1.14.1-00_amd64.deb ...
Unpacking kubectl (1.14.1-00) ...
Selecting previously unselected package kubeadm.
Preparing to unpack .../6-kubeadm_1.14.1-00_amd64.deb ...
Unpacking kubeadm (1.14.1-00) ...
Setting up conntrack (1:1.4.4+snapshot20161117-6ubuntu2) ...
Setting up kubernetes-cni (0.7.5-00) ...
Setting up cri-tools (1.12.0-00) ...
Setting up socat (1.7.3.2-2ubuntu2) ...
Setting up kubelet (1.14.1-00) ...
Created symlink /etc/systemd/system/multi-user.target.wants/kubelet.service → /lib/systemd/system/kubelet.service.
Setting up kubectl (1.14.1-00) ...
Processing triggers for man-db (2.8.3-2ubuntu0.1) ...
# 注意这里的版本号，我们使用的是 kubernetes v1.14.1
Setting up kubeadm (1.14.1-00) ...

# 设置 kubelet 自启动，并启动 kubelet
systemctl enable kubelet && systemctl start kubelet
```

- kubeadm：用于初始化 Kubernetes 集群
- kubectl：Kubernetes 的命令行工具，主要作用是部署和管理应用，查看各种资源，创建，删除和更新组件
- kubelet：主要负责启动 Pod 和容器



# 配置 kubeadm

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/配置-kubeadm.html#本节视频)本节视频

[【（千锋教育）服务网格化 Service Mesh】Kubernetes-配置 kubeadm](https://www.bilibili.com/video/av52359802/?p=5)

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/配置-kubeadm.html#概述)概述

安装 kubernetes 主要是安装它的各个镜像，而 kubeadm 已经为我们集成好了运行 kubernetes 所需的基本镜像。但由于国内的网络原因，在搭建环境时，无法拉取到这些镜像。此时我们只需要修改为阿里云提供的镜像服务即可解决该问题。

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/配置-kubeadm.html#创建并修改配置)创建并修改配置

```bash
# 导出配置文件
kubeadm config print init-defaults --kubeconfig ClusterConfiguration > kubeadm.yml
```

1
2

```yaml
# 修改配置为如下内容
apiVersion: kubeadm.k8s.io/v1beta1
bootstrapTokens:
- groups:
  - system:bootstrappers:kubeadm:default-node-token
  token: abcdef.0123456789abcdef
  ttl: 24h0m0s
  usages:
  - signing
  - authentication
kind: InitConfiguration
localAPIEndpoint:
  # 修改为主节点 IP
  advertiseAddress: 192.168.141.130
  bindPort: 6443
nodeRegistration:
  criSocket: /var/run/dockershim.sock
  name: kubernetes-master
  taints:
  - effect: NoSchedule
    key: node-role.kubernetes.io/master
---
apiServer:
  timeoutForControlPlane: 4m0s
apiVersion: kubeadm.k8s.io/v1beta1
certificatesDir: /etc/kubernetes/pki
clusterName: kubernetes
controlPlaneEndpoint: ""
controllerManager: {}
dns:
  type: CoreDNS
etcd:
  local:
    dataDir: /var/lib/etcd
# 国内不能访问 Google，修改为阿里云
imageRepository: registry.aliyuncs.com/google_containers
kind: ClusterConfiguration
# 修改版本号
kubernetesVersion: v1.14.1
networking:
  dnsDomain: cluster.local
  # 配置成 Calico 的默认网段
  podSubnet: "192.168.0.0/16"
  serviceSubnet: 10.96.0.0/12
scheduler: {}
---
# 开启 IPVS 模式
apiVersion: kubeproxy.config.k8s.io/v1alpha1
kind: KubeProxyConfiguration
featureGates:
  SupportIPVSProxyMode: true
mode: ipvs
```

1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
50
51
52

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/配置-kubeadm.html#查看和拉取镜像)查看和拉取镜像

```bash
# 查看所需镜像列表
kubeadm config images list --config kubeadm.yml
# 拉取镜像
kubeadm config images pull --config kubeadm.yml
```

![img](https://www.funtl.com/assets1/Lusifer_20190510144633.png)



# 使用 kubeadm 搭建 kubernetes 集群

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/使用-kubeadm.html#本节视频)本节视频

[【（千锋教育）服务网格化 Service Mesh】Kubernetes-使用 kubeadm](https://www.bilibili.com/video/av52359802/?p=6)

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/使用-kubeadm.html#安装-kubernetes-主节点)安装 kubernetes 主节点

执行以下命令初始化主节点，该命令指定了初始化时需要使用的配置文件，其中添加 `--experimental-upload-certs` 参数可以在后续执行加入节点时自动分发证书文件。追加的 `tee kubeadm-init.log` 用以输出日志。

```bash
kubeadm init --config=kubeadm.yml --experimental-upload-certs | tee kubeadm-init.log

# 安装成功则会有如下输出
[init] Using Kubernetes version: v1.14.1
[preflight] Running pre-flight checks
        [WARNING IsDockerSystemdCheck]: detected "cgroupfs" as the Docker cgroup driver. The recommended driver is "systemd". Please follow the guide at https://kubernetes.io/docs/setup/cri/
[preflight] Pulling images required for setting up a Kubernetes cluster
[preflight] This might take a minute or two, depending on the speed of your internet connection
[preflight] You can also perform this action in beforehand using 'kubeadm config images pull'
[kubelet-start] Writing kubelet environment file with flags to file "/var/lib/kubelet/kubeadm-flags.env"
[kubelet-start] Writing kubelet configuration to file "/var/lib/kubelet/config.yaml"
[kubelet-start] Activating the kubelet service
[certs] Using certificateDir folder "/etc/kubernetes/pki"
[certs] Generating "ca" certificate and key
[certs] Generating "apiserver" certificate and key
[certs] apiserver serving cert is signed for DNS names [kubernetes-master kubernetes kubernetes.default kubernetes.default.svc kubernetes.default.svc.cluster.local] and IPs [10.96.0.1 192.168.141.130]
[certs] Generating "apiserver-kubelet-client" certificate and key
[certs] Generating "front-proxy-ca" certificate and key
[certs] Generating "front-proxy-client" certificate and key
[certs] Generating "etcd/ca" certificate and key
[certs] Generating "etcd/peer" certificate and key
[certs] etcd/peer serving cert is signed for DNS names [kubernetes-master localhost] and IPs [192.168.141.130 127.0.0.1 ::1]
[certs] Generating "etcd/server" certificate and key
[certs] etcd/server serving cert is signed for DNS names [kubernetes-master localhost] and IPs [192.168.141.130 127.0.0.1 ::1]
[certs] Generating "etcd/healthcheck-client" certificate and key
[certs] Generating "apiserver-etcd-client" certificate and key
[certs] Generating "sa" key and public key
[kubeconfig] Using kubeconfig folder "/etc/kubernetes"
[kubeconfig] Writing "admin.conf" kubeconfig file
[kubeconfig] Writing "kubelet.conf" kubeconfig file
[kubeconfig] Writing "controller-manager.conf" kubeconfig file
[kubeconfig] Writing "scheduler.conf" kubeconfig file
[control-plane] Using manifest folder "/etc/kubernetes/manifests"
[control-plane] Creating static Pod manifest for "kube-apiserver"
[control-plane] Creating static Pod manifest for "kube-controller-manager"
[control-plane] Creating static Pod manifest for "kube-scheduler"
[etcd] Creating static Pod manifest for local etcd in "/etc/kubernetes/manifests"
[wait-control-plane] Waiting for the kubelet to boot up the control plane as static Pods from directory "/etc/kubernetes/manifests". This can take up to 4m0s
[apiclient] All control plane components are healthy after 20.003326 seconds
[upload-config] storing the configuration used in ConfigMap "kubeadm-config" in the "kube-system" Namespace
[kubelet] Creating a ConfigMap "kubelet-config-1.14" in namespace kube-system with the configuration for the kubelets in the cluster
[upload-certs] Storing the certificates in ConfigMap "kubeadm-certs" in the "kube-system" Namespace
[upload-certs] Using certificate key:
2cd5b86c4905c54d68cc7dfecc2bf87195e9d5d90b4fff9832d9b22fc5e73f96
[mark-control-plane] Marking the node kubernetes-master as control-plane by adding the label "node-role.kubernetes.io/master=''"
[mark-control-plane] Marking the node kubernetes-master as control-plane by adding the taints [node-role.kubernetes.io/master:NoSchedule]
[bootstrap-token] Using token: abcdef.0123456789abcdef
[bootstrap-token] Configuring bootstrap tokens, cluster-info ConfigMap, RBAC Roles
[bootstrap-token] configured RBAC rules to allow Node Bootstrap tokens to post CSRs in order for nodes to get long term certificate credentials
[bootstrap-token] configured RBAC rules to allow the csrapprover controller automatically approve CSRs from a Node Bootstrap Token
[bootstrap-token] configured RBAC rules to allow certificate rotation for all node client certificates in the cluster
[bootstrap-token] creating the "cluster-info" ConfigMap in the "kube-public" namespace
[addons] Applied essential addon: CoreDNS
[addons] Applied essential addon: kube-proxy

Your Kubernetes control-plane has initialized successfully!

To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

Then you can join any number of worker nodes by running the following on each as root:

# 后面子节点加入需要如下命令
kubeadm join 192.168.141.130:6443 --token abcdef.0123456789abcdef \
    --discovery-token-ca-cert-hash sha256:cab7c86212535adde6b8d1c7415e81847715cfc8629bb1d270b601744d662515
```



> 注意：如果安装 kubernetes 版本和下载的镜像版本不统一则会出现 `timed out waiting for the condition` 错误。中途失败或是想修改配置可以使用 `kubeadm reset` 命令重置配置，再做初始化操作即可。

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/使用-kubeadm.html#配置-kubectl)配置 kubectl

```bash
mkdir -p $HOME/.kube
cp -i /etc/kubernetes/admin.conf $HOME/.kube/config

# 非 ROOT 用户执行
chown $(id -u):$(id -g) $HOME/.kube/config
```



## [#](https://www.funtl.com/zh/service-mesh-kubernetes/使用-kubeadm.html#验证是否成功)验证是否成功

```bash
kubectl get node

# 能够打印出节点信息即表示成功
NAME                STATUS     ROLES    AGE     VERSION
kubernetes-master   NotReady   master   8m40s   v1.14.1
```

至此主节点配置完成

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/使用-kubeadm.html#kubeadm-init-的执行过程)kubeadm init 的执行过程

- init：指定版本进行初始化操作
- preflight：初始化前的检查和下载所需要的 Docker 镜像文件
- kubelet-start：生成 kubelet 的配置文件 `var/lib/kubelet/config.yaml`，没有这个文件 kubelet 无法启动，所以初始化之前的 kubelet 实际上启动不会成功
- certificates：生成 Kubernetes 使用的证书，存放在 `/etc/kubernetes/pki` 目录中
- kubeconfig：生成 KubeConfig 文件，存放在 `/etc/kubernetes` 目录中，组件之间通信需要使用对应文件
- control-plane：使用 `/etc/kubernetes/manifest` 目录下的 YAML 文件，安装 Master 组件
- etcd：使用 `/etc/kubernetes/manifest/etcd.yaml` 安装 Etcd 服务
- wait-control-plane：等待 control-plan 部署的 Master 组件启动
- apiclient：检查 Master 组件服务状态。
- uploadconfig：更新配置
- kubelet：使用 configMap 配置 kubelet
- patchnode：更新 CNI 信息到 Node 上，通过注释的方式记录
- mark-control-plane：为当前节点打标签，打了角色 Master，和不可调度标签，这样默认就不会使用 Master 节点来运行 Pod
- bootstrap-token：生成 token 记录下来，后边使用 `kubeadm join` 往集群中添加节点时会用到
- addons：安装附加组件 CoreDNS 和 kube-proxy



# 使用 kubeadm 配置 slave 节点

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/配置-Slave.html#本节视频)本节视频

[【（千锋教育）服务网格化 Service Mesh】Kubernetes-配置 Slave](https://www.bilibili.com/video/av52359802/?p=7)

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/配置-Slave.html#概述)概述

将 slave 节点加入到集群中很简单，只需要在 slave 服务器上安装 kubeadm，kubectl，kubelet 三个工具，然后使用 `kubeadm join` 命令加入即可。准备工作如下：

- 修改主机名
- 配置软件源
- 安装三个工具

由于之前章节已经说明了操作步骤，此处不再赘述。

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/配置-Slave.html#将-slave-加入到集群)将 slave 加入到集群

```bash
kubeadm join 192.168.141.130:6443 --token abcdef.0123456789abcdef --discovery-token-ca-cert-hash sha256:cab7c86212535adde6b8d1c7415e81847715cfc8629bb1d270b601744d662515

# 安装成功将看到如下信息
[preflight] Running pre-flight checks
        [WARNING IsDockerSystemdCheck]: detected "cgroupfs" as the Docker cgroup driver. The recommended driver is "systemd". Please follow the guide at https://kubernetes.io/docs/setup/cri/
[preflight] Reading configuration from the cluster...
[preflight] FYI: You can look at this config file with 'kubectl -n kube-system get cm kubeadm-config -oyaml'
[kubelet-start] Downloading configuration for the kubelet from the "kubelet-config-1.14" ConfigMap in the kube-system namespace
[kubelet-start] Writing kubelet configuration to file "/var/lib/kubelet/config.yaml"
[kubelet-start] Writing kubelet environment file with flags to file "/var/lib/kubelet/kubeadm-flags.env"
[kubelet-start] Activating the kubelet service
[kubelet-start] Waiting for the kubelet to perform the TLS Bootstrap...

This node has joined the cluster:
* Certificate signing request was sent to apiserver and a response was received.
* The Kubelet was informed of the new secure connection details.

Run 'kubectl get nodes' on the control-plane to see this node join the cluster.
```

说明：

- token
  - 可以通过安装 master 时的日志查看 token 信息
  - 可以通过 `kubeadm token list` 命令打印出 token 信息
  - 如果 token 过期，可以使用 `kubeadm token create` 命令创建新的 token
- discovery-token-ca-cert-hash
  - 可以通过安装 master 时的日志查看 sha256 信息
  - 可以通过 `openssl x509 -pubkey -in /etc/kubernetes/pki/ca.crt | openssl rsa -pubin -outform der 2>/dev/null | openssl dgst -sha256 -hex | sed 's/^.* //'` 命令查看 sha256 信息

以上方式感谢群友 **停 驻** 提供。

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/配置-Slave.html#验证是否成功)验证是否成功

回到 master 服务器

```bash
kubectl get nodes

# 可以看到 slave 成功加入 master
NAME                STATUS     ROLES    AGE   VERSION
kubernetes-master   NotReady   master   9h    v1.14.1
kubernetes-slave1   NotReady   <none>   22s   v1.14.1
```

> 如果 slave 节点加入 master 时配置有问题可以在 slave 节点上使用 `kubeadm reset` 重置配置再使用 `kubeadm join` 命令重新加入即可。希望在 master 节点删除 node ，可以使用 `kubeadm delete nodes ` 删除。

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/配置-Slave.html#查看-pod-状态)查看 pod 状态

```bash
kubectl get pod -n kube-system -o wide

NAME                                        READY   STATUS    RESTARTS   AGE   IP                NODE                NOMINATED NODE   READINESS GATES
coredns-8686dcc4fd-gwrmb                    0/1     Pending   0          9h    <none>            <none>              <none>           <none>
coredns-8686dcc4fd-j6gfk                    0/1     Pending   0          9h    <none>            <none>              <none>           <none>
etcd-kubernetes-master                      1/1     Running   1          9h    192.168.141.130   kubernetes-master   <none>           <none>
kube-apiserver-kubernetes-master            1/1     Running   1          9h    192.168.141.130   kubernetes-master   <none>           <none>
kube-controller-manager-kubernetes-master   1/1     Running   1          9h    192.168.141.130   kubernetes-master   <none>           <none>
kube-proxy-496dr                            1/1     Running   0          17m   192.168.141.131   kubernetes-slave1   <none>           <none>
kube-proxy-rsnb6                            1/1     Running   1          9h    192.168.141.130   kubernetes-master   <none>           <none>
kube-scheduler-kubernetes-master            1/1     Running   1          9h    192.168.141.130   kubernetes-master   <none>           <none>
```

由此可以看出 coredns 尚未运行，此时我们还需要安装网络插件。



# 配置网络

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/配置网络.html#本节视频)本节视频

- [【（千锋教育）服务网格化 Service Mesh】Kubernetes-配置网络1](https://www.bilibili.com/video/av52359802/?p=8)
- [【（千锋教育）服务网格化 Service Mesh】Kubernetes-配置网络2](https://www.bilibili.com/video/av52359802/?p=9)

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/配置网络.html#概述)概述

容器网络是容器选择连接到其他容器、主机和外部网络的机制。容器的 runtime 提供了各种网络模式，每种模式都会产生不同的体验。例如，Docker 默认情况下可以为容器配置以下网络：

- **none：** 将容器添加到一个容器专门的网络堆栈中，没有对外连接。
- **host：** 将容器添加到主机的网络堆栈中，没有隔离。
- **default bridge：** 默认网络模式。每个容器可以通过 IP 地址相互连接。
- **自定义网桥：** 用户定义的网桥，具有更多的灵活性、隔离性和其他便利功能。

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/配置网络.html#什么是-cni)什么是 CNI

CNI(Container Network Interface) 是一个标准的，通用的接口。在容器平台，Docker，Kubernetes，Mesos 容器网络解决方案 flannel，calico，weave。只要提供一个标准的接口，就能为同样满足该协议的所有容器平台提供网络功能，而 CNI 正是这样的一个标准接口协议。

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/配置网络.html#kubernetes-中的-cni-插件)Kubernetes 中的 CNI 插件

CNI 的初衷是创建一个框架，用于在配置或销毁容器时动态配置适当的网络配置和资源。插件负责为接口配置和管理 IP 地址，并且通常提供与 IP 管理、每个容器的 IP 分配、以及多主机连接相关的功能。容器运行时会调用网络插件，从而在容器启动时分配 IP 地址并配置网络，并在删除容器时再次调用它以清理这些资源。

运行时或协调器决定了容器应该加入哪个网络以及它需要调用哪个插件。然后，插件会将接口添加到容器网络命名空间中，作为一个 veth 对的一侧。接着，它会在主机上进行更改，包括将 veth 的其他部分连接到网桥。再之后，它会通过调用单独的 IPAM（IP地址管理）插件来分配 IP 地址并设置路由。

在 Kubernetes 中，kubelet 可以在适当的时间调用它找到的插件，为通过 kubelet 启动的 pod进行自动的网络配置。

Kubernetes 中可选的 CNI 插件如下：

- Flannel
- Calico
- Canal
- Weave

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/配置网络.html#什么是-calico)什么是 Calico

Calico 为容器和虚拟机提供了安全的网络连接解决方案，并经过了大规模生产验证（在公有云和跨数千个集群节点中），可与 Kubernetes，OpenShift，Docker，Mesos，DC / OS 和 OpenStack 集成。

Calico 还提供网络安全规则的动态实施。使用 Calico 的简单策略语言，您可以实现对容器，虚拟机工作负载和裸机主机端点之间通信的细粒度控制。

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/配置网络.html#安装网络插件-calico)安装网络插件 Calico

> 注意：截止到文章发表日期 2019 年 05 月 11 日，Calico 官方版本为 3.7

参考官方文档安装：https://docs.projectcalico.org/v3.7/getting-started/kubernetes/

```bash
# 在 Master 节点操作即可
kubectl apply -f https://docs.projectcalico.org/v3.7/manifests/calico.yaml

# 安装时显示如下输出
configmap/calico-config created
customresourcedefinition.apiextensions.k8s.io/felixconfigurations.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/ipamblocks.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/blockaffinities.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/ipamhandles.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/ipamconfigs.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/bgppeers.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/bgpconfigurations.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/ippools.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/hostendpoints.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/clusterinformations.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/globalnetworkpolicies.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/globalnetworksets.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/networkpolicies.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/networksets.crd.projectcalico.org created
clusterrole.rbac.authorization.k8s.io/calico-kube-controllers created
clusterrolebinding.rbac.authorization.k8s.io/calico-kube-controllers created
clusterrole.rbac.authorization.k8s.io/calico-node created
clusterrolebinding.rbac.authorization.k8s.io/calico-node created
daemonset.extensions/calico-node created
serviceaccount/calico-node created
deployment.extensions/calico-kube-controllers created
serviceaccount/calico-kube-controllers created
```

确认安装是否成功

```bash
watch kubectl get pods --all-namespaces

# 需要等待所有状态为 Running，注意时间可能较久，3 - 5 分钟的样子
Every 2.0s: kubectl get pods --all-namespaces                                                                                                    kubernetes-master: Fri May 10 18:16:51 2019

NAMESPACE     NAME                                        READY   STATUS    RESTARTS   AGE
kube-system   calico-kube-controllers-8646dd497f-g2lln    1/1     Running   0          50m
kube-system   calico-node-8jrtp                           1/1     Running   0          50m
kube-system   coredns-8686dcc4fd-mhwfn                    1/1     Running   0          51m
kube-system   coredns-8686dcc4fd-xsxwk                    1/1     Running   0          51m
kube-system   etcd-kubernetes-master                      1/1     Running   0          50m
kube-system   kube-apiserver-kubernetes-master            1/1     Running   0          51m
kube-system   kube-controller-manager-kubernetes-master   1/1     Running   0          51m
kube-system   kube-proxy-p8mdw                            1/1     Running   0          51m
kube-system   kube-scheduler-kubernetes-master            1/1     Running   0          51m
```

至此基本环境已部署完毕。

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/配置网络.html#解决-imagepullbackoff)解决 ImagePullBackOff

在使用 `watch kubectl get pods --all-namespaces` 命令观察 Pods 状态时如果出现 `ImagePullBackOff` 无法 Running 的情况，请尝试使用如下步骤处理：

- Master 中删除 Nodes：`kubectl delete nodes `
- Slave 中重置配置：`kubeadm reset`
- Slave 重启计算机：`reboot`
- Slave 重新加入集群：`kubeadm join`

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/配置网络.html#附：配置固定-ip-和-dns)附：配置固定 IP 和 DNS

当关机后再启动虚拟机有时 IP 地址会自动更换，导致之前的配置不可用；配置完 Kubernetes 网络后虚拟机还会出现无法联网的情况，后经研究发现是 DNS 会被自动重写所致，Ubuntu Server 18.04 LTS 版本的 IP 和 DNS 配置也与之前的版本配置大相径庭，故在此说明下如何修改 IP 和 DNS

### [#](https://www.funtl.com/zh/service-mesh-kubernetes/配置网络.html#修改固定-ip)修改固定 IP

编辑 `vi /etc/netplan/50-cloud-init.yaml` 配置文件，注意这里的配置文件名未必和你机器上的相同，请根据实际情况修改。修改内容如下：

```yaml
network:
    ethernets:
        ens33:
          addresses: [192.168.141.134/24]
          gateway4: 192.168.141.2
          nameservers:
            addresses: [192.168.141.2]
    version: 2
```

使配置生效 `netplan apply`

### [#](https://www.funtl.com/zh/service-mesh-kubernetes/配置网络.html#修改-dns)修改 DNS

#### [#](https://www.funtl.com/zh/service-mesh-kubernetes/配置网络.html#方法一)方法一

- 停止 `systemd-resolved` 服务：`systemctl stop systemd-resolved`
- 修改 DNS：`vi /etc/resolv.conf`，将 `nameserver` 修改为如 `114.114.114.114` 可以正常使用的 DNS 地址

#### [#](https://www.funtl.com/zh/service-mesh-kubernetes/配置网络.html#方法二)方法二

```bash
vi /etc/systemd/resolved.conf
```

把 DNS 取消注释，添加 DNS，保存退出，重启即可

![img](https://www.funtl.com/assets1/Lusifer_20190602201826.png)



# 第一个 Kubernetes 容器

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/第一个-Kubernetes-容器.html#本节视频)本节视频

[【（千锋教育）服务网格化 Service Mesh】Kubernetes-运行第一个容器](https://www.bilibili.com/video/av52359802/?p=10)

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/第一个-Kubernetes-容器.html#检查组件运行状态)检查组件运行状态

```bash
kubectl get cs

# 输出如下
NAME                 STATUS    MESSAGE             ERROR
# 调度服务，主要作用是将 POD 调度到 Node
scheduler            Healthy   ok                  
# 自动化修复服务，主要作用是 Node 宕机后自动修复 Node 回到正常的工作状态
controller-manager   Healthy   ok                  
# 服务注册与发现
etcd-0               Healthy   {"health":"true"} 
```



## [#](https://www.funtl.com/zh/service-mesh-kubernetes/第一个-Kubernetes-容器.html#检查-master-状态)检查 Master 状态

```bash
kubectl cluster-info

# 输出如下
# 主节点状态
Kubernetes master is running at https://192.168.141.130:6443
# DNS 状态
KubeDNS is running at https://192.168.141.130:6443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
```

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/第一个-Kubernetes-容器.html#检查-nodes-状态)检查 Nodes 状态

```bash
kubectl get nodes

# 输出如下，STATUS 为 Ready 即为正常状态
NAME                STATUS   ROLES    AGE     VERSION
kubernetes-master   Ready    master   44h     v1.14.1
kubernetes-slave1   Ready    <none>   3h38m   v1.14.1
kubernetes-slave2   Ready    <none>   3h37m   v1.14.1
```

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/第一个-Kubernetes-容器.html#运行第一个容器实例)运行第一个容器实例

```bash
# 使用 kubectl 命令创建两个监听 80 端口的 Nginx Pod（Kubernetes 运行容器的最小单元）
kubectl run nginx --image=nginx --replicas=2 --port=80

# 输出如下
kubectl run --generator=deployment/apps.v1 is DEPRECATED and will be removed in a future version. Use kubectl run --generator=run-pod/v1 or kubectl create instead.
deployment.apps/nginx created
```

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/第一个-Kubernetes-容器.html#查看全部-pods-的状态)查看全部 Pods 的状态

```bash
kubectl get pods

# 输出如下，需要等待一小段实践，STATUS 为 Running 即为运行成功
NAME                     READY   STATUS    RESTARTS   AGE
nginx-755464dd6c-qnmwp   1/1     Running   0          90m
nginx-755464dd6c-shqrp   1/1     Running   0          90m
```

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/第一个-Kubernetes-容器.html#查看已部署的服务)查看已部署的服务

```bash
kubectl get deployment

# 输出如下
NAME    READY   UP-TO-DATE   AVAILABLE   AGE
nginx   2/2     2            2           91m
```

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/第一个-Kubernetes-容器.html#映射服务，让用户可以访问)映射服务，让用户可以访问

```bash
kubectl expose deployment nginx --port=80 --type=LoadBalancer

# 输出如下
service/nginx exposed
```

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/第一个-Kubernetes-容器.html#查看已发布的服务)查看已发布的服务

```bash
kubectl get services

# 输出如下
NAME         TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
kubernetes   ClusterIP      10.96.0.1        <none>        443/TCP        44h
# 由此可见，Nginx 服务已成功发布并将 80 端口映射为 31738
nginx        LoadBalancer   10.108.121.244   <pending>     80:31738/TCP   88m
```

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/第一个-Kubernetes-容器.html#查看服务详情)查看服务详情

```bash
kubectl describe service nginx

# 输出如下
Name:                     nginx
Namespace:                default
Labels:                   run=nginx
Annotations:              <none>
Selector:                 run=nginx
Type:                     LoadBalancer
IP:                       10.108.121.244
Port:                     <unset>  80/TCP
TargetPort:               80/TCP
NodePort:                 <unset>  31738/TCP
Endpoints:                192.168.17.5:80,192.168.8.134:80
Session Affinity:         None
External Traffic Policy:  Cluster
Events:                   <none>
```

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/第一个-Kubernetes-容器.html#验证是否成功)验证是否成功

通过浏览器访问 Master 服务器

```text
http://192.168.141.130:31738/
```

此时 Kubernetes 会以负载均衡的方式访问部署的 Nginx 服务，能够正常看到 Nginx 的欢迎页即表示成功。容器实际部署在其它 Node 节点上，通过访问 Node 节点的 IP:Port 也是可以的。

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/第一个-Kubernetes-容器.html#停止服务)停止服务

```bash
kubectl delete deployment nginx

# 输出如下
deployment.extensions "nginx" deleted
```



```bash
kubectl delete service nginx

# 输出如下
service "nginx" deleted
```



# 概念总结

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/概念总结.html#本节视频)本节视频

[【（千锋教育）服务网格化 Service Mesh】Kubernetes-概念总结](https://www.bilibili.com/video/av52359802/?p=11)

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/概念总结.html#什么是-kubernetes)什么是 Kubernetes

Kubernetes 是一个开源的 Docker 容器编排系统，它可以调度计算集群的节点，动态管理上面的作业，保证它们按用户期望的状态运行。通过使用「labels」和「pods」的概念，Kubernetes 将应用按逻辑单元进行分组，方便管理和服务发现。

![整体架构](https://www.funtl.com/assets1/Lusifer_20190531050832.png)

- **pods：** 是一组紧密关联的容器集合，它们共享 IPC(进程间通信)、Network(网络) 和 UTS namespace(UTS 命名空间是 Linux 命名空间的一个子系统，主要作用是完成对容器 Hostname 和 Domain 的隔离，同时保存内核名称、版本、以及底层体系结构类型等信息)，是 Kubernetes 调度的基本单位。
- **labels：** 键值对(key/value)标签，可以被关联到如 Pod 这样的对象上，主要作用是给用户一个直观的感受，比如这个 Pod 是用来放置数据库的
- **GUI：** 用户图形界面，可以是 Web 用户界面，比如使用 `kubernetes-dashboard` 组件，用户可以通过 Dashboard 在 Kubernetes 集群中部署容器化的应用，可以查看集群中应用的运行情况，同时也能够基于 Dashboard 创建或修改部署、任务、服务等 Kubernetes 的资源。通过部署向导，用户能够对部署进行扩缩容，进行滚动更新、重启 Pod 和部署新应用。当然，通过 Dashboard 也能够查看 Kubernetes 资源的状态
- **kubectl：** 用于管理 Kubernetes 集群的命令行工具
- **kube-apiserver：** 提供了资源操作的唯一入口，并提供认证、授权、访问控制、API 注册和发现等机制
- **Kubernetes Master：** Kubernetes 集群主节点，主要由 `kube-apiserver`、`kube-scheduler`、`kube-controller-manager`、`etcd` 四个模块组成
- **Kubernetes Node：** Kubernetes 集群子节点，主要由 `kubelet`、`kube-proxy`、`runtime` 三个模块组成
- **Image Registry：** 镜像仓库，比如：Ducker HUB 或 Docker 私服

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/概念总结.html#kubernetes-master)Kubernetes Master

![img](https://www.funtl.com/assets1/Lusifer_20190531060523.png)

- **kube-apiserver：** 提供了资源操作的唯一入口，并提供认证、授权、访问控制、API 注册和发现等机制
- **kube-scheduler：** 负责资源的调度，按照预定的调度策略将 Pod 调度到相应的机器上
- **kube-controller-manager：** 负责维护集群的状态，比如故障检测、自动扩展、滚动更新等
- **etcd：** CoreOS 基于 Raft 开发的分布式 key-value 存储，可用于服务发现、共享配置以及一致性保障（如数据库选主、分布式锁等）

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/概念总结.html#kubernetes-node)Kubernetes Node

![img](https://www.funtl.com/assets1/Lusifer_20190531062141.png)

- **runtime：** 负责镜像管理以及 Pod 和容器的真正运行（CRI，Container Runtime Interface），默认的容器运行时为 Docker，还支持 RKT 容器
- **kubelet：** 负责维持容器的生命周期，同时也负责 Volume（CVI，Container Volume Interface）和网络（CNI，Container Network Interface）的管理
- **kube-proxy：** 负责为 Service 提供 cluster 内部的服务发现和负载均衡

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/概念总结.html#kubernetes-架构)Kubernetes 架构



# Kubernetes 高可用集群

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/高可用集群.html#本节视频)本节视频

- [【（千锋教育）服务网格化 Service Mesh】Kubernetes-高可用集群1](https://www.bilibili.com/video/av52359802/?p=12)
- [【（千锋教育）服务网格化 Service Mesh】Kubernetes-高可用集群2](https://www.bilibili.com/video/av52359802/?p=13)

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/高可用集群.html#概述)概述

在入门课程中我们部署的 Kubernetes 是 **集群模式**，但在实际生产中我们需要部署 **高可用集群** ，本章内容旨在指导大家如何部署 Kubernetes 高可用集群

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/高可用集群.html#统一环境配置)统一环境配置

### [#](https://www.funtl.com/zh/service-mesh-kubernetes/高可用集群.html#节点配置)节点配置

| 主机名               | IP              | 角色   | 系统                | CPU/内存 | 磁盘 |
| -------------------- | --------------- | ------ | ------------------- | -------- | ---- |
| kubernetes-master-01 | 192.168.141.150 | Master | Ubuntu Server 18.04 | 2核2G    | 20G  |
| kubernetes-master-02 | 192.168.141.151 | Master | Ubuntu Server 18.04 | 2核2G    | 20G  |
| kubernetes-master-03 | 192.168.141.152 | Master | Ubuntu Server 18.04 | 2核2G    | 20G  |
| kubernetes-node-01   | 192.168.141.160 | Node   | Ubuntu Server 18.04 | 2核4G    | 20G  |
| kubernetes-node-02   | 192.168.141.161 | Node   | Ubuntu Server 18.04 | 2核4G    | 20G  |
| kubernetes-node-03   | 192.168.141.162 | Node   | Ubuntu Server 18.04 | 2核4G    | 20G  |
| Kubernetes VIP       | 192.168.141.200 | -      | -                   | -        | -    |

### [#](https://www.funtl.com/zh/service-mesh-kubernetes/高可用集群.html#对操作系统的配置)对操作系统的配置

> 特别注意：以下步骤请在制作 VMware 镜像时一并完成，避免逐台安装的痛苦

#### [#](https://www.funtl.com/zh/service-mesh-kubernetes/高可用集群.html#关闭交换空间)关闭交换空间

```bash
swapoff -a
```

1

#### [#](https://www.funtl.com/zh/service-mesh-kubernetes/高可用集群.html#避免开机启动交换空间)避免开机启动交换空间

```bash
# 注释 swap 开头的行
vi /etc/fstab
```

1
2

#### [#](https://www.funtl.com/zh/service-mesh-kubernetes/高可用集群.html#关闭防火墙)关闭防火墙

```bash
ufw disable
```

1

#### [#](https://www.funtl.com/zh/service-mesh-kubernetes/高可用集群.html#配置-dns)配置 DNS

```bash
# 取消 DNS 行注释，并增加 DNS 配置如：114.114.114.114，修改后重启下计算机
vi /etc/systemd/resolved.conf
```

1
2

#### [#](https://www.funtl.com/zh/service-mesh-kubernetes/高可用集群.html#安装-docker)安装 Docker

```bash
# 更新软件源
sudo apt-get update
# 安装所需依赖
sudo apt-get -y install apt-transport-https ca-certificates curl software-properties-common
# 安装 GPG 证书
curl -fsSL http://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | sudo apt-key add -
# 新增软件源信息
sudo add-apt-repository "deb [arch=amd64] http://mirrors.aliyun.com/docker-ce/linux/ubuntu $(lsb_release -cs) stable"
# 再次更新软件源
sudo apt-get -y update
# 安装 Docker CE 版
sudo apt-get -y install docker-ce
```

1
2
3
4
5
6
7
8
9
10
11
12

#### [#](https://www.funtl.com/zh/service-mesh-kubernetes/高可用集群.html#配置-docker-加速器)配置 Docker 加速器

> 特别注意：国内镜像加速器可能会很卡，请替换成你自己阿里云镜像加速器，地址如：`https://yourself.mirror.aliyuncs.com`，在阿里云控制台的 **容器镜像服务 -> 镜像加速器** 菜单中可以找到

在 `/etc/docker/daemon.json` 中写入如下内容（如果文件不存在请新建该文件）

```json
{
  "registry-mirrors": [
    "https://registry.docker-cn.com"
  ]
}
```

1
2
3
4
5

#### [#](https://www.funtl.com/zh/service-mesh-kubernetes/高可用集群.html#安装-kubeadm，kubelet，kubectl)安装 kubeadm，kubelet，kubectl

```bash
# 安装系统工具
apt-get update && apt-get install -y apt-transport-https

# 安装 GPG 证书
curl https://mirrors.aliyun.com/kubernetes/apt/doc/apt-key.gpg | apt-key add -

# 写入软件源；注意：我们用系统代号为 bionic，但目前阿里云不支持，所以沿用 16.04 的 xenial
cat << EOF >/etc/apt/sources.list.d/kubernetes.list
> deb https://mirrors.aliyun.com/kubernetes/apt/ kubernetes-xenial main
> EOF

# 安装
apt-get update && apt-get install -y kubelet kubeadm kubectl
```



#### [#](https://www.funtl.com/zh/service-mesh-kubernetes/高可用集群.html#同步时间)同步时间

**设置时区**

```bash
dpkg-reconfigure tzdata
```

选择 **Asia（亚洲）**

![img](https://www.funtl.com/assets1/Lusifer_20190602220034.png)

选择 **Shanghai（上海）**

![img](https://www.funtl.com/assets1/Lusifer_20190602220202.png)

**时间同步**

```bash
# 安装 ntpdate
apt-get install ntpdate

# 设置系统时间与网络时间同步（cn.pool.ntp.org 位于中国的公共 NTP 服务器）
ntpdate cn.pool.ntp.org

# 将系统时间写入硬件时间
hwclock --systohc
```



**确认时间**

```bash
date

# 输出如下（自行对照与系统时间是否一致）
Sun Jun  2 22:02:35 CST 2019
```



#### [#](https://www.funtl.com/zh/service-mesh-kubernetes/高可用集群.html#配置-ipvs)配置 IPVS

```bash
# 安装系统工具
apt-get install -y ipset ipvsadm

# 配置并加载 IPVS 模块
mkdir -p /etc/sysconfig/modules/
vi /etc/sysconfig/modules/ipvs.modules

# 输入如下内容
#!/bin/bash
modprobe -- ip_vs
modprobe -- ip_vs_rr
modprobe -- ip_vs_wrr
modprobe -- ip_vs_sh
modprobe -- nf_conntrack_ipv4

# 执行脚本，注意：如果重启则需要重新运行该脚本
chmod 755 /etc/sysconfig/modules/ipvs.modules && bash /etc/sysconfig/modules/ipvs.modules && lsmod | grep -e ip_vs -e nf_conntrack_ipv4

# 执行脚本输出如下
ip_vs_sh               16384  0
ip_vs_wrr              16384  0
ip_vs_rr               16384  0
ip_vs                 147456  6 ip_vs_rr,ip_vs_sh,ip_vs_wrr
nf_conntrack_ipv4      16384  3
nf_defrag_ipv4         16384  1 nf_conntrack_ipv4
nf_conntrack          131072  8 xt_conntrack,nf_nat_masquerade_ipv4,nf_conntrack_ipv4,nf_nat,ipt_MASQUERADE,nf_nat_ipv4,nf_conntrack_netlink,ip_vs
libcrc32c              16384  4 nf_conntrack,nf_nat,raid456,ip_vs
```

#### [#](https://www.funtl.com/zh/service-mesh-kubernetes/高可用集群.html#配置内核参数)配置内核参数

```bash
# 配置参数
vi /etc/sysctl.d/k8s.conf

# 输入如下内容
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_nonlocal_bind = 1
net.ipv4.ip_forward = 1
vm.swappiness=0

# 应用参数
sysctl --system

# 应用参数输出如下（找到 Applying /etc/sysctl.d/k8s.conf 开头的日志）
* Applying /etc/sysctl.d/10-console-messages.conf ...
kernel.printk = 4 4 1 7
* Applying /etc/sysctl.d/10-ipv6-privacy.conf ...
* Applying /etc/sysctl.d/10-kernel-hardening.conf ...
kernel.kptr_restrict = 1
* Applying /etc/sysctl.d/10-link-restrictions.conf ...
fs.protected_hardlinks = 1
fs.protected_symlinks = 1
* Applying /etc/sysctl.d/10-lxd-inotify.conf ...
fs.inotify.max_user_instances = 1024
* Applying /etc/sysctl.d/10-magic-sysrq.conf ...
kernel.sysrq = 176
* Applying /etc/sysctl.d/10-network-security.conf ...
net.ipv4.conf.default.rp_filter = 1
net.ipv4.conf.all.rp_filter = 1
net.ipv4.tcp_syncookies = 1
* Applying /etc/sysctl.d/10-ptrace.conf ...
kernel.yama.ptrace_scope = 1
* Applying /etc/sysctl.d/10-zeropage.conf ...
vm.mmap_min_addr = 65536
* Applying /usr/lib/sysctl.d/50-default.conf ...
net.ipv4.conf.all.promote_secondaries = 1
net.core.default_qdisc = fq_codel
* Applying /etc/sysctl.d/99-sysctl.conf ...
* Applying /etc/sysctl.d/k8s.conf ...
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_nonlocal_bind = 1
net.ipv4.ip_forward = 1
vm.swappiness = 0
* Applying /etc/sysctl.conf ...
```



#### [#](https://www.funtl.com/zh/service-mesh-kubernetes/高可用集群.html#修改-cloud-cfg)修改 cloud.cfg

```bash
vi /etc/cloud/cloud.cfg

# 该配置默认为 false，修改为 true 即可
preserve_hostname: true
```



## [#](https://www.funtl.com/zh/service-mesh-kubernetes/高可用集群.html#单独节点配置)单独节点配置

> 特别注意：为 Master 和 Node 节点单独配置对应的 **IP** 和 **主机名**

### [#](https://www.funtl.com/zh/service-mesh-kubernetes/高可用集群.html#配置-ip)配置 IP

编辑 `vi /etc/netplan/50-cloud-init.yaml` 配置文件，修改内容如下

```yaml
network:
    ethernets:
        ens33:
          # 我的 Master 是 150 - 152，Node 是 160 - 162
          addresses: [192.168.141.150/24]
          gateway4: 192.168.141.2
          nameservers:
            addresses: [192.168.141.2]
    version: 2
```



使用 `netplan apply` 命令让配置生效

### [#](https://www.funtl.com/zh/service-mesh-kubernetes/高可用集群.html#配置主机名)配置主机名

```bash
# 修改主机名
hostnamectl set-hostname kubernetes-master-01

# 配置 hosts
cat >> /etc/hosts << EOF
192.168.141.150 kubernetes-master-01
EOF
```



## [#](https://www.funtl.com/zh/service-mesh-kubernetes/高可用集群.html#安装-haproxy-keepalived)安装 HAProxy + Keepalived

### [#](https://www.funtl.com/zh/service-mesh-kubernetes/高可用集群.html#概述-2)概述

Kubernetes Master 节点运行组件如下：

- **kube-apiserver：** 提供了资源操作的唯一入口，并提供认证、授权、访问控制、API 注册和发现等机制
- **kube-scheduler：** 负责资源的调度，按照预定的调度策略将 Pod 调度到相应的机器上
- **kube-controller-manager：** 负责维护集群的状态，比如故障检测、自动扩展、滚动更新等
- **etcd：** CoreOS 基于 Raft 开发的分布式 key-value 存储，可用于服务发现、共享配置以及一致性保障（如数据库选主、分布式锁等）

`kube-scheduler` 和 `kube-controller-manager` 可以以集群模式运行，通过 leader 选举产生一个工作进程，其它进程处于阻塞模式。

**`kube-apiserver` 可以运行多个实例，但对其它组件需要提供统一的访问地址，本章节部署 Kubernetes 高可用集群实际就是利用 HAProxy + Keepalived 配置该组件**

配置的思路就是利用 HAProxy + Keepalived 实现 `kube-apiserver` 虚拟 IP 访问从而实现高可用和负载均衡，拆解如下：

- Keepalived 提供 `kube-apiserver` 对外服务的虚拟 IP（VIP）
- HAProxy 监听 Keepalived VIP
- 运行 Keepalived 和 HAProxy 的节点称为 LB（负载均衡） 节点
- Keepalived 是一主多备运行模式，故至少需要两个 LB 节点
- Keepalived 在运行过程中周期检查本机的 HAProxy 进程状态，如果检测到 HAProxy 进程异常，则触发重新选主的过程，VIP 将飘移到新选出来的主节点，从而实现 VIP 的高可用
- 所有组件（如 kubeclt、apiserver、controller-manager、scheduler 等）都通过 VIP +HAProxy 监听的 6444 端口访问 `kube-apiserver` 服务（**注意：`kube-apiserver` 默认端口为 6443，为了避免冲突我们将 HAProxy 端口设置为 6444，其它组件都是通过该端口统一请求 apiserver**）

![负载均衡架构图](https://www.funtl.com/assets1/20190427104124213.png)

### [#](https://www.funtl.com/zh/service-mesh-kubernetes/高可用集群.html#创建-haproxy-启动脚本)创建 HAProxy 启动脚本

> 该步骤在 `kubernetes-master-01` 执行

```bash
mkdir -p /usr/local/kubernetes/lb
vi /usr/local/kubernetes/lb/start-haproxy.sh

# 输入内容如下
#!/bin/bash
# 修改为你自己的 Master 地址
MasterIP1=192.168.141.150
MasterIP2=192.168.141.151
MasterIP3=192.168.141.152
# 这是 kube-apiserver 默认端口，不用修改
MasterPort=6443

# 容器将 HAProxy 的 6444 端口暴露出去
docker run -d --restart=always --name HAProxy-K8S -p 6444:6444 \
        -e MasterIP1=$MasterIP1 \
        -e MasterIP2=$MasterIP2 \
        -e MasterIP3=$MasterIP3 \
        -e MasterPort=$MasterPort \
        wise2c/haproxy-k8s

# 设置权限
chmod +x start-haproxy.sh
```



### [#](https://www.funtl.com/zh/service-mesh-kubernetes/高可用集群.html#创建-keepalived-启动脚本)创建 Keepalived 启动脚本

> 该步骤在 `kubernetes-master-01` 执行

```bash
mkdir -p /usr/local/kubernetes/lb
vi /usr/local/kubernetes/lb/start-keepalived.sh

# 输入内容如下
#!/bin/bash
# 修改为你自己的虚拟 IP 地址
VIRTUAL_IP=192.168.141.200
# 虚拟网卡设备名
INTERFACE=ens33
# 虚拟网卡的子网掩码
NETMASK_BIT=24
# HAProxy 暴露端口，内部指向 kube-apiserver 的 6443 端口
CHECK_PORT=6444
# 路由标识符
RID=10
# 虚拟路由标识符
VRID=160
# IPV4 多播地址，默认 224.0.0.18
MCAST_GROUP=224.0.0.18

docker run -itd --restart=always --name=Keepalived-K8S \
        --net=host --cap-add=NET_ADMIN \
        -e VIRTUAL_IP=$VIRTUAL_IP \
        -e INTERFACE=$INTERFACE \
        -e CHECK_PORT=$CHECK_PORT \
        -e RID=$RID \
        -e VRID=$VRID \
        -e NETMASK_BIT=$NETMASK_BIT \
        -e MCAST_GROUP=$MCAST_GROUP \
        wise2c/keepalived-k8s

# 设置权限
chmod +x start-keepalived.sh
```



### [#](https://www.funtl.com/zh/service-mesh-kubernetes/高可用集群.html#复制脚本到其它-master-地址)复制脚本到其它 Master 地址

分别在 `kubernetes-master-02` 和 `kubernetes-master-03` 执行创建工作目录命令

```bash
mkdir -p /usr/local/kubernetes/lb
```

将 `kubernetes-master-01` 中的脚本拷贝至其它 Master

```bash
scp start-haproxy.sh start-keepalived.sh 192.168.141.151:/usr/local/kubernetes/lb
scp start-haproxy.sh start-keepalived.sh 192.168.141.152:/usr/local/kubernetes/lb
```



分别在 3 个 Master 中启动容器（执行脚本）

```bash
sh /usr/local/kubernetes/lb/start-haproxy.sh && sh /usr/local/kubernetes/lb/start-keepalived.sh
```



### [#](https://www.funtl.com/zh/service-mesh-kubernetes/高可用集群.html#验证是否成功)验证是否成功

#### [#](https://www.funtl.com/zh/service-mesh-kubernetes/高可用集群.html#查看容器)查看容器

```bash
docker ps

# 输出如下
CONTAINER ID        IMAGE                   COMMAND                  CREATED             STATUS              PORTS                    NAMES
f50df479ecae        wise2c/keepalived-k8s   "/usr/bin/keepalived…"   About an hour ago   Up About an hour                             Keepalived-K8S
75066a7ed2fb        wise2c/haproxy-k8s      "/docker-entrypoint.…"   About an hour ago   Up About an hour    0.0.0.0:6444->6444/tcp   HAProxy-K8S
```



#### [#](https://www.funtl.com/zh/service-mesh-kubernetes/高可用集群.html#查看网卡绑定的虚拟-ip)查看网卡绑定的虚拟 IP

```bash
ip a | grep ens33

# 输出如下
2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    inet 192.168.141.151/24 brd 192.168.141.255 scope global ens33
    inet 192.168.141.200/24 scope global secondary ens33
```



> 特别注意：Keepalived 会对 HAProxy 监听的 6444 端口进行检测，如果检测失败即认定本机 HAProxy 进程异常，会将 VIP 漂移到其他节点，所以无论本机 Keepalived 容器异常或 HAProxy 容器异常都会导致 VIP 漂移到其他节点

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/高可用集群.html#部署-kubernetes-集群)部署 Kubernetes 集群

### [#](https://www.funtl.com/zh/service-mesh-kubernetes/高可用集群.html#初始化-master)初始化 Master

- 创建工作目录并导出配置文件

```bash
# 创建工作目录
mkdir -p /usr/local/kubernetes/cluster

# 导出配置文件到工作目录
kubeadm config print init-defaults --kubeconfig ClusterConfiguration > kubeadm.yml
```

1
2
3
4
5

- 修改配置文件

```yaml
apiVersion: kubeadm.k8s.io/v1beta1
bootstrapTokens:
- groups:
  - system:bootstrappers:kubeadm:default-node-token
  token: abcdef.0123456789abcdef
  ttl: 24h0m0s
  usages:
  - signing
  - authentication
kind: InitConfiguration
localAPIEndpoint:
  # 修改为主节点 IP
  advertiseAddress: 192.168.141.150
  bindPort: 6443
nodeRegistration:
  criSocket: /var/run/dockershim.sock
  name: kubernetes-master
  taints:
  - effect: NoSchedule
    key: node-role.kubernetes.io/master
---
apiServer:
  timeoutForControlPlane: 4m0s
apiVersion: kubeadm.k8s.io/v1beta1
certificatesDir: /etc/kubernetes/pki
clusterName: kubernetes
# 配置 Keepalived 地址和 HAProxy 端口
controlPlaneEndpoint: "192.168.141.200:6444"
controllerManager: {}
dns:
  type: CoreDNS
etcd:
  local:
    dataDir: /var/lib/etcd
# 国内不能访问 Google，修改为阿里云
imageRepository: registry.aliyuncs.com/google_containers
kind: ClusterConfiguration
# 修改版本号
kubernetesVersion: v1.14.2
networking:
  dnsDomain: cluster.local
  # 配置成 Calico 的默认网段
  podSubnet: "192.168.0.0/16"
  serviceSubnet: 10.96.0.0/12
scheduler: {}
---
# 开启 IPVS 模式
apiVersion: kubeproxy.config.k8s.io/v1alpha1
kind: KubeProxyConfiguration
featureGates:
  SupportIPVSProxyMode: true
mode: ipvs
```

- kubeadm 初始化

```bash
# kubeadm 初始化
kubeadm init --config=kubeadm.yml --experimental-upload-certs | tee kubeadm-init.log

# 配置 kubectl
mkdir -p $HOME/.kube
cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
chown $(id -u):$(id -g) $HOME/.kube/config

# 验证是否成功
kubectl get node
```



- 安装网络插件

```bash
# 安装 Calico
kubectl apply -f https://docs.projectcalico.org/v3.7/manifests/calico.yaml

# 验证安装是否成功
watch kubectl get pods --all-namespaces
```



### [#](https://www.funtl.com/zh/service-mesh-kubernetes/高可用集群.html#加入-master-节点)加入 Master 节点

从 `kubeadm-init.log` 中获取命令，分别将 `kubernetes-master-02` 和 `kubernetes-master-03` 加入 Master

```bash
# 以下为示例命令
kubeadm join 192.168.141.200:6444 --token abcdef.0123456789abcdef \
  --discovery-token-ca-cert-hash sha256:56d53268517c132ae81c868ce99c44be797148fb2923e59b49d73c99782ff21f \
  --experimental-control-plane --certificate-key c4d1525b6cce4b69c11c18919328c826f92e660e040a46f5159431d5ff0545bd
```

### [#](https://www.funtl.com/zh/service-mesh-kubernetes/高可用集群.html#加入-node-节点)加入 Node 节点

从 `kubeadm-init.log` 中获取命令，分别将 `kubernetes-node-01` 至 `kubernetes-node-03` 加入 Node

```bash
# 以下为示例命令
kubeadm join 192.168.141.200:6444 --token abcdef.0123456789abcdef \
    --discovery-token-ca-cert-hash sha256:56d53268517c132ae81c868ce99c44be797148fb2923e59b49d73c99782ff21f 
```



### [#](https://www.funtl.com/zh/service-mesh-kubernetes/高可用集群.html#验证集群状态)验证集群状态

- 查看 Node

```bash
kubectl get nodes -o wide
```

- 查看 Pod

```bash
kubectl -n kube-system get pod -o wide
```

- 查看 Service

```bash
kubectl -n kube-system get svc
```

- 验证 IPVS

查看 kube-proxy 日志，server_others.go:176] Using ipvs Proxier.

```bash
kubectl -n kube-system logs -f <kube-proxy 容器名>
```

- 查看代理规则

```bash
ipvsadm -ln
```

- 查看生效的配置

```bash
kubectl -n kube-system get cm kubeadm-config -oyaml
```

- 查看 etcd 集群

```bash
kubectl -n kube-system exec etcd-kubernetes-master-01 -- etcdctl \
	--endpoints=https://192.168.141.150:2379 \
	--ca-file=/etc/kubernetes/pki/etcd/ca.crt \
	--cert-file=/etc/kubernetes/pki/etcd/server.crt \
	--key-file=/etc/kubernetes/pki/etcd/server.key cluster-health

# 输出如下
member 1dfaf07371bb0cb6 is healthy: got healthy result from https://192.168.141.152:2379
member 2da85730b52fbeb2 is healthy: got healthy result from https://192.168.141.150:2379
member 6a3153eb4faaaffa is healthy: got healthy result from https://192.168.141.151:2379
cluster is healthy
```



### [#](https://www.funtl.com/zh/service-mesh-kubernetes/高可用集群.html#验证高可用)验证高可用

> 特别注意：Keepalived 要求至少 2 个备用节点，故想测试高可用至少需要 1 主 2 从模式验证，否则可能出现意想不到的问题

对任意一台 Master 机器执行关机操作

```bash
shutdown -h now
```



在任意一台 Master 节点上查看 Node 状态

```bash
kubectl get node

# 输出如下，除已关机那台状态为 NotReady 其余正常便表示成功
NAME                   STATUS   ROLES    AGE   VERSION
kubernetes-master-01   NotReady master   18m   v1.14.2
kubernetes-master-02   Ready    master   17m   v1.14.2
kubernetes-master-03   Ready    master   16m   v1.14.2
```



查看 VIP 漂移

```bash
ip a |grep ens33

# 输出如下
2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    inet 192.168.141.151/24 brd 192.168.141.255 scope global ens33
    inet 192.168.141.200/24 scope global secondary ens33
```



# 解决 Node 无法加入的问题

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/解决-Node-无法加入的问题.html#本节视频)本节视频

[【（千锋教育）服务网格化 Service Mesh】Kubernetes-解决 Node 无法加入的问题](https://www.bilibili.com/video/av52359802/?p=14)

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/解决-Node-无法加入的问题.html#问题描述)问题描述

当我们使用 `kubeadm join` 命令将 Node 节点加入集群时，你会发现所有 `kubectl` 命令均不可用（呈现阻塞状态，并不会返回响应结果），我们可以在 Node 节点中通过 `kubeadm reset` 命令将 Node 节点下线，此时回到 Master 节点再使用 `watch kubectl get pods --all-namespaces` 可以看到下图中报错了，`coredns-xxx-xxx` 状态为 `CrashLoopBackOff`

![img](https://www.funtl.com/assets1/Lusifer_20190604010905.png)

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/解决-Node-无法加入的问题.html#解决方案)解决方案

从上面的错误信息不难看出应该是出现了网络问题，而我们在安装过程中只使用了一个网络插件 **Calico** ，那么该错误是不是由 Calico 引起的呢？带着这个疑问我们去到 Calico 官网再看一下它的说明，官网地址：https://docs.projectcalico.org/v3.7/getting-started/kubernetes/

在它的 Quickstart 里有两段话（属于特别提醒），截图如下：

![img](https://www.funtl.com/assets1/Lusifer_20190604013518.png)

上面这段话的主要意思是：当 `kubeadm` 安装完成后不要关机，继续完成后续的安装步骤；这也说明了安装 Kubernetes 的过程不要出现中断一口气搞定（不过这不是重点）(*￣rǒ￣)

![img](https://www.funtl.com/assets1/Lusifer_20190604014207.png)

上面这段话的主要意思是：如果你的网络在 `192.168.0.0/16` 网段中，则必须选择一个不同的 Pod 网络；恰巧咱们的网络范围（我虚拟机的 IP 范围是 `192.168.141.0/24`）和该网段重叠 (ノへ￣、)；好吧，当时做单节点集群时因为没啥问题而忽略了 ♪(^∇^*)

so，能够遇到这个问题主要是因为虚拟机 IP 范围刚好和 Calico 默认网段重叠导致的，所以想要解决这个问题，咱们就需要修改 Calico 的网段了（当然也可以改虚拟机的），换句话说就是大家重装一下 o (一︿一 +) o

**按照以下标准步骤重装即可**

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/解决-Node-无法加入的问题.html#重置-kubernetes)重置 Kubernetes

```bash
kubeadm reset

# 输出如下
[reset] WARNING: Changes made to this host by 'kubeadm init' or 'kubeadm join' will be reverted.
[reset] Are you sure you want to proceed? [y/N]: y
[preflight] Running pre-flight checks
W0604 01:55:28.517280   22688 reset.go:234] [reset] No kubeadm config, using etcd pod spec to get data directory
[reset] No etcd config found. Assuming external etcd
[reset] Please manually reset etcd to prevent further issues
[reset] Stopping the kubelet service
[reset] unmounting mounted directories in "/var/lib/kubelet"
[reset] Deleting contents of stateful directories: [/var/lib/kubelet /etc/cni/net.d /var/lib/dockershim /var/run/kubernetes]
[reset] Deleting contents of config directories: [/etc/kubernetes/manifests /etc/kubernetes/pki]
[reset] Deleting files: [/etc/kubernetes/admin.conf /etc/kubernetes/kubelet.conf /etc/kubernetes/bootstrap-kubelet.conf /etc/kubernetes/controller-manager.conf /etc/kubernetes/scheduler.conf]

The reset process does not reset or clean up iptables rules or IPVS tables.
If you wish to reset iptables, you must do so manually.
For example:
iptables -F && iptables -t nat -F && iptables -t mangle -F && iptables -X

If your cluster was setup to utilize IPVS, run ipvsadm --clear (or similar)
to reset your system's IPVS tables.
```

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/解决-Node-无法加入的问题.html#删除-kubectl-配置)删除 kubectl 配置

```bash
rm -fr ~/.kube/
```

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/解决-Node-无法加入的问题.html#启用-ipvs)启用 IPVS

```bash
modprobe -- ip_vs
modprobe -- ip_vs_rr
modprobe -- ip_vs_wrr
modprobe -- ip_vs_sh
modprobe -- nf_conntrack_ipv4
```



## [#](https://www.funtl.com/zh/service-mesh-kubernetes/解决-Node-无法加入的问题.html#导出并修改配置文件)导出并修改配置文件

```bash
kubeadm config print init-defaults --kubeconfig ClusterConfiguration > kubeadm.yml
```

配置文件修改如下

```yaml
apiVersion: kubeadm.k8s.io/v1beta1
bootstrapTokens:
- groups:
  - system:bootstrappers:kubeadm:default-node-token
  token: abcdef.0123456789abcdef
  ttl: 24h0m0s
  usages:
  - signing
  - authentication
kind: InitConfiguration
localAPIEndpoint:
  advertiseAddress: 192.168.141.150
  bindPort: 6443
nodeRegistration:
  criSocket: /var/run/dockershim.sock
  name: kubernetes-master-01
  taints:
  - effect: NoSchedule
    key: node-role.kubernetes.io/master
---
apiServer:
  timeoutForControlPlane: 4m0s
apiVersion: kubeadm.k8s.io/v1beta1
certificatesDir: /etc/kubernetes/pki
clusterName: kubernetes
controlPlaneEndpoint: "192.168.141.200:6444"
controllerManager: {}
dns:
  type: CoreDNS
etcd:
  local:
    dataDir: /var/lib/etcd
imageRepository: registry.aliyuncs.com/google_containers
kind: ClusterConfiguration
kubernetesVersion: v1.14.2
networking:
  dnsDomain: cluster.local
  # 主要修改在这里，替换 Calico 网段为我们虚拟机不重叠的网段（这里用的是 Flannel 默认网段）
  podSubnet: "10.244.0.0/16"
  serviceSubnet: 10.96.0.0/12
scheduler: {}
---
apiVersion: kubeproxy.config.k8s.io/v1alpha1
kind: KubeProxyConfiguration
featureGates:
  SupportIPVSProxyMode: true
mode: ipvs
```



## [#](https://www.funtl.com/zh/service-mesh-kubernetes/解决-Node-无法加入的问题.html#kubeadm-初始化)kubeadm 初始化

```bash
kubeadm init --config=kubeadm.yml --experimental-upload-certs | tee kubeadm-init.log

# 输出如下
[init] Using Kubernetes version: v1.14.2
[preflight] Running pre-flight checks
        [WARNING IsDockerSystemdCheck]: detected "cgroupfs" as the Docker cgroup driver. The recommended driver is "systemd". Please follow the guide at https://kubernetes.io/docs/setup/cri/
[preflight] Pulling images required for setting up a Kubernetes cluster
[preflight] This might take a minute or two, depending on the speed of your internet connection
[preflight] You can also perform this action in beforehand using 'kubeadm config images pull'
[kubelet-start] Writing kubelet environment file with flags to file "/var/lib/kubelet/kubeadm-flags.env"
[kubelet-start] Writing kubelet configuration to file "/var/lib/kubelet/config.yaml"
[kubelet-start] Activating the kubelet service
[certs] Using certificateDir folder "/etc/kubernetes/pki"
[certs] Generating "front-proxy-ca" certificate and key
[certs] Generating "front-proxy-client" certificate and key
[certs] Generating "etcd/ca" certificate and key
[certs] Generating "etcd/peer" certificate and key
[certs] etcd/peer serving cert is signed for DNS names [kubernetes-master-01 localhost] and IPs [192.168.141.150 127.0.0.1 ::1]
[certs] Generating "etcd/healthcheck-client" certificate and key
[certs] Generating "apiserver-etcd-client" certificate and key
[certs] Generating "etcd/server" certificate and key
[certs] etcd/server serving cert is signed for DNS names [kubernetes-master-01 localhost] and IPs [192.168.141.150 127.0.0.1 ::1]
[certs] Generating "ca" certificate and key
[certs] Generating "apiserver" certificate and key
[certs] apiserver serving cert is signed for DNS names [kubernetes-master-01 kubernetes kubernetes.default kubernetes.default.svc kubernetes.default.svc.cluster.local] and IPs [10.96.0.1 192.168.141.150 192.168.141.200]
[certs] Generating "apiserver-kubelet-client" certificate and key
[certs] Generating "sa" key and public key
[kubeconfig] Using kubeconfig folder "/etc/kubernetes"
[endpoint] WARNING: port specified in controlPlaneEndpoint overrides bindPort in the controlplane address
[kubeconfig] Writing "admin.conf" kubeconfig file
[endpoint] WARNING: port specified in controlPlaneEndpoint overrides bindPort in the controlplane address
[kubeconfig] Writing "kubelet.conf" kubeconfig file
[endpoint] WARNING: port specified in controlPlaneEndpoint overrides bindPort in the controlplane address
[kubeconfig] Writing "controller-manager.conf" kubeconfig file
[endpoint] WARNING: port specified in controlPlaneEndpoint overrides bindPort in the controlplane address
[kubeconfig] Writing "scheduler.conf" kubeconfig file
[control-plane] Using manifest folder "/etc/kubernetes/manifests"
[control-plane] Creating static Pod manifest for "kube-apiserver"
[control-plane] Creating static Pod manifest for "kube-controller-manager"
[control-plane] Creating static Pod manifest for "kube-scheduler"
[etcd] Creating static Pod manifest for local etcd in "/etc/kubernetes/manifests"
[wait-control-plane] Waiting for the kubelet to boot up the control plane as static Pods from directory "/etc/kubernetes/manifests". This can take up to 4m0s
[apiclient] All control plane components are healthy after 24.507568 seconds
[upload-config] storing the configuration used in ConfigMap "kubeadm-config" in the "kube-system" Namespace
[kubelet] Creating a ConfigMap "kubelet-config-1.14" in namespace kube-system with the configuration for the kubelets in the cluster
[upload-certs] Storing the certificates in ConfigMap "kubeadm-certs" in the "kube-system" Namespace
[upload-certs] Using certificate key:
a662b8364666f82c93cc5cd4fb4fabb623bbe9afdb182da353ac40f1752dfa4a
[mark-control-plane] Marking the node kubernetes-master-01 as control-plane by adding the label "node-role.kubernetes.io/master=''"
[mark-control-plane] Marking the node kubernetes-master-01 as control-plane by adding the taints [node-role.kubernetes.io/master:NoSchedule]
[bootstrap-token] Using token: abcdef.0123456789abcdef
[bootstrap-token] Configuring bootstrap tokens, cluster-info ConfigMap, RBAC Roles
[bootstrap-token] configured RBAC rules to allow Node Bootstrap tokens to post CSRs in order for nodes to get long term certificate credentials
[bootstrap-token] configured RBAC rules to allow the csrapprover controller automatically approve CSRs from a Node Bootstrap Token
[bootstrap-token] configured RBAC rules to allow certificate rotation for all node client certificates in the cluster
[bootstrap-token] creating the "cluster-info" ConfigMap in the "kube-public" namespace
[addons] Applied essential addon: CoreDNS
[endpoint] WARNING: port specified in controlPlaneEndpoint overrides bindPort in the controlplane address
[addons] Applied essential addon: kube-proxy

Your Kubernetes control-plane has initialized successfully!

To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

You can now join any number of the control-plane node running the following command on each as root:

  kubeadm join 192.168.141.200:6444 --token abcdef.0123456789abcdef \
    --discovery-token-ca-cert-hash sha256:2ea8c138021fb1e184a24ed2a81c16c92f9f25c635c73918b1402df98f9c8aad \
    --experimental-control-plane --certificate-key a662b8364666f82c93cc5cd4fb4fabb623bbe9afdb182da353ac40f1752dfa4a

Please note that the certificate-key gives access to cluster sensitive data, keep it secret!
As a safeguard, uploaded-certs will be deleted in two hours; If necessary, you can use 
"kubeadm init phase upload-certs --experimental-upload-certs" to reload certs afterward.

Then you can join any number of worker nodes by running the following on each as root:

kubeadm join 192.168.141.200:6444 --token abcdef.0123456789abcdef \
    --discovery-token-ca-cert-hash sha256:2ea8c138021fb1e184a24ed2a81c16c92f9f25c635c73918b1402df98f9c8aad 
```



## [#](https://www.funtl.com/zh/service-mesh-kubernetes/解决-Node-无法加入的问题.html#配置-kubectl)配置 kubectl

```bash
# 配置 kubectl
mkdir -p $HOME/.kube
cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
chown $(id -u):$(id -g) $HOME/.kube/config

# 验证是否成功
kubectl get node
```

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/解决-Node-无法加入的问题.html#下载-calico-配置文件并修改)下载 Calico 配置文件并修改

```bash
wget https://docs.projectcalico.org/v3.7/manifests/calico.yaml
```

```bash
vi calico.yaml
```

修改第 611 行，将 `192.168.0.0/16` 修改为 `10.244.0.0/16`，可以通过如下命令快速查找

- 显示行号：`:set number`
- 查找字符：`/要查找的字符`，输入小写 `n` 下一个匹配项，输入大写 `N` 上一个匹配项

![img](https://www.funtl.com/assets1/Lusifer_20190604022029.png)

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/解决-Node-无法加入的问题.html#安装-calico)安装 Calico

```bash
kubectl apply -f calico.yaml

# 输出如下
configmap/calico-config created
customresourcedefinition.apiextensions.k8s.io/felixconfigurations.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/ipamblocks.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/blockaffinities.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/ipamhandles.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/ipamconfigs.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/bgppeers.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/bgpconfigurations.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/ippools.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/hostendpoints.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/clusterinformations.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/globalnetworkpolicies.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/globalnetworksets.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/networkpolicies.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/networksets.crd.projectcalico.org created
clusterrole.rbac.authorization.k8s.io/calico-kube-controllers created
clusterrolebinding.rbac.authorization.k8s.io/calico-kube-controllers created
clusterrole.rbac.authorization.k8s.io/calico-node created
clusterrolebinding.rbac.authorization.k8s.io/calico-node created
daemonset.extensions/calico-node created
serviceaccount/calico-node created
deployment.extensions/calico-kube-controllers created
serviceaccount/calico-kube-controllers created
```

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/解决-Node-无法加入的问题.html#加入-master-节点)加入 Master 节点

```bash
# 示例如下，别忘记两个备用节点都要加入哦
kubeadm join 192.168.141.200:6444 --token abcdef.0123456789abcdef \
    --discovery-token-ca-cert-hash sha256:2ea8c138021fb1e184a24ed2a81c16c92f9f25c635c73918b1402df98f9c8aad \
    --experimental-control-plane --certificate-key a662b8364666f82c93cc5cd4fb4fabb623bbe9afdb182da353ac40f1752dfa4a

# 输出如下
[preflight] Running pre-flight checks
        [WARNING IsDockerSystemdCheck]: detected "cgroupfs" as the Docker cgroup driver. The recommended driver is "systemd". Please follow the guide at https://kubernetes.io/docs/setup/cri/
[preflight] Reading configuration from the cluster...
[preflight] FYI: You can look at this config file with 'kubectl -n kube-system get cm kubeadm-config -oyaml'
[preflight] Running pre-flight checks before initializing the new control plane instance
[preflight] Pulling images required for setting up a Kubernetes cluster
[preflight] This might take a minute or two, depending on the speed of your internet connection
[preflight] You can also perform this action in beforehand using 'kubeadm config images pull'
[download-certs] Downloading the certificates in Secret "kubeadm-certs" in the "kube-system" Namespace
[certs] Using certificateDir folder "/etc/kubernetes/pki"
[certs] Generating "apiserver" certificate and key
[certs] apiserver serving cert is signed for DNS names [kubernetes-master-02 kubernetes kubernetes.default kubernetes.default.svc kubernetes.default.svc.cluster.local] and IPs [10.96.0.1 192.168.141.151 192.168.141.200]
[certs] Generating "apiserver-kubelet-client" certificate and key
[certs] Generating "front-proxy-client" certificate and key
[certs] Generating "etcd/server" certificate and key
[certs] etcd/server serving cert is signed for DNS names [kubernetes-master-02 localhost] and IPs [192.168.141.151 127.0.0.1 ::1]
[certs] Generating "etcd/peer" certificate and key
[certs] etcd/peer serving cert is signed for DNS names [kubernetes-master-02 localhost] and IPs [192.168.141.151 127.0.0.1 ::1]
[certs] Generating "etcd/healthcheck-client" certificate and key
[certs] Generating "apiserver-etcd-client" certificate and key
[certs] Valid certificates and keys now exist in "/etc/kubernetes/pki"
[certs] Using the existing "sa" key
[kubeconfig] Generating kubeconfig files
[kubeconfig] Using kubeconfig folder "/etc/kubernetes"
[endpoint] WARNING: port specified in controlPlaneEndpoint overrides bindPort in the controlplane address
[kubeconfig] Writing "admin.conf" kubeconfig file
[kubeconfig] Writing "controller-manager.conf" kubeconfig file
[kubeconfig] Writing "scheduler.conf" kubeconfig file
[control-plane] Using manifest folder "/etc/kubernetes/manifests"
[control-plane] Creating static Pod manifest for "kube-apiserver"
[control-plane] Creating static Pod manifest for "kube-controller-manager"
[control-plane] Creating static Pod manifest for "kube-scheduler"
[check-etcd] Checking that the etcd cluster is healthy
[kubelet-start] Downloading configuration for the kubelet from the "kubelet-config-1.14" ConfigMap in the kube-system namespace
[kubelet-start] Writing kubelet configuration to file "/var/lib/kubelet/config.yaml"
[kubelet-start] Writing kubelet environment file with flags to file "/var/lib/kubelet/kubeadm-flags.env"
[kubelet-start] Activating the kubelet service
[kubelet-start] Waiting for the kubelet to perform the TLS Bootstrap...
[etcd] Announced new etcd member joining to the existing etcd cluster
[etcd] Wrote Static Pod manifest for a local etcd member to "/etc/kubernetes/manifests/etcd.yaml"
[etcd] Waiting for the new etcd member to join the cluster. This can take up to 40s
[upload-config] storing the configuration used in ConfigMap "kubeadm-config" in the "kube-system" Namespace
[mark-control-plane] Marking the node kubernetes-master-02 as control-plane by adding the label "node-role.kubernetes.io/master=''"
[mark-control-plane] Marking the node kubernetes-master-02 as control-plane by adding the taints [node-role.kubernetes.io/master:NoSchedule]

This node has joined the cluster and a new control plane instance was created:

* Certificate signing request was sent to apiserver and approval was received.
* The Kubelet was informed of the new secure connection details.
* Control plane (master) label and taint were applied to the new node.
* The Kubernetes control plane instances scaled up.
* A new etcd member was added to the local/stacked etcd cluster.

To start administering your cluster from this node, you need to run the following as a regular user:

        mkdir -p $HOME/.kube
        sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
        sudo chown $(id -u):$(id -g) $HOME/.kube/config

Run 'kubectl get nodes' to see this node join the cluster.
```



## [#](https://www.funtl.com/zh/service-mesh-kubernetes/解决-Node-无法加入的问题.html#加入-node-节点)加入 Node 节点

```bash
# 示例如下
kubeadm join 192.168.141.200:6444 --token abcdef.0123456789abcdef \
    --discovery-token-ca-cert-hash sha256:2ea8c138021fb1e184a24ed2a81c16c92f9f25c635c73918b1402df98f9c8aad

# 输出如下
>     --discovery-token-ca-cert-hash sha256:2ea8c138021fb1e184a24ed2a81c16c92f9f25c635c73918b1402df98f9c8aad 
[preflight] Running pre-flight checks
        [WARNING IsDockerSystemdCheck]: detected "cgroupfs" as the Docker cgroup driver. The recommended driver is "systemd". Please follow the guide at https://kubernetes.io/docs/setup/cri/
[preflight] Reading configuration from the cluster...
[preflight] FYI: You can look at this config file with 'kubectl -n kube-system get cm kubeadm-config -oyaml'
[kubelet-start] Downloading configuration for the kubelet from the "kubelet-config-1.14" ConfigMap in the kube-system namespace
[kubelet-start] Writing kubelet configuration to file "/var/lib/kubelet/config.yaml"
[kubelet-start] Writing kubelet environment file with flags to file "/var/lib/kubelet/kubeadm-flags.env"
[kubelet-start] Activating the kubelet service
[kubelet-start] Waiting for the kubelet to perform the TLS Bootstrap...

This node has joined the cluster:
* Certificate signing request was sent to apiserver and a response was received.
* The Kubelet was informed of the new secure connection details.

Run 'kubectl get nodes' on the control-plane to see this node join the cluster.
```

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/解决-Node-无法加入的问题.html#验证是否可用)验证是否可用

```bash
kubectl get node

# 输出如下，我们可以看到 Node 节点已经成功上线 ━━(￣ー￣*|||━━
NAME                   STATUS   ROLES    AGE     VERSION
kubernetes-master-01   Ready    master   19m     v1.14.2
kubernetes-master-02   Ready    master   4m46s   v1.14.2
kubernetes-master-03   Ready    master   3m23s   v1.14.2
kubernetes-node-01     Ready    <none>   74s     v1.14.2
```

```bash
watch kubectl get pods --all-namespaces

# 输出如下，coredns 也正常运行了
Every 2.0s: kubectl get pods --all-namespaces                                                                                                 kubernetes-master-01: Tue Jun  4 02:31:43 2019

NAMESPACE     NAME                                           READY   STATUS    RESTARTS   AGE
kube-system   calico-kube-controllers-8646dd497f-hz5xp       1/1     Running   0          9m9s
kube-system   calico-node-2z892                              1/1     Running   0          9m9s
kube-system   calico-node-fljxv                              1/1     Running   0          6m39s
kube-system   calico-node-vprlw                              1/1     Running   0          5m16s
kube-system   calico-node-xvqcx                              1/1     Running   0          3m7s
kube-system   coredns-8686dcc4fd-5ndjm                       1/1     Running   0          21m
kube-system   coredns-8686dcc4fd-zxtql                       1/1     Running   0          21m
kube-system   etcd-kubernetes-master-01                      1/1     Running   0          20m
kube-system   etcd-kubernetes-master-02                      1/1     Running   0          6m37s
kube-system   etcd-kubernetes-master-03                      1/1     Running   0          5m14s
kube-system   kube-apiserver-kubernetes-master-01            1/1     Running   0          20m
kube-system   kube-apiserver-kubernetes-master-02            1/1     Running   0          6m37s
kube-system   kube-apiserver-kubernetes-master-03            1/1     Running   0          5m14s
kube-system   kube-controller-manager-kubernetes-master-01   1/1     Running   1          20m
kube-system   kube-controller-manager-kubernetes-master-02   1/1     Running   0          6m37s
kube-system   kube-controller-manager-kubernetes-master-03   1/1     Running   0          5m14s
kube-system   kube-proxy-68jqr                               1/1     Running   0          3m7s
kube-system   kube-proxy-69bnn                               1/1     Running   0          6m39s
kube-system   kube-proxy-vvhp5                               1/1     Running   0          5m16s
kube-system   kube-proxy-ws6wx                               1/1     Running   0          21m
kube-system   kube-scheduler-kubernetes-master-01            1/1     Running   1          20m
kube-system   kube-scheduler-kubernetes-master-02            1/1     Running   0          6m37s
kube-system   kube-scheduler-kubernetes-master-03            1/1     Running   0          5m14s
```



**至此，Kubernetes 高可用集群算是彻底部署成功，撒花撒花 (゜-゜)つロ 干杯**

# 通过资源配置运行容器

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/通过资源配置运行容器.html#本节视频)本节视频

[【（千锋教育）服务网格化 Service Mesh】Kubernetes-通过资源配置运行容器](https://www.bilibili.com/video/av52359802/?p=15)

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/通过资源配置运行容器.html#概述)概述

我们知道通过 `run` 命令启动容器非常麻烦，Docker 提供了 Compose 为我们解决了这个问题。那 Kubernetes 是如何解决这个问题的呢？其实很简单，使用 `kubectl create` 命令就可以做到和 Compose 一样的效果了，该命令可以通过配置文件快速创建一个集群资源对象。

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/通过资源配置运行容器.html#创建-yaml-配置文件)创建 YAML 配置文件

以部署 Nginx 为例

### [#](https://www.funtl.com/zh/service-mesh-kubernetes/通过资源配置运行容器.html#部署-deployment)部署 Deployment

创建一个名为 `nginx-deployment.yml` 的配置文件

#### [#](https://www.funtl.com/zh/service-mesh-kubernetes/通过资源配置运行容器.html#v1-16-0-之前)v1.16.0 之前

> **注意：** `extensions/v1beta1` 不再支持部署 `Deployment`，并且修改了少量命令

```yaml
# API 版本号
apiVersion: extensions/v1beta1
# 类型，如：Pod/ReplicationController/Deployment/Service/Ingress
kind: Deployment
# 元数据
metadata:
  # Kind 的名称
  name: nginx-app
spec:
  # 部署的实例数量
  replicas: 2
  template:
    metadata:
      labels:
        # 容器标签的名字，发布 Service 时，selector 需要和这里对应
        name: nginx
    spec:
      # 配置容器，数组类型，说明可以配置多个容器
      containers:
      # 容器名称
      - name: nginx
        # 容器镜像
        image: nginx
        # 暴露端口
        ports:
        # Pod 端口
        - containerPort: 80
```



#### [#](https://www.funtl.com/zh/service-mesh-kubernetes/通过资源配置运行容器.html#v1-16-0-之后)v1.16.0 之后

```yaml
# API 版本号：由 extensions/v1beta1 修改为 apps/v1
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-app
spec:
  # 增加了选择器配置
  selector:
    matchLabels:
      app: nginx
  replicas: 2
  template:
    metadata:
      labels:
        # 设置标签由 name 修改为 app
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx
        ports:
        - containerPort: 80
```

```bash
# 部署
kubectl create -f nginx-deployment.yml

# 删除
kubectl delete -f nginx-deployment.yml
```



### [#](https://www.funtl.com/zh/service-mesh-kubernetes/通过资源配置运行容器.html#发布-service)发布 Service

创建一个名为 `nginx-service.yml` 的配置文件

#### [#](https://www.funtl.com/zh/service-mesh-kubernetes/通过资源配置运行容器.html#v1-16-0-之前-2)v1.16.0 之前

```yaml
# API 版本号
apiVersion: v1
# 类型，如：Pod/ReplicationController/Deployment/Service/Ingress
kind: Service
# 元数据
metadata:
  # Kind 的名称
  name: nginx-http
spec:
  # 暴露端口
  ports:
    ## Service 暴露的端口
    - port: 80
      ## Pod 上的端口，这里是将 Service 暴露的端口转发到 Pod 端口上
      targetPort: 80
  # 类型
  type: LoadBalancer
  # 标签选择器
  selector:
    # 需要和上面部署的 Deployment 标签名对应
    name: nginx
```



#### [#](https://www.funtl.com/zh/service-mesh-kubernetes/通过资源配置运行容器.html#v1-16-0-之后-2)v1.16.0 之后

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-http
spec:
  ports:
    - port: 80
      targetPort: 80
  type: LoadBalancer
  selector:
    # 标签选择器由 name 修改为 app
    app: nginx
```



```bash
# 部署
kubectl create -f nginx-service.yml

# 删除
kubectl delete -f nginx-service.yml
```



## [#](https://www.funtl.com/zh/service-mesh-kubernetes/通过资源配置运行容器.html#验证是否生效)验证是否生效

### [#](https://www.funtl.com/zh/service-mesh-kubernetes/通过资源配置运行容器.html#查看-pod-列表)查看 Pod 列表

```bash
kubectl get pods

# 输出如下
NAME                         READY   STATUS    RESTARTS   AGE
nginx-app-64bb598779-2pplx   1/1     Running   0          25m
nginx-app-64bb598779-824lc   1/1     Running   0          25m
```



### [#](https://www.funtl.com/zh/service-mesh-kubernetes/通过资源配置运行容器.html#查看-deployment-列表)查看 Deployment 列表

```bash
kubectl get deployment

# 输出如下
NAME        READY   UP-TO-DATE   AVAILABLE   AGE
nginx-app   2/2     2            2           25m
```



### [#](https://www.funtl.com/zh/service-mesh-kubernetes/通过资源配置运行容器.html#查看-service-列表)查看 Service 列表

```bash
kubectl get service

# 输出如下
NAME         TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
kubernetes   ClusterIP      10.96.0.1      <none>        443/TCP        20h
nginx-http    LoadBalancer   10.98.49.142   <pending>     80:31631/TCP   14m
```



### [#](https://www.funtl.com/zh/service-mesh-kubernetes/通过资源配置运行容器.html#查看-service-详情)查看 Service 详情

```bash
kubectl describe service nginx-app

# 输出如下
Name:                     nginx-http
Namespace:                default
Labels:                   <none>
Annotations:              <none>
Selector:                 name=nginx
Type:                     LoadBalancer
IP:                       10.98.49.142
Port:                     <unset>  80/TCP
TargetPort:               80/TCP
NodePort:                 <unset>  31631/TCP
Endpoints:                10.244.141.205:80,10.244.2.4:80
Session Affinity:         None
External Traffic Policy:  Cluster
Events:                   <none>
```

### [#](https://www.funtl.com/zh/service-mesh-kubernetes/通过资源配置运行容器.html#通过浏览器访问)通过浏览器访问

通过浏览器访问 http://192.168.141.150:31631/ ，出现 Nginx 欢迎页即表示成功

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/通过资源配置运行容器.html#集成环境部署)集成环境部署

也可以不区分配置文件，一次性部署 Deployment 和 Service，创建一个名为 `nginx.yml` 的配置文件，配置内容如下：

#### [#](https://www.funtl.com/zh/service-mesh-kubernetes/通过资源配置运行容器.html#v1-16-0-之前-3)v1.16.0 之前

```yaml
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: nginx-app
spec:
  replicas: 2
  template:
    metadata:
      labels:
        name: nginx
    spec:
      containers:
      - name: nginx
        image: nginx
        ports:
        - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: nginx-http
spec:
  ports:
    - port: 80
      targetPort: 80
      # 可以指定 NodePort 端口，默认范围是：30000-32767
      # nodePort: 30080
  type: LoadBalancer
  selector:
    name: nginx
```

#### [#](https://www.funtl.com/zh/service-mesh-kubernetes/通过资源配置运行容器.html#v1-16-0-之后-3)v1.16.0 之后

```yaml
# API 版本号：由 extensions/v1beta1 修改为 apps/v1
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-app
spec:
  # 增加了选择器配置
  selector:
    matchLabels:
      app: nginx
  replicas: 2
  template:
    metadata:
      labels:
        # 设置标签由 name 修改为 app
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx
        ports:
        - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: nginx-http
spec:
  ports:
    - port: 80
      targetPort: 80
  type: LoadBalancer
  selector:
    # 标签选择器由 name 修改为 app
    app: nginx
```

```bash
# 部署
kubectl create -f nginx.yml

# 删除
kubectl delete -f nginx.yml
```

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/通过资源配置运行容器.html#附：修改默认的端口范围)附：修改默认的端口范围

Kubernetes 服务的 NodePort 默认端口范围是 30000-32767，在某些场合下，这个限制不太适用，我们可以自定义它的端口范围，操作步骤如下：

编辑 `vi /etc/kubernetes/manifests/kube-apiserver.yaml` 配置文件，增加配置 `--service-node-port-range=2-65535`

```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    component: kube-apiserver
    tier: control-plane
  name: kube-apiserver
  namespace: kube-system
spec:
  containers:
  - command:
    - kube-apiserver
    # 在这里增加配置即可
    - --service-node-port-range=2-65535
    - --advertise-address=192.168.141.150
    - --allow-privileged=true
    - --authorization-mode=Node,RBAC
    - --client-ca-file=/etc/kubernetes/pki/ca.crt
    - --enable-admission-plugins=NodeRestriction
    - --enable-bootstrap-token-auth=true
    - --etcd-cafile=/etc/kubernetes/pki/etcd/ca.crt
    - --etcd-certfile=/etc/kubernetes/pki/apiserver-etcd-client.crt
// 以下配置省略...
```

使用 `docker ps` 命令找到 `kube-apiserver` 容器，再使用 `docker restart ` 即可生效。



#  Ingress 统一访问入口

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/Ingress-统一访问入口.html#本节视频)本节视频

[【（千锋教育）服务网格化 Service Mesh】Kubernetes-Ingress 统一访问入口](https://www.bilibili.com/video/av52359802/?p=16)

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/Ingress-统一访问入口.html#术语)术语

- **节点：** Kubernetes 集群中的服务器
- **集群：** Kubernetes 管理的一组服务器集合
- **边界路由器：** 为局域网和 Internet 路由数据包的路由器，执行防火墙保护局域网络
- **集群网络：** 遵循 Kubernetes 网络模型实现集群内的通信的具体实现，比如 Flannel 和 Calico
- **服务：** Kubernetes 的服务 (Service) 是使用标签选择器标识的一组 Pod Service (Deployment)。 **除非另有说明，否则服务的虚拟 IP 仅可在集群内部访问**

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/Ingress-统一访问入口.html#内部访问方式-clusterip)内部访问方式 ClusterIP

ClusterIP 服务是 Kubernetes 的默认服务。它给你一个集群内的服务，集群内的其它应用都可以访问该服务。集群外部无法访问它。在某些场景下我们可以使用 Kubernetes 的 Proxy 模式来访问服务，比如调试服务时。

![img](https://www.funtl.com/assets1/Lusifer_2019060601200001.png)

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/Ingress-统一访问入口.html#三种外部访问方式)三种外部访问方式

### [#](https://www.funtl.com/zh/service-mesh-kubernetes/Ingress-统一访问入口.html#nodeport)NodePort

NodePort 服务是引导外部流量到你的服务的最原始方式。NodePort，正如这个名字所示，**在所有节点（虚拟机）上开放一个特定端口**，任何发送到该端口的流量都被转发到对应服务。

NodePort 服务特征如下：

- 每个端口只能是一种服务
- 端口范围只能是 30000-32767（可调）
- 不在 YAML 配置文件中指定则会分配一个默认端口

> **建议：** 不要在生产环境中使用这种方式暴露服务，大多数时候我们应该让 Kubernetes 来选择端口

![img](https://www.funtl.com/assets1/Lusifer_2019060601200002.png)

### [#](https://www.funtl.com/zh/service-mesh-kubernetes/Ingress-统一访问入口.html#loadbalancer)LoadBalancer

LoadBalancer 服务是暴露服务到 Internet 的标准方式。所有通往你指定的端口的流量都会被转发到对应的服务。它没有过滤条件，没有路由等。这意味着你几乎可以发送任何种类的流量到该服务，像 HTTP，TCP，UDP，WebSocket，gRPC 或其它任意种类。

![img](https://www.funtl.com/assets1/Lusifer_2019060601200003.png)

### [#](https://www.funtl.com/zh/service-mesh-kubernetes/Ingress-统一访问入口.html#ingress)Ingress

Ingress 事实上不是一种服务类型。相反，它处于多个服务的前端，扮演着 “智能路由” 或者集群入口的角色。你可以用 Ingress 来做许多不同的事情，各种不同类型的 Ingress 控制器也有不同的能力。它允许你基于路径或者子域名来路由流量到后端服务。

Ingress 可能是暴露服务的最强大方式，但同时也是最复杂的。Ingress 控制器有各种类型，包括 Google Cloud Load Balancer， Nginx，Contour，Istio，等等。它还有各种插件，比如 cert-manager (它可以为你的服务自动提供 SSL 证书)/

如果你想要使用同一个 IP 暴露多个服务，这些服务都是使用相同的七层协议（典型如 HTTP），你还可以获取各种开箱即用的特性（比如 SSL、认证、路由等等）

![img](https://www.funtl.com/assets1/Lusifer_2019060601200004.png)



## [#](https://www.funtl.com/zh/service-mesh-kubernetes/Ingress-统一访问入口.html#什么是-ingress)什么是 Ingress

通常情况下，Service 和 Pod 的 IP 仅可在集群内部访问。集群外部的请求需要通过负载均衡转发到 Service 在 Node 上暴露的 NodePort 上，然后再由 kube-proxy 通过边缘路由器 (edge router) 将其转发给相关的 Pod 或者丢弃。而 Ingress 就是为进入集群的请求提供路由规则的集合

Ingress 可以给 Service 提供集群外部访问的 URL、负载均衡、SSL 终止、HTTP 路由等。为了配置这些 Ingress 规则，集群管理员需要部署一个 Ingress Controller，它监听 Ingress 和 Service 的变化，并根据规则配置负载均衡并提供访问入口。

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/Ingress-统一访问入口.html#使用-nginx-ingress-controller)使用 Nginx Ingress Controller

本次实践的主要目的就是将入口统一，不再通过 LoadBalancer 等方式将端口暴露出来，而是使用 Ingress 提供的反向代理负载均衡功能作为我们的唯一入口。通过以下步骤操作仔细体会。

> **注意：** 下面包含资源配置的步骤都是自行创建 YAML 配置文件通过 `kubectl create -f ` 和 `kubectl delete -f ` 部署和删除

### [#](https://www.funtl.com/zh/service-mesh-kubernetes/Ingress-统一访问入口.html#部署-tomcat)部署 Tomcat

部署 Tomcat 但仅允许在内网访问，我们要通过 Ingress 提供的反向代理功能路由到 Tomcat 之上

```yaml
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: tomcat-app
spec:
  replicas: 2
  template:
    metadata:
      labels:
        name: tomcat
    spec:
      containers:
      - name: tomcat
        image: tomcat
        ports:
        - containerPort: 8080
---
apiVersion: v1
kind: Service
metadata:
  name: tomcat-http
spec:
  ports:
    - port: 8080
      targetPort: 8080
  # ClusterIP, NodePort, LoadBalancer
  type: LoadBalancer
  selector:
    name: tomcat
```



### [#](https://www.funtl.com/zh/service-mesh-kubernetes/Ingress-统一访问入口.html#安装-nginx-ingress-controller)安装 Nginx Ingress Controller

Ingress Controller 有许多种，我们选择最熟悉的 Nginx 来处理请求，其它可以参考 [官方文档](https://kubernetes.io/docs/concepts/services-networking/ingress-controllers/)

- 下载 Nginx Ingress Controller 配置文件

```bash
wget https://raw.githubusercontent.com/kubernetes/ingress-nginx/master/deploy/static/mandatory.yaml
```

- 修改配置文件，找到配置如下位置 (搜索 `serviceAccountName`) 在下面增加一句 `hostNetwork: true`

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-ingress-controller
  namespace: ingress-nginx
  labels:
    app.kubernetes.io/name: ingress-nginx
    app.kubernetes.io/part-of: ingress-nginx
spec:
  # 可以部署多个实例
  replicas: 1
  selector:
    matchLabels:
      app.kubernetes.io/name: ingress-nginx
      app.kubernetes.io/part-of: ingress-nginx
  template:
    metadata:
      labels:
        app.kubernetes.io/name: ingress-nginx
        app.kubernetes.io/part-of: ingress-nginx
      annotations:
        prometheus.io/port: "10254"
        prometheus.io/scrape: "true"
    spec:
      serviceAccountName: nginx-ingress-serviceaccount
      # 增加 hostNetwork: true，意思是开启主机网络模式，暴露 Nginx 服务端口 80
      hostNetwork: true
      containers:
        - name: nginx-ingress-controller
          image: quay.io/kubernetes-ingress-controller/nginx-ingress-controller:0.24.1
          args:
            - /nginx-ingress-controller
            - --configmap=$(POD_NAMESPACE)/nginx-configuration
            - --tcp-services-configmap=$(POD_NAMESPACE)/tcp-services
            - --udp-services-configmap=$(POD_NAMESPACE)/udp-services
            - --publish-service=$(POD_NAMESPACE)/ingress-nginx
// 以下代码省略...
```

### [#](https://www.funtl.com/zh/service-mesh-kubernetes/Ingress-统一访问入口.html#部署-ingress)部署 Ingress

Ingress 翻译过来是入口的意思，说白了就是个 API 网关（想想之前学的 Zuul 和 Spring Cloud Gateway）

```yaml
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: nginx-web
  annotations:
    # 指定 Ingress Controller 的类型
    kubernetes.io/ingress.class: "nginx"
    # 指定我们的 rules 的 path 可以使用正则表达式
    nginx.ingress.kubernetes.io/use-regex: "true"
    # 连接超时时间，默认为 5s
    nginx.ingress.kubernetes.io/proxy-connect-timeout: "600"
    # 后端服务器回转数据超时时间，默认为 60s
    nginx.ingress.kubernetes.io/proxy-send-timeout: "600"
    # 后端服务器响应超时时间，默认为 60s
    nginx.ingress.kubernetes.io/proxy-read-timeout: "600"
    # 客户端上传文件，最大大小，默认为 20m
    nginx.ingress.kubernetes.io/proxy-body-size: "10m"
    # URL 重写
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  # 路由规则
  rules:
  # 主机名，只能是域名，修改为你自己的
  - host: k8s.test.com
    http:
      paths:
      - path:
        backend:
          # 后台部署的 Service Name，与上面部署的 Tomcat 对应
          serviceName: tomcat-http
          # 后台部署的 Service Port，与上面部署的 Tomcat 对应
          servicePort: 8080
```

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/Ingress-统一访问入口.html#验证是否成功)验证是否成功

### [#](https://www.funtl.com/zh/service-mesh-kubernetes/Ingress-统一访问入口.html#查看-tomcat)查看 Tomcat

```bash
kubectl get deployment

# 输出如下
NAME         READY   UP-TO-DATE   AVAILABLE   AGE
tomcat-app   2/2     2            2           88m
```

```bash
kubectl get service

# 输出如下
NAME          TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
kubernetes    ClusterIP   10.96.0.1       <none>        443/TCP    2d5h
tomcat-http   ClusterIP   10.97.222.179   <none>        8080/TCP   89m
```



### [#](https://www.funtl.com/zh/service-mesh-kubernetes/Ingress-统一访问入口.html#查看-nginx-ingress-controller)查看 Nginx Ingress Controller

```bash
kubectl get pods -n ingress-nginx -o wide

# 输出如下，注意下面的 IP 地址，就是我们实际访问地址
NAME                                        READY   STATUS    RESTARTS   AGE   IP                NODE                 NOMINATED NODE   READINESS GATES
nginx-ingress-controller-76f9fddcf8-vzkm5   1/1     Running   0          61m   192.168.141.160   kubernetes-node-01   <none>           <none>
```

### [#](https://www.funtl.com/zh/service-mesh-kubernetes/Ingress-统一访问入口.html#查看-ingress)查看 Ingress

```bash
kubectl get ingress

# 输出如下
NAME        HOSTS          ADDRESS   PORTS   AGE
nginx-web   k8s.test.com             80      61m
```



### [#](https://www.funtl.com/zh/service-mesh-kubernetes/Ingress-统一访问入口.html#测试访问)测试访问

成功代理到 Tomcat 即表示成功

```bash
# 不设置 Hosts 的方式请求地址，下面的 IP 和 Host 均在上面有配置
curl -v http://192.168.141.160 -H 'host: k8s.test.com'
```



### [#](https://www.funtl.com/zh/service-mesh-kubernetes/Ingress-统一访问入口.html#扩展阅读)扩展阅读

#### [#](https://www.funtl.com/zh/service-mesh-kubernetes/Ingress-统一访问入口.html#解决无法下载-quay-io-地址镜像的问题)解决无法下载 quay.io 地址镜像的问题

在实际操作中我们需要拉取 Nginx Ingress 镜像，默认地址为：`quay.io/kubernetes-ingress-controller/nginx-ingress-controller:0.24.1`，由于国内网络等原因，可能无法正常拉取该镜像，此时我们可以使用 Azure 提供的镜像加速器，将 `quay.io` 替换为 `quay.azk8s.cn`，其它类似地址的镜像无法下载均可尝试使用该方式解决，替换后的地址如：`quay.azk8s.cn/kubernetes-ingress-controller/nginx-ingress-controller:0.24.1`



# 准备数据持久化

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/准备数据持久化.html#本节视频)本节视频

[【（千锋教育）服务网格化 Service Mesh】Kubernetes-准备数据持久化](https://www.bilibili.com/video/av52359802/?p=17)

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/准备数据持久化.html#概述)概述

在 Docker 中就有数据卷的概念，当容器删除时，数据也一起会被删除，想要持久化使用数据，需要把主机上的目录挂载到 Docker 中去，在 K8S 中，数据卷是通过 Pod 实现持久化的，如果 Pod 删除，数据卷也会一起删除，k8s 的数据卷是 docker 数据卷的扩展，K8S 适配各种存储系统，包括本地存储 EmptyDir，HostPath， 网络存储（NFS，GlusterFS，PV/PVC）等。

我们以部署 MySQL8 为例，采用 **NFS + PV/PVC** 网络存储方案实现我们的 Kubernetes 数据持久化。

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/准备数据持久化.html#什么是-nfs)什么是 NFS

NFS 是 Network File System 的简写，即网络文件系统，NFS 是 FreeBSD 支持的文件系统中的一种。NFS 基于 RPC (Remote Procedure Call) 远程过程调用实现，其允许一个系统在网络上与它人共享目录和文件。通过使用 NFS，用户和程序就可以像访问本地文件一样访问远端系统上的文件。NFS 是一个非常稳定的，可移植的网络文件系统。具备可扩展和高性能等特性，达到了企业级应用质量标准。由于网络速度的增加和延迟的降低，NFS 系统一直是通过网络提供文件系统服务的有竞争力的选择 。

### [#](https://www.funtl.com/zh/service-mesh-kubernetes/准备数据持久化.html#nfs-原理)NFS 原理

NFS 使用 RPC (Remote Procedure Call) 的机制进行实现，RPC 使得客户端可以调用服务端的函数。同时，由于有 VFS 的存在，客户端可以像使用其它普通文件系统一样使用 NFS 文件系统。经由操作系统的内核，将 NFS 文件系统的调用请求通过 TCP/IP 发送至服务端的 NFS 服务。NFS 服务器执行相关的操作，并将操作结果返回给客户端。

![img](https://www.funtl.com/assets1/1335928-20180604090750551-519778068.jpg)

### [#](https://www.funtl.com/zh/service-mesh-kubernetes/准备数据持久化.html#nfs-服务主要进程)NFS 服务主要进程

- rpc.nfsd：最主要的 NFS 进程，管理客户端是否可登录
- rpc.mountd：挂载和卸载 NFS 文件系统，包括权限管理
- rpc.lockd：非必要，管理文件锁，避免同时写出错
- rpc.statd：非必要，检查文件一致性，可修复文件

### [#](https://www.funtl.com/zh/service-mesh-kubernetes/准备数据持久化.html#nfs-的关键工具)NFS 的关键工具

- 主要配置文件：`/etc/exports`
- NFS 文件系统维护命令：`/usr/bin/exportfs`
- 共享资源的日志文件：`/var/lib/nfs/*tab`
- 客户端查询共享资源命令：`/usr/sbin/showmount`
- 端口配置：`/etc/sysconfig/nfs`

### [#](https://www.funtl.com/zh/service-mesh-kubernetes/准备数据持久化.html#nfs-服务端配置)NFS 服务端配置

在 NFS 服务器端的主要配置文件为 `/etc/exports` 时，通过此配置文件可以设置共享文件目录。每条配置记录由 NFS 共享目录、NFS 客户端地址和参数这 3 部分组成，格式如下：

```text
[NFS 共享目录] [NFS 客户端地址 1 (参数 1, 参数 2, 参数 3……)] [客户端地址 2 (参数 1, 参数 2, 参数 3……)]
```

- NFS 共享目录：服务器上共享出去的文件目录
- NFS 客户端地址：允许其访问的 NFS 服务器的客户端地址，可以是客户端 IP 地址，也可以是一个网段 (192.168.141.0/24)
- 访问参数：括号中逗号分隔项，主要是一些权限选项

#### [#](https://www.funtl.com/zh/service-mesh-kubernetes/准备数据持久化.html#访问权限参数)访问权限参数

| 序号 | 选项 | 描述                                   |
| ---- | ---- | -------------------------------------- |
| 1    | ro   | 客户端对于共享文件目录为只读权限。默认 |
| 2    | rw   | 客户端对于共享文件目录具有读写权限     |

#### [#](https://www.funtl.com/zh/service-mesh-kubernetes/准备数据持久化.html#用户映射参数)用户映射参数

| 序号 | 选项           | 描述                                                         |
| ---- | -------------- | ------------------------------------------------------------ |
| 1    | root_squash    | 使客户端使用 root 账户访冋时，服务器映射为服务器本地的匿名账号 |
| 2    | no_root_squash | 客户端连接服务端时如果使用的是 root，那么也拥有对服务端分享的目录的 root 权限 |
| 3    | all_squash     | 将所有客户端用户请求映射到匿名用户或用户组（nfsnobody)       |
| 4    | no_all_squash  | 与上相反。默认                                               |
| 5    | anonuid=xxx    | 将远程访问的所有用户都映射为匿名用户，并指定该用户为本地用户(UID=xxx) |
| 6    | anongid=xxx    | 将远程访问的所有用户组都映射为匿名用户组账户，并指定该匿名用户组账户为本地用户组账户(GUI=xxx) |

#### [#](https://www.funtl.com/zh/service-mesh-kubernetes/准备数据持久化.html#其它配置参数)其它配置参数

| 序号 | 选项       | 描述                                                         |
| ---- | ---------- | ------------------------------------------------------------ |
| 1    | sync       | 同步写操作，数据写入存储设备后返回成功信息。默认             |
| 2    | async      | 异步写提作，数据在未完全写入存储设备前就返回成功信息，实际还在内存， |
| 3    | wdelay     | 延迟写入选项，将多个写提请求合并后写入硬盘，减少 I/O 次数， NFS 非正常关闭数据可能丢失。默认 |
| 4    | no_wdelay  | 与上相反，不与 async 同时生效，如果 NFS 服务器主要收到小且不相关的请求，该选项实际会降低性能 |
| 5    | subtree    | 若输出目录是一个子目录，则 NFS 服务器将检查其父目录的权限。默认 |
| 6    | no_subtree | 即使输出目录是一个子目录， NFS 服务器也不检查其父目录的权限，这样可以提高效率 |
| 7    | secure     | 限制客户端只能从小于 1024 的 TCP/IP 端口连接 NFS 服务器。默认 |
| 8    | insecure   | 允许客户端从大于 1024 的 TCP/IP 端口连接服务器               |

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/准备数据持久化.html#安装-nfs-服务端)安装 NFS 服务端

由于 NFS 是一套分布式文件系统，我们再创建一台独立的虚拟机作为我们 NFS 服务端，配置如下

| 主机名             | IP              | 系统                | CPU/内存 | 磁盘 |
| ------------------ | --------------- | ------------------- | -------- | ---- |
| kubernetes-volumes | 192.168.141.140 | Ubuntu Server 18.04 | 2核2G    | 20G  |

- 创建一个目录作为共享文件目录

```bash
mkdir -p /usr/local/kubernetes/volumes
```

- 给目录增加读写权限

```bash
chmod a+rw /usr/local/kubernetes/volumes
```



- 安装 NFS 服务端

```bash
apt-get update
apt-get install -y nfs-kernel-server
```

- 配置 NFS 服务目录，打开文件

   

  ```
  vi /etc/exports
  ```

  ，在尾部新增一行，内容如下

  - `/usr/local/kubernetes/volumes`：作为服务目录向客户端开放
  - *：表示任何 IP 都可以访问
  - rw：读写权限
  - sync：同步权限
  - no_subtree_check：表示如果输出目录是一个子目录，NFS 服务器不检查其父目录的权限

```text
/usr/local/kubernetes/volumes *(rw,sync,no_subtree_check)
```

- 重启服务，使配置生效

```bash
/etc/init.d/nfs-kernel-server restart
```

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/准备数据持久化.html#安装-nfs-客户端)安装 NFS 客户端

安装客户端的目的是验证是否可以上传文件到服务端，安装命令如下

```bash
apt-get install -y nfs-common
```

- 创建 NFS 客户端挂载目录

```bash
mkdir -p /usr/local/kubernetes/volumes-mount
```



- 将 NFS 服务器的 `/usr/local/kubernetes/volumes` 目录挂载到 NFS 客户端的 `/usr/local/kubernetes/volumes-mount` 目录

```bash
mount 192.168.141.140:/usr/local/kubernetes/volumes /usr/local/kubernetes/volumes-mount
```

- 使用 `df` 命令查看挂载信息

```bash
df

# 输出如下
Filesystem                                    1K-blocks    Used Available Use% Mounted on
udev                                             977556       0    977556   0% /dev
tmpfs                                            201732    1252    200480   1% /run
/dev/mapper/ubuntu--vg-ubuntu--lv              19475088 5490916  12971848  30% /
tmpfs                                           1008648       0   1008648   0% /dev/shm
tmpfs                                              5120       0      5120   0% /run/lock
tmpfs                                           1008648       0   1008648   0% /sys/fs/cgroup
/dev/loop0                                        90624   90624         0 100% /snap/core/6964
/dev/loop1                                        93184   93184         0 100% /snap/core/6350
/dev/sda2                                        999320  214252    716256  24% /boot
tmpfs                                            201728       0    201728   0% /run/user/0
# 有此输出表示挂载成功
193.192.168.141.140:/usr/local/kubernetes/volumes  19475200 5490944  12972032  30% /usr/local/kubernetes/volumes-mount
```



## [#](https://www.funtl.com/zh/service-mesh-kubernetes/准备数据持久化.html#验证-nfs-服务)验证 NFS 服务

- 测试文件上传

```bash
ip addr > /usr/local/kubernetes/volumes-mount/test.txt
```

- 查看 `/usr/local/kubernetes/volumes` 目录下是否有 `test.txt` 文件，有则表示成功

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/准备数据持久化.html#取消-nfs-客户端挂载)取消 NFS 客户端挂载

> **注意：** 不要直接在挂载目录下执行，否则会报错

```bash
umount /usr/local/kubernetes/volumes-mount
```



# 实现数据持久化

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/实现数据持久化.html#本节视频)本节视频

[【（千锋教育）服务网格化 Service Mesh】Kubernetes-实现数据持久化](https://www.bilibili.com/video/av52359802/?p=18)

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/实现数据持久化.html#概述)概述

存储管理与计算管理是两个不同的问题。Persistent Volume 子系统，对存储的供应和使用做了抽象，以 API 形式提供给管理员和用户使用。要完成这一任务，我们引入了两个新的 API 资源：**Persistent Volume（持久卷）** 和 **Persistent Volume Claim（持久卷消费者）**。

Persistent Volume（PV）是集群之中的一块网络存储。跟 Node 一样，也是集群的资源。PV 跟 Volume (卷) 类似，不过会有独立于 Pod 的生命周期。这一 API 对象包含了存储的实现细节，例如 NFS、iSCSI 或者其他的云提供商的存储系统。Persistent Volume Claim (PVC) 是用户的一个请求。跟 Pod 类似，Pod 消费 Node 的资源，PVC 消费 PV 的资源。Pod 能够申请特定的资源（CPU 和内存）；Claim 能够请求特定的尺寸和访问模式（例如可以加载一个读写，以及多个只读实例）

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/实现数据持久化.html#pv-与-pvc)PV 与 PVC

PV 是集群的资源。PVC 是对这一资源的请求，也是对资源的所有权的检验。PV 和 PVC 之间的互动遵循如下的生命周期。

- **供应：** 集群管理员会创建一系列的 PV。这些 PV 包含了为集群用户提供的真实存储资源，它们可利用 Kubernetes API 来消费。
- **绑定：** 用户创建一个包含了容量和访问模式的持久卷申请。Master 会监听 PVC 的产生，并尝试根据请求内容查找匹配的 PV，并把 PV 和 PVC 进行绑定。用户能够获取满足需要的资源，并且在使用过程中可能超出请求数量。如果找不到合适的卷，这一申请就会持续处于非绑定状态，一直到出现合适的 PV。例如一个集群准备了很多的 50G 大小的持久卷，（虽然总量足够）也是无法响应 100G 的申请的，除非把 100G 的 PV 加入集群。
- **使用：** Pod 把申请作为卷来使用。集群会通过 PVC 查找绑定的 PV，并 Mount 给 Pod。对于支持多种访问方式的卷，用户在使用 PVC 作为卷的时候，可以指定需要的访问方式。一旦用户拥有了一个已经绑定的 PVC，被绑定的 PV 就归该用户所有了。用户的 Pods 能够通过在 Pod 的卷中包含的 PVC 来访问他们占有的 PV。
- **释放：** 当用户完成对卷的使用时，就可以利用 API 删除 PVC 对象了，而且他还可以重新申请。删除 PVC 后，对应的卷被视为 “被释放”，但是这时还不能给其他的 PVC 使用。之前的 PVC 数据还保存在卷中，要根据策略来进行后续处理。
- **回收：** PV 的回收策略向集群阐述了在 PVC 释放卷的时候，应如何进行后续工作。目前可以采用三种策略：保留，回收或者删除。保留策略允许重新申请这一资源。在持久卷能够支持的情况下，删除策略会同时删除持久卷以及 AWS EBS/GCE PD 或者 Cinder 卷中的存储内容。如果插件能够支持，回收策略会执行基础的擦除操作（`rm -rf /thevolume/*`），这一卷就能被重新申请了。

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/实现数据持久化.html#定义-pv)定义 PV

### [#](https://www.funtl.com/zh/service-mesh-kubernetes/实现数据持久化.html#持久卷插件)持久卷插件

持久卷是以插件方式实现的，目前支持的插件如下：

- GCEPersistentDisk
- AWSElasticBlockStore
- **NFS（我们采用的是该方案）**
- iSCSI
- RBD (Ceph Block Device)
- Glusterfs
- HostPath (单节点测试使用)
- 本地持久卷

### [#](https://www.funtl.com/zh/service-mesh-kubernetes/实现数据持久化.html#yaml-配置)YAML 配置

创建一个名为 `nfs-pv-mysql.yml` 的配置文件

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: nfs-pv-mysql
spec:
  # 设置容量
  capacity:
    storage: 5Gi
  # 访问模式
  accessModes:
    # 该卷能够以读写模式被多个节点同时加载
    - ReadWriteMany
  # 回收策略，这里是基础擦除 `rm-rf/thevolume/*`
  persistentVolumeReclaimPolicy: Recycle
  nfs:
    # NFS 服务端配置的路径
    path: "/usr/local/kubernetes/volumes"
    # NFS 服务端地址
    server: 192.168.141.140
    readOnly: false
```

```bash
# 部署
kubectl create -f nfs-pv-mysql.yml
# 删除
kubectl delete -f nfs-pv-mysql.yml
# 查看
kubectl get pv
NAME           CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM   STORAGECLASS   REASON   AGE
nfs-pv-mysql   5Gi        RWX            Recycle          Available                                   29m
```

### [#](https://www.funtl.com/zh/service-mesh-kubernetes/实现数据持久化.html#配置说明)配置说明

#### [#](https://www.funtl.com/zh/service-mesh-kubernetes/实现数据持久化.html#capacity（容量）)Capacity（容量）

一般来说，PV 会指定存储容量。这里需要使用 PV 的 capcity 属性。目前存储大小是唯一一个能够被申请的指标，今后会加入更多属性，例如 IOPS，吞吐能力等。

#### [#](https://www.funtl.com/zh/service-mesh-kubernetes/实现数据持久化.html#accessmodes（访问模式）)AccessModes（访问模式）

只要资源提供者支持，持久卷能够被用任何方式加载到主机上。每种存储都会有不同的能力，每个 PV 的访问模式也会被设置成为该卷所支持的特定模式。例如 NFS 能够支持多个读写客户端，但是某个 NFS PV 可能会在服务器上以只读方式使用。每个 PV 都有自己的一系列的访问模式，这些访问模式取决于 PV 的能力。访问模式的可选范围如下：

- ReadWriteOnce：该卷能够以读写模式被加载到一个节点上
- ReadOnlyMany：该卷能够以只读模式加载到多个节点上
- ReadWriteMany：该卷能够以读写模式被多个节点同时加载

在 CLI 下，访问模式缩写为：

- RWO：ReadWriteOnce
- ROX：ReadOnlyMany
- RWX：ReadWriteMany

另外，一个卷不论支持多少种访问模式，同时只能以一种访问模式加载。例如一个 GCE Persistent Disk 既能支持 ReadWriteOnce，也能支持 ReadOnlyMany。

#### [#](https://www.funtl.com/zh/service-mesh-kubernetes/实现数据持久化.html#recyclingpolicy（回收策略）)RecyclingPolicy（回收策略）

当前的回收策略可选值包括：

- Retain：人工重新申请
- Recycle：基础擦除（`rm-rf/thevolume/*`）
- Delete：相关的存储资产例如 AWS EBS，GCE PD 或者 OpenStack Cinder 卷一并删除

目前，只有 NFS 和 HostPath 支持 Recycle 策略，AWS EBS、GCE PD 以及 Cinder 卷支持 Delete 策略。

#### [#](https://www.funtl.com/zh/service-mesh-kubernetes/实现数据持久化.html#阶段（phase）)阶段（Phase）

一个卷会处于如下阶段之一：

- Available：可用资源，尚未被绑定到 PVC 上
- Bound：该卷已经被绑定
- Released：PVC 已经被删除，但该资源尚未被集群回收
- Failed：该卷的自动回收过程失败

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/实现数据持久化.html#定义-pvc)定义 PVC

创建一个名为 `nfs-pvc-mysql-myshop.yml` 的配置文件

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: nfs-pvc-mysql-myshop
spec:
  accessModes:
  # 需要使用和 PV 一致的访问模式
  - ReadWriteMany
  # 按需分配资源
  resources:
     requests:
       storage: 1Gi
```

```bash
# 部署
kubectl create -f nfs-pvc-mysql-myshop.yml
# 删除
kubectl delete -f nfs-pvc-mysql-myshop.yml
# 查看
kubectl get pvc
```



## [#](https://www.funtl.com/zh/service-mesh-kubernetes/实现数据持久化.html#部署-mysql8)部署 MySQL8

> **注意：** 要确保每台 Node 都安装了 NFS 客户端，`apt-get install -y nfs-common`

```yaml
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: mysql-myshop
spec:
  replicas: 1
  template:
    metadata:
      labels:
        name: mysql-myshop
    spec:
      containers:
        - name: mysql-myshop
          image: mysql
          # 只有镜像不存在时，才会进行镜像拉取
          imagePullPolicy: IfNotPresent
          ports:
            - containerPort: 3306
          # 同 Docker 配置中的 environment
          env:
            - name: MYSQL_ROOT_PASSWORD
              value: "123456"
          # 容器中的挂载目录
          volumeMounts:
            - name: nfs-vol-myshop
              mountPath: /var/lib/mysql
      volumes:
        # 挂载到数据卷
        - name: nfs-vol-myshop
          persistentVolumeClaim:
            claimName: nfs-pvc-mysql-myshop
---
apiVersion: v1
kind: Service
metadata:
  name: mysql-myshop
spec:
  ports:
    - port: 3306
      targetPort: 3306
  type: LoadBalancer
  selector:
    name: mysql-myshop
```

### [#](https://www.funtl.com/zh/service-mesh-kubernetes/实现数据持久化.html#解决权限问题)解决权限问题

当你使用 `kubectl create -f ` 部署后，你会发现 Pod 状态为 Error，容器无法正常启动的情况，我们可以使用 `kubectl logs ` 看到一条日志

```text
chown: changing ownership of '/var/lib/mysql/': Operation not permitted
```

1

解决方案是在 NFS 服务端配置中增加一个参数 `no_root_squash`，即将配置修改为：`/usr/local/kubernetes/volumes *(rw,sync,no_subtree_check,no_root_squash)`

### [#](https://www.funtl.com/zh/service-mesh-kubernetes/实现数据持久化.html#测试运行)测试运行

部署成功后可以使用 `kubectl get service` 查看我们 MySQL 的运行端口，再使用连接工具连接会报如下错误

![img](https://www.funtl.com/assets1/Lusifer_20190609212326.png)

意思为无法使用密码的方式登录，在 Docker 部署时我们可以在 YAML 中配置相关参数解决这个问题；下一节我们讲解在 Kubernetes 中采用 **ConfigMap** 的方式配置 MySQL

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/实现数据持久化.html#附：imagepullpolicy)附：ImagePullPolicy

支持三种 ImagePullPolicy

- **Always：** 不管镜像是否存在都会进行一次拉取
- **Never：** 不管镜像是否存在都不会进行拉取
- **IfNotPresent：** 只有镜像不存在时，才会进行镜像拉取

注意

- 默认为 `IfNotPresent`，但 `:latest` 标签的镜像默认为 `Always`
- 拉取镜像时 Docker 会进行校验，如果镜像中的 MD5 码没有变，则不会拉取镜像数据
- 生产环境中应该尽量避免使用 `:latest` 标签，而开发环境中可以借助 `:latest` 标签自动拉取最新的镜像



# Kubernetes ConfigMap

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/ConfigMap.html#本节视频)本节视频

[【（千锋教育）服务网格化 Service Mesh】Kubernetes-ConfigMap](https://www.bilibili.com/video/av52359802/?p=19)

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/ConfigMap.html#概述)概述

ConfigMap 是用来存储配置文件的 Kubernetes 资源对象，所有的配置内容都存储在 etcd 中。它可以被用来保存单个属性，也可以用来保存整个配置文件或者 JSON 二进制对象。ConfigMap API 资源提供了将配置数据注入容器的方式，同时保证该机制对容器来说是透明的。配置应该从 Image 内容中解耦，以此来保持容器化应用程序的可移植性。

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/ConfigMap.html#使用-configmap-配置-mysql)使用 ConfigMap 配置 MySQL

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: mysql-myshop-config
data:
  # 这里是键值对数据
  mysqld.cnf: |
    [client]
    port=3306
    [mysql]
    no-auto-rehash
    [mysqld]
    skip-host-cache
    skip-name-resolve
    default-authentication-plugin=mysql_native_password
    character-set-server=utf8mb4
    collation-server=utf8mb4_general_ci
    explicit_defaults_for_timestamp=true
    lower_case_table_names=1
---
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: mysql-myshop
spec:
  replicas: 1
  template:
    metadata:
      labels:
        name: mysql-myshop
    spec:
      containers:
        - name: mysql-myshop
          image: mysql
          imagePullPolicy: IfNotPresent
          ports:
            - containerPort: 3306
          env:
            - name: MYSQL_ROOT_PASSWORD
              value: "123456"
          volumeMounts:
            # 以数据卷的形式挂载 MySQL 配置文件目录
            - name: cm-vol-myshop
              mountPath: /etc/mysql/conf.d
            - name: nfs-vol-myshop
              mountPath: /var/lib/mysql
      volumes:
        # 将 ConfigMap 中的内容以文件形式挂载进数据卷
        - name: cm-vol-myshop
          configMap:
            name: mysql-myshop-config
            items:
                # ConfigMap 中的 Key
              - key: mysqld.cnf
                # ConfigMap Key 匹配的 Value 写入名为 mysqld.cnf 的文件中
                path: mysqld.cnf
        - name: nfs-vol-myshop
          persistentVolumeClaim:
            claimName: nfs-pvc-mysql-myshop
---
apiVersion: v1
kind: Service
metadata:
  name: mysql-myshop
spec:
  ports:
    - port: 3306
      targetPort: 3306
      nodePort: 32036
  type: LoadBalancer
  selector:
    name: mysql-myshop
```

```bash
# 查看 ConfigMap
kubectl get cm
kubectl describe cm <ConfigMap Name>
```



# Kubernetes Dashboard

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/Dashboard.html#本节视频)本节视频

[【（千锋教育）服务网格化 Service Mesh】Kubernetes-Dashboard](https://www.bilibili.com/video/av52359802/?p=20)

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/Dashboard.html#概述)概述

Kubernetes Dashboard 是 Kubernetes 集群的 Web UI，用于管理集群。

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/Dashboard.html#安装)安装

GitHub 地址：[Kubernetes Dashboard](https://github.com/kubernetes/dashboard)

下载配置文件

```bash
wget https://raw.githubusercontent.com/kubernetes/dashboard/v1.10.1/src/deploy/recommended/kubernetes-dashboard.yaml
```



修改配置如下

```yaml
# 省略部分代码...

# ------------------- Dashboard Deployment ------------------- #

kind: Deployment
apiVersion: apps/v1
metadata:
  labels:
    k8s-app: kubernetes-dashboard
  name: kubernetes-dashboard
  namespace: kube-system
spec:
  replicas: 1
  revisionHistoryLimit: 10
  selector:
    matchLabels:
      k8s-app: kubernetes-dashboard
  template:
    metadata:
      labels:
        k8s-app: kubernetes-dashboard
    spec:
      containers:
      - name: kubernetes-dashboard
        # 修改镜像地址为阿里云
        image: registry.aliyuncs.com/google_containers/kubernetes-dashboard-amd64:v1.10.1
        ports:
        - containerPort: 8443
          protocol: TCP
        args:
          - --auto-generate-certificates
        volumeMounts:
        - name: kubernetes-dashboard-certs
          mountPath: /certs
        - mountPath: /tmp
          name: tmp-volume
        livenessProbe:
          httpGet:
            scheme: HTTPS
            path: /
            port: 8443
          initialDelaySeconds: 30
          timeoutSeconds: 30
      volumes:
      - name: kubernetes-dashboard-certs
        secret:
          secretName: kubernetes-dashboard-certs
      - name: tmp-volume
        emptyDir: {}
      serviceAccountName: kubernetes-dashboard
      tolerations:
      - key: node-role.kubernetes.io/master
        effect: NoSchedule

---
# ------------------- Dashboard Service ------------------- #

kind: Service
apiVersion: v1
metadata:
  labels:
    k8s-app: kubernetes-dashboard
  name: kubernetes-dashboard
  namespace: kube-system
spec:
  # 修改类型为 NodePort 访问
  type: NodePort
  ports:
    - port: 443
      targetPort: 8443
      # 设置端口号为 30001
      nodePort: 30001
  selector:
    k8s-app: kubernetes-dashboard
```



部署到集群

```bash
# 部署
kubectl create -f kubernetes-dashboard.yaml

# 查看
kubectl -n kube-system get pods
kubectl -n kube-system get service kubernetes-dashboard
kubectl -n kube-system describe service kubernetes-dashboard
```



## [#](https://www.funtl.com/zh/service-mesh-kubernetes/Dashboard.html#访问)访问

需要使用 NodeIP:30001 访问 Dashboard，因为证书原因除火狐浏览器外其它浏览器无法直接打开页面

Chrome 浏览器显示如下

![img](https://www.funtl.com/assets1/Lusifer_20190610071425.png)

Firefox 浏览器显示如下

![img](https://www.funtl.com/assets1/Lusifer_20190610071443.png)

点击 **接受风险并继续** 即可显示欢迎界面

![img](https://www.funtl.com/assets1/Lusifer_20190610072653.png)

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/Dashboard.html#登录)登录

我们采用 Token 方式登录

- 创建登录账号，创建一个名为 `dashboard-adminuser.yaml` 的配置文件

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: admin-user
  namespace: kube-system
```

```bash
kubectl create -f dashboard-adminuser.yaml
```

- 打印 Token 信息

```bash
kubectl -n kube-system describe secret $(kubectl -n kube-system get secret | grep admin-user | awk '{print $1}')

# 输出如下
Name:         admin-user-token-86cz9
Namespace:    kube-system
Labels:       <none>
Annotations:  kubernetes.io/service-account.name: admin-user
              kubernetes.io/service-account.uid: 3902d3d4-8b13-11e9-8089-000c29d49c77

Type:  kubernetes.io/service-account-token

Data
====
ca.crt:     1025 bytes
namespace:  11 bytes
token:      eyJhbGciOiJSUzI1NiIsImtpZCI6IiJ9.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJrdWJlLXN5c3RlbSIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJhZG1pbi11c2VyLXRva2VuLTg2Y3o5Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZXJ2aWNlLWFjY291bnQubmFtZSI6ImFkbWluLXVzZXIiLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC51aWQiOiIzOTAyZDNkNC04YjEzLTExZTktODA4OS0wMDBjMjlkNDljNzciLCJzdWIiOiJzeXN0ZW06c2VydmljZWFjY291bnQ6a3ViZS1zeXN0ZW06YWRtaW4tdXNlciJ9.pA44wyarsahOwqH7X7RVlcdB1k3_j-L3gwOYlTQ4_Lu5ZmfXDFlhqN-Q1tdryJes_V1Nj_utocnXBAxsGzOGaVR4Te4oli3htSepI9MrggQAyeC3C0_QANXGCE6V5L6B5tGZ6tDsY92VDnlvz2N6OrHaH2IJJd2DlxzYvAPvfAFuPeHWuPeVxUisMfXeW42S7US6skZwbZ06JrPYAFxHjqv3zoxRxI8-bmekltvOamsrL0pAXvIUzaowgbjiQb2NgeLAw9O6qfYcz5DAi2C-7G_yAcve6pgnWcIGhVpKoim9DfJUhe1SVx4H4X5Na6GVaaD6FdUIb7UOgsO1FVpTPw
```



- 将 Token 输入浏览器，成功登陆后效果如下

![img](https://www.funtl.com/assets1/Lusifer_20190610081109.png)

#  Kubectl 与 Docker 命令

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/命令-kubectl-与-docker.html#概述)概述

Docker 命令和 Kubectl 命令有很多相似的地方，Docker 操作容器，Kubectl 操作 Pod（容器的集合）等

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/命令-kubectl-与-docker.html#运行容器)运行容器

- docker：`docker run -d --restart=always -e DOMAIN=cluster --name nginx-app -p 80:80 nginx`
- kubectl：
  - `kubectl run --image=nginx nginx-app --port=80 --env="DOMAIN=cluster"`
  - `kubectl expose deployment nginx-app --port=80 --name=nginx-http`

> **注意：** `kubectl run` 会创建一个 **Deployment** 并且默认会在后台运行，以上面的代码为例它的名称为 **nginx-app**。默认情况 Deployment 并不会将端口暴露出去，所以我们还需要使用 `kubectl expose` 暴露端口以供访问，此时还会创建一个同名的 **Service**

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/命令-kubectl-与-docker.html#查看已运行的容器)查看已运行的容器

- docker：`docker ps`
- kubectl：
  - `kubectl get pods`
  - `kubectl get deployment`
  - `kubectl get service`

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/命令-kubectl-与-docker.html#交互式进入容器)交互式进入容器

- docker：`docker exec -it <容器 ID/NAME> /bin/bash`
- kubectl：`kubectl exec -it <容器名> -- /bin/bash`

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/命令-kubectl-与-docker.html#打印日志)打印日志

- docker：`docker logs -f <容器 ID/NAME>`
- kubectl：`kubectl logs -f <容器名>`

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/命令-kubectl-与-docker.html#停止和删除容器)停止和删除容器

- docker：
  - `docker stop <容器 ID/NAME>`
  - `docker rm <容器 ID/NAME>`
- kubectl：
  - `kubectl delete deployment `
  - `kubectl delete service `

> **注意：** 不要直接删除 Pod，使用 kubectl 请删除拥有该 Pod 的 Deployment。如果直接删除 Pod，则 Deployment 将会重新创建该 Pod。

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/命令-kubectl-与-docker.html#查看版本)查看版本

- docker：`docker version`
- kubectl：`kubectl version`

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/命令-kubectl-与-docker.html#查看环境信息)查看环境信息

- docker：`docker info`
- kubectl：`kubectl cluster-info`



# Kubectl 常用命令

> **小提示：** 所有命令前都可以加上 `watch` 命令来观察状态的实时变化，如：`watch kubectl get pods --all-namespaces`

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/kubectl-常用命令.html#查看组件状态)查看组件状态

```bash
kubectl get cs
```

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/kubectl-常用命令.html#查看环境信息)查看环境信息

```bash
kubectl cluster-info
```

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/kubectl-常用命令.html#查看-node)查看 Node

```bash
kubectl get nodes -o wide
```

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/kubectl-常用命令.html#查看集群配置)查看集群配置

```bash
kubectl -n kube-system get cm kubeadm-config -oyaml
```

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/kubectl-常用命令.html#运行容器)运行容器

```bash
kubectl run nginx --image=nginx --replicas=2 --port=80
```



## [#](https://www.funtl.com/zh/service-mesh-kubernetes/kubectl-常用命令.html#暴露服务)暴露服务

```bash
kubectl expose deployment nginx --port=80 --type=LoadBalancer
```

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/kubectl-常用命令.html#查看命名空间)查看命名空间

```bash
kubectl get namespace
```

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/kubectl-常用命令.html#创建命名空间)创建命名空间

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: development
```



## [#](https://www.funtl.com/zh/service-mesh-kubernetes/kubectl-常用命令.html#查看容器)查看容器

```bash
kubectl get pods -o wide
kubectl get deployment -o wide
```



## [#](https://www.funtl.com/zh/service-mesh-kubernetes/kubectl-常用命令.html#查看服务)查看服务

```bash
kubectl get service -o wide
```



## [#](https://www.funtl.com/zh/service-mesh-kubernetes/kubectl-常用命令.html#查看详情)查看详情

```bash
kubectl describe pod <Pod Name>
kubectl describe deployment <Deployment Name>
kubectl describe service <Service Name>
```

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/kubectl-常用命令.html#查看日志)查看日志

```bash
kubectl logs -f <Pod Name>
```

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/kubectl-常用命令.html#删除容器和服务)删除容器和服务

```bash
kubectl delete deployment <Deployment Name>
kubectl delete service <Service Name>
```



## [#](https://www.funtl.com/zh/service-mesh-kubernetes/kubectl-常用命令.html#配置方式运行)配置方式运行

```bash
kubectl create -f <YAML>
```

1

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/kubectl-常用命令.html#配置方式删除)配置方式删除

```bash
kubectl delete -f <YAML>
```

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/kubectl-常用命令.html#查看配置)查看配置

```bash
kubeadm config view
kubectl config view
```



## [#](https://www.funtl.com/zh/service-mesh-kubernetes/kubectl-常用命令.html#查看-ingress)查看 Ingress

```bash
kubectl get ingress
```

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/kubectl-常用命令.html#查看持久卷)查看持久卷

```bash
kubectl get pv
```

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/kubectl-常用命令.html#查看持久卷消费者)查看持久卷消费者

```bash
kubectl get pvc
```

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/kubectl-常用命令.html#查看-configmap)查看 ConfigMap

```bash
kubectl get cm <ConfigMap Name>
```



## [#](https://www.funtl.com/zh/service-mesh-kubernetes/kubectl-常用命令.html#修改-configmap)修改 ConfigMap

```bash
kubectl edit cm <ConfigMap Name>
```