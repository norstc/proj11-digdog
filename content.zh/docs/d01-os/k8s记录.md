---
title: "k8s记录"
weight: 3
---

## 使用minikbue

### 安装

```shell
[root@localhost minikube-installer]# install minikube-linux-amd64 /usr/local/bin/minikube
[root@localhost minikube-installer]# cd /usr/local/bin/
[root@localhost bin]# ll
总用量 92M
-rwxr-xr-x 1 root root 92M  4月 26 17:05 minikube
[root@localhost bin]# minikube start
* Bigcloud 21.10 上的 minikube v1.33.0
* Unable to pick a default driver. Here is what was considered, in preference order:
  - kvm2: Not healthy: libvirt group membership check failed:
user is not a member of the appropriate libvirt group
  - kvm2：建议：Check that libvirtd is properly installed and that you are a member of the appropriate libvirt group (remember to relogin for group changes to take effect!) <https://minikube.sigs.k8s.io/docs/reference/drivers/kvm2/>
* 或者你也可以安装以下驱动程序：
  - docker: Not installed: exec: "docker": executable file not found in $PATH
  - podman: Not installed: exec: "podman": executable file not found in $PATH
  - qemu2: Not installed: exec: "qemu-system-x86_64": executable file not found in $PATH
  - virtualbox: Not installed: unable to find VBoxManage in $PATH

X 因 DRV_NOT_HEALTHY 错误而退出：找到个驱动程序，但没有一个是健康的。有关如何修复已安装的驱动程序的建议，请参阅上文。

[root@localhost bin]# 
```

### 启动

dockerd是用root启动的， 所以需要用 --force

```shell
[root@localhost ~]# minikube start --force
* Bigcloud 21.10 上的 minikube v1.33.0
! 当提供 --force 参数时，minikube 将跳过各种验证，这可能会导致意外行为
* 自动选择 docker 驱动。其他选项：ssh, none
* The "docker" driver should not be used with root privileges. If you wish to continue as root, use --force.
* 如果您在VM中运行 minikube，请考虑使用 --driver=none:
*   https://minikube.sigs.k8s.io/docs/reference/drivers/none/
* 使用具有 root 权限的 Docker 驱动程序
* Starting "minikube" primary control-plane node in "minikube" cluster
* Pulling base image v0.0.43 ...
* 正在下载 Kubernetes v1.30.0 的预加载文件...
    > preloaded-images-k8s-v18-v1...:  342.90 MiB / 342.90 MiB  100.00% 6.14 Mi
    > index.docker.io/kicbase/sta...:  480.25 MiB / 480.29 MiB  99.99% 6.08 MiB
! minikube was unable to download gcr.io/k8s-minikube/kicbase:v0.0.43, but successfully downloaded docker.io/kicbase/stable:v0.0.43 as a fallback image
* Creating docker container (CPUs=2, Memory=2200MB) .../ INFO[2024-04-28T10:49:31.990453788+08:00] shim containerd-shim started                  address="/containerd-shim/moby/364c989ec0221eb090f5bae030e9be90cd6eb6aeb6be5a849fde8b86329ceae8/shim.sock" debug=false pid=194222
| INFO[2024-04-28T10:49:32.337718857+08:00] shim reaped                                   id=364c989ec0221eb090f5bae030e9be90cd6eb6aeb6be5a849fde8b86329ceae8
INFO[2024-04-28T10:49:32.347706572+08:00] ignoring event                                module=libcontainerd namespace=moby topic=/tasks/delete type="*events.TaskDelete"
- INFO[2024-04-28T10:49:32.552004922+08:00] shim containerd-shim started                  address="/containerd-shim/moby/2643547577d4c8d7a4f70a36a13feb4d218c157a708c48b9187cec91ccf58c33/shim.sock" debug=false pid=194312
- INFO[2024-04-28T10:49:36.186636949+08:00] shim reaped                                   id=2643547577d4c8d7a4f70a36a13feb4d218c157a708c48b9187cec91ccf58c33
INFO[2024-04-28T10:49:36.196887271+08:00] ignoring event                                module=libcontainerd namespace=moby topic=/tasks/delete type="*events.TaskDelete"
/ INFO[2024-04-28T10:49:37.664125507+08:00] shim containerd-shim started                  address="/containerd-shim/moby/68dbccb955474825207e1cb2a3fcc0b3966d00cc8413a022d324a356a4b3b9f4/shim.sock" debug=false pid=194494

* 正在 Docker 26.0.1 中准备 Kubernetes v1.30.0…
  - 正在生成证书和密钥...
  - 正在启动控制平面...
  - 配置 RBAC 规则 ...
* 配置 bridge CNI (Container Networking Interface) ...
* 正在验证 Kubernetes 组件...
  - 正在使用镜像 gcr.io/k8s-minikube/storage-provisioner:v5
* 启用插件： default-storageclass, storage-provisioner
* kubectl 未找到。如果你需要使用它，请尝试：'minikube kubectl -- get pods -A'
* 完成！kubectl 现在已配置，默认使用"minikube"集群和"default"命名空间
```

### kubectl 命令

```shell
[root@localhost ~]# kubectl get po -A
-bash: kubectl：未找到命令
[root@localhost ~]# minikube kubectl -- get pods -A
    > kubectl.sha256:  64 B / 64 B [-------------------------] 100.00% ? p/s 0s
    > kubectl:  49.07 MiB / 49.07 MiB [--------------] 100.00% 4.98 MiB p/s 10s
NAMESPACE     NAME                               READY   STATUS    RESTARTS   AGE
kube-system   coredns-7db6d8ff4d-hvbnj           1/1     Running   0          19m
kube-system   etcd-minikube                      1/1     Running   0          20m
kube-system   kube-apiserver-minikube            1/1     Running   0          20m
kube-system   kube-controller-manager-minikube   1/1     Running   0          20m
kube-system   kube-proxy-8rc96                   1/1     Running   0          19m
kube-system   kube-scheduler-minikube            1/1     Running   0          20m
kube-system   storage-provisioner                1/1     Running   0          20m
[root@localhost ~]# kubectl
-bash: kubectl：未找到命令
[root@localhost ~]# minikube kubectl -- get po -A
NAMESPACE     NAME                               READY   STATUS    RESTARTS   AGE
kube-system   coredns-7db6d8ff4d-hvbnj           1/1     Running   0          20m
kube-system   etcd-minikube                      1/1     Running   0          20m
kube-system   kube-apiserver-minikube            1/1     Running   0          20m
kube-system   kube-controller-manager-minikube   1/1     Running   0          20m
kube-system   kube-proxy-8rc96                   1/1     Running   0          20m
kube-system   kube-scheduler-minikube            1/1     Running   0          20m
kube-system   storage-provisioner                1/1     Running   0          20m
```

### dashboard

启动dashboard 会卡住

```shell
[root@localhost ~]# minikube dashboard
* 正在开启 dashboard ...
  - 正在使用镜像 docker.io/kubernetesui/dashboard:v2.7.0
  - 正在使用镜像 docker.io/kubernetesui/metrics-scraper:v1.0.8
* Some dashboard features require the metrics-server addon. To enable all features please run:

        minikube addons enable metrics-server

* 正在验证 dashboard 运行情况 ...
* 正在启动代理...
* 正在验证 proxy 运行状况 ...
http://127.0.0.1:35993/api/v1/namespaces/kubernetes-dashboard/services/http:kubernetes-dashboard:/proxy/


```

## 需要安装docker

以bclinux为例， 这个是类centos的发行版， 使用yum 安装

```shell
uname -r
4.19.90-2107.6.0.0192.8.oe1.bclinux.x86_64
```

```shell
yum -y update
```

卸载

```shell
[root@localhost docker-installer]# yum remove docker  docker-common docker-selinux docker-engine
未找到匹配的参数: docker
未找到匹配的参数: docker-common
未找到匹配的参数: docker-selinux
未找到匹配的参数: docker-engine
没有软件包需要移除。
依赖关系解决。
无需任何处理。
完毕！
```

```shell
[root@localhost docker-installer]# yum install -y yum-utils device-mapper-persistent-data lvm2
BC-Linux-release - baseos                                                                                                                                                                                687 kB/s | 3.6 MB     00:05    
BC-Linux-release - everything                                                                                                                                                                            6.5 MB/s |  14 MB     00:02    
BC-Linux-release - update                                                                                                                                                                                5.6 MB/s | 9.7 MB     00:01    
BC-Linux-release - extras                                                                                                                                                                                984 kB/s | 156 kB     00:00    
未找到匹配的参数: yum-utils
软件包 thin-provisioning-tools-0.9.0-2.oe1.x86_64 已安装。
软件包 lvm2-8:2.03.09-5.oe1.x86_64 已安装。
错误：没有任何匹配: yum-utils
[root@localhost docker-installer]# 
```

加仓库

```shell
yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
添加仓库自：http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```

查看可以安装的dockers 版本

```shell
[root@localhost docker-installer]# yum list docker-ce --showduplicates | sort -r
Errors during downloading metadata for repository 'docker-ce-stable':
  - Status code: 404 for https://mirrors.aliyun.com/docker-ce/linux/centos/21.10LTS_SP2/x86_64/stable/repodata/repomd.xml (IP: 116.211.183.143)
错误：Failed to download metadata for repo 'docker-ce-stable': Cannot download repomd.xml: Cannot download repodata/repomd.xml: All mirrors were tried
Docker CE Stable - x86_64                       8.8 kB/s | 2.3 kB     00:00    
```

在加一个仓库

```shell
yum-config-manager --add-repo http://download.docker.com/linux/centos/docker-ce.repo
添加仓库自：http://download.docker.com/linux/centos/docker-ce.repo
```

没有合适的仓库， 用手动方式安装docker

参考 [官方文档]https://docs.docker.com/engine/install/binaries/

### 下载docker

https://download.docker.com/linux/static/stable/x86_64/

选择目前适配的版本

```shell
docker -v
Docker version 18.09.7, build 2d0083d
```

docker-18.09.7.tgz

### tar解压

```shell
[root@localhost docker-installer]# ll
总用量 46M
-rw-r--r-- 1 root root 46M  4月 28 09:43 docker-18.09.7.tgz
-rw-r--r-- 1 root root 22K  4月 26 17:17 install-docker.sh
[root@localhost docker-installer]# tar xzvf ./docker-18.09.7.tgz 
docker/
docker/docker
docker/docker-init
docker/ctr
docker/docker-proxy
docker/runc
docker/containerd
docker/dockerd
docker/containerd-shim
[root@localhost docker-installer]# pwd
/root/docker-installer
[root@localhost docker-installer]# 
```

### 设置执行文件路径

将 /root/docker-installer/docker  加到 path 中

编辑 /etc/profile

```profile
export DOCKER_HOME=/root/docker-installer/docker
export PATH=$DOCKER_HOME:$PATH
```

### 验证是否安装成功

```shell
 docker -v
Docker version 18.09.7, build 2d0083d
```

### 启动dockerd服务

minikube 需要这个后台服务， 并且建议不要用root 启动

```shell
[root@localhost ~]# ps -ef | grep docker
root      193536  192843  0 10:34 pts/0    00:00:00 dockerd
root      193544  193536  0 10:34 ?        00:00:00 containerd --config /var/run/docker/containerd/containerd.toml --log-level info
root      193670  192843  0 10:35 pts/0    00:00:00 grep --color=auto docker
```

```shell
[root@localhost ~]# minikube start
* Bigcloud 21.10 上的 minikube v1.33.0
* 自动选择 docker 驱动。其他选项：none, ssh
! 为了提高 Docker 驱动的性能，Upgrade Docker to a newer version (Minimum recommended version is 20.10.0, minimum supported version is 18.09.0, current version is 18.09.7)
* The "docker" driver should not be used with root privileges. If you wish to continue as root, use --force.
* 如果您在VM中运行 minikube，请考虑使用 --driver=none:
*   https://minikube.sigs.k8s.io/docs/reference/drivers/none/

X 因 DRV_AS_ROOT 错误而退出：docker 驱动不应使用 root 权限。

```

### 测试docker hello-world

```shell
[root@localhost ~]# docker run hello-world
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
c1ec31eb5944: Pull complete 
Digest: sha256:a26bff933ddc26d5cdf7faa98b4ae1e3ec20c4985e6f87ac0973052224d24302
Status: Downloaded newer image for hello-world:latest
INFO[2024-04-28T10:44:09.941800437+08:00] shim containerd-shim started                  address="/containerd-shim/moby/c9fcf2ffd36399157e255476f8a9f6c5dd526a78cfcfa6e0c9acc214bc63687b/shim.sock" debug=false pid=193817

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/

INFO[2024-04-28T10:44:10.353739840+08:00] shim reaped                                   id=c9fcf2ffd36399157e255476f8a9f6c5dd526a78cfcfa6e0c9acc214bc63687b
INFO[2024-04-28T10:44:10.359891137+08:00] ignoring event                                module=libcontainerd namespace=moby topic=/tasks/delete type="*events.TaskDelete"
```

