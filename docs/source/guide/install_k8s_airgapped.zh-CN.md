---
title: 在没有公网访问的情况下安装 Label Studio
short: 离线服务器
tier: all
type: guide
order: 71
order_enterprise: 73
meta_title: 在没有公网访问的情况下安装 Label Studio
meta_description: 在没有公网访问的情况下安装 Label Studio，在离线环境中创建机器学习和数据科学项目。
section: "安装与设置"
parent: "install_k8s"
parent_enterprise: "install_enterprise_k8s"
---

如果您需要在通过代理阻止互联网访问的服务器上安装 Label Studio，或在不允许出站连接互联网的离线服务器上安装 Label Studio，请按照以下步骤操作：

- 如果您通过 HTTPS 代理从服务器访问互联网，请参阅[在 HTTPS 代理后安装](#Install-behind-an-HTTPS-proxy)。
- 如果您无法从服务器访问互联网，或使用其他代理，请参阅[在没有互联网访问或 HTTPS 代理的情况下安装](#Install-without-internet-access-or-HTTPS-proxy)。

### 在 HTTPS 代理后安装

!!! note
    如果您使用的是 SOCKS 代理，Helm 3 不支持 SOCKS 代理。请参阅[在没有互联网访问或 HTTPS 代理的情况下安装](#Install-without-internet-access-or-HTTPS-proxy)。

如果您的组织使用 HTTPS 代理来管理互联网访问，请执行以下操作。

<div class="opensource-only">

1. 与您的网络安全团队合作，将 `https://charts.heartex.com` 加入白名单，以便您可以访问 Helm Charts 进行部署。
2. 在 Label Studio 服务器上，使用 HTTPS 代理地址设置环境变量：
```shell
export HTTPS_PROXY=<your_proxy>
```
3. [使用 Helm 在 Kubernetes 集群上安装 Label Studio](install_k8s#Use-Helm-to-install-Label-Studio-on-your-Kubernetes-cluster)。

</div>

<div class="enterprise-only">

1. 与您的网络安全团队合作，将 `https://charts.heartex.com` 加入白名单，以便您可以访问 Helm Charts 进行部署。
2. 在 Label Studio 服务器上，使用 HTTPS 代理地址设置环境变量：
```shell
export HTTPS_PROXY=<your_proxy>
```
3. [使用 Helm 在 Kubernetes 集群上安装 Label Studio](install_enterprise_k8s#Use-Helm-to-install-Label-Studio-Enterprise-on-your-Kubernetes-cluster)。

</div>

### 在没有互联网访问或 HTTPS 代理的情况下安装

如果您无法使用 Helm 支持的代理或根本无法访问互联网，请按照以下步骤下载在离线 Kubernetes 集群上部署 Label Studio 所需的 Helm Charts。

!!! note
    您需要客户经理提供给您的 Label Studio 凭据才能下载 Helm Charts。

1. 下载最新版本的 Label Studio Helm Chart：
   ```shell
   helm repo add heartex https://charts.heartex.com/
   helm repo update heartex
   helm pull heartex/label-studio
   ```
2. 将下载的 `tar.gz` 压缩包传输到安装了 `kubectl` 和 `helm` 的主机上。
3. 解压 `tar.gz` 压缩包。
4. 安装 Label Studio，更新 `helm` 命令中的路径以指向您解压 Label Studio Helm Chart 的文件夹的相对路径。例如，如果您在当前目录中解压了压缩包，请运行以下命令：
```shell
helm install ls ./label-studio -f ls-values.yaml
```
