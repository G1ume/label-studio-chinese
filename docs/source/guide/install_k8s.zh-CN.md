---
title: 在 Kubernetes 上部署 Label Studio
short: 使用 Kubernetes 安装
tier: opensource
type: guide
order: 69
order_enterprise: 0
meta_title: 在 Kubernetes 上部署 Label Studio
meta_description: 在 Kubernetes（例如 Amazon Elastic Container Service for Kubernetes）上部署 Label Studio，在可扩展的容器化环境中创建机器学习和数据科学项目。
section: "安装与设置"
parent: "install"
---

使用 Helm 3 在 Kubernetes 集群上部署 Label Studio。您可以使用此 Helm Chart 将 Label Studio 部署到 Kubernetes 集群上，并安装、升级和管理该应用程序。

您的 Kubernetes 集群可以自托管，也可以安装到 Amazon EKS 等平台。有关在 Amazon EKS 上部署应用的更多信息，请参阅 Amazon 教程[使用 Amazon Elastic Container Service for Kubernetes 部署 Kubernetes 应用程序](https://aws.amazon.com/getting-started/hands-on/deploy-kubernetes-app-amazon-eks/)。

<div class="opensource-only">

!!! warning
    要安装 Label Studio 企业版，请参阅<a href="https://docs.humansignal.com/guide/install_enterprise_k8s">在 Kubernetes 上部署 Label Studio Enterprise</a>。本页面专门针对 Label Studio 的社区版本。

</div>

## 在 Kubernetes 上安装 Label Studio

如果您想在 Kubernetes 上安装 Label Studio，并且您的 K8s 集群可以无限制地访问互联网，请按照以下步骤操作。

1. 确认您满足[必需软件前提条件](#Required-software-prerequisites)并查看[容量规划](#Capacity-planning)指南。
2. [准备 Kubernetes 集群](#Prepare-the-Kubernetes-cluster)。
3. [添加 Helm Chart 仓库](#Add-the-Helm-chart-repository)。
4. （可选）设置[持久化存储](persistent_storage.html)。
5. （可选）配置 [Ingress](ingress_config.html)。
6. （可选）配置 [values.yaml](helm_values.html)。
7. （可选）[为 PostgreSQL 设置 TLS](#Optional-set-up-TLS-for-PostgreSQL)
8. [使用 Helm 在 Kubernetes 集群上安装 Label Studio](#Use-Helm-to-install-Label-Studio-on-your-Kubernetes-cluster)。

如果您通过代理从 Kubernetes 集群访问互联网，或者集群处于离线环境，请参阅[在没有公网访问的情况下安装 Label Studio](/guide/install_k8s_airgapped.html)。

### 必需软件前提条件

- Kubernetes 和 kubectl 版本 1.17 或更高
- Helm 版本 3.6.3 或更高

此 Chart 已经过测试，确认可与 [NGINX Ingress Controller](https://kubernetes.github.io/ingress-nginx/) 和 [cert-manager](https://cert-manager.io/docs/) 配合使用。有关 Label Studio 的 Ingress 设置更多信息，请参阅[为 Label Studio Kubernetes 部署设置 Ingress 控制器](ingress_config.html)。

您的 Kubernetes 集群可以自托管，也可以安装到 Amazon EKS 等平台。

### 容量规划

要规划 Kubernetes 集群的容量，请参考以下指南。

Label Studio 对资源请求、资源限制和副本数有以下默认配置：

```yaml
app:
  replicas: 1
  resources:
    requests:
      memory: 1024Mi
      cpu: 1000m
    limits:
      memory: 6144Mi
      cpu: 4000m
```

在更改这些值之前，请先熟悉 Kubernetes 文档中的 [Pod 和容器资源管理](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/) 指南。

如果您选择更改这些默认设置，请考虑以下情况：

| 场景                        | 调整措施                                                                                       |
| --------------------------- | ------------------------------------------------------------------------------------------------- |
| 超过 10 个并发标注者        | 调整 `app` Pod 中 `resources` 的 requests 和 limits                                               |
| 提高容错能力                | 增加 `app` Pod 的副本数                                                                          |
| 生产环境部署（副本数）       | 副本数等于或大于 Kubernetes 集群中可用区的数量                                                    |

### 准备 Kubernetes 集群

在安装 Label Studio 之前，使用 [kubectl](https://kubernetes.io/docs/reference/kubectl/) 准备 Kubernetes 集群。

### 添加 Helm Chart 仓库

添加 Helm Chart 仓库以方便安装和更新 Label Studio。

1. 在命令行中执行：
   ```shell
   helm repo add heartex https://charts.heartex.com/
   helm repo update heartex
   ```
2. 如果需要，检查可用版本：
   ```shell
   helm search repo heartex/label-studio
   ```

## 可选：为 PostgreSQL 设置 TLS

要配置 Label Studio 使用 TLS 进行与 PostgreSQL 的客户端连接，请执行以下操作：

1. 为您的 PostgreSQL 实例启用 TLS，并保存根 TLS 证书、客户端证书及其密钥以供后续步骤使用。
2. 使用您的证书创建 Kubernetes Secret，将 `<PATH_TO_CA>`、`<PATH_TO_CLIENT_CRT>` 和 `<PATH_TO_CLIENT_KEY>` 替换为证书路径：
```shell
kubectl create secret generic <YOUR_SECRET_NAME> --from-file=ca.crt=<PATH_TO_CA> --from-file=client.crt=<PATH_TO_CLIENT_CRT> --from-file=client.key=<PATH_TO_CLIENT_KEY>
```

3. 使用新创建的 Kubernetes Secret 更新您的 `ls-values.yaml` 文件：

!!! note
    如果 `POSTGRE_SSL_MODE: verify-ca`，则通过检查证书链直到存储在客户端上的根证书来验证服务器。如果 `POSTGRE_SSL_MODE: verify-full`，则将验证服务器主机名以确保其与服务器证书中存储的名称匹配。如果无法验证服务器证书，SSL 连接将失败。在大多数对安全性敏感的环境中，建议使用 `verify-full`。

```yaml
global:
  pgConfig:
    ssl:
      pgSslMode: "verify-full"
      pgSslSecretName: "<YOUR_SECRET_NAME>"
      pgSslRootCertSecretKey: "ca.crt"
      pgSslCertSecretKey: "client.crt"
      pgSslKeySecretKey: "client.key"
```

4. 使用 Helm 安装或升级 Label Studio。

## 使用 Helm 在 Kubernetes 集群上安装 Label Studio

使用 Helm 在您的 Kubernetes 集群上安装 Label Studio。提供您的自定义资源定义 YAML 文件。使用 `helm install` 命令的 `--set` 参数指定 Label Studio 安装所需的任何环境变量。

!!! note
    如果您要部署到生产环境，应该设置 `SSRF_PROTECTION_ENABLED: true` 环境变量。请参阅[保护 Label Studio](security#Enable-SSRF-protection-for-production-environments)。

在命令行中运行以下命令：

```shell
helm install <RELEASE_NAME> heartex/label-studio -f ls-values.yaml
```

安装完成后，检查 Kubernetes Pod 创建状态：

```shell
kubectl get pods
```

## 使用 Helm 重启 Label Studio

通过以下命令行操作重启您的 Helm Release：

1. 确定最新 Label Studio Release 的 &lt;RELEASE_NAME&gt;：
```shell
helm list
```

2. 重启 Label Studio 应用：
```shell
kubectl rollout restart deployment/<RELEASE_NAME>-ls-app
```

## 使用 Helm 升级 Label Studio

要使用 Helm 升级 Label Studio，请执行以下操作：

1. 确定 Label Studio 的最新标签版本，并在您的 `ls-values.yaml` 文件中添加/替换以下内容：
   ```yaml
   global:
     image:
       tag: "20210914.154442-d2d1935"
   ```
2. 更新 values 文件后，获取 Helm Chart 的最新更新：
   ```shell
   helm repo update heartex
   ```
3. 在命令行中运行以下命令来升级您的部署：
   ```shell
   helm upgrade <RELEASE_NAME> heartex/label-studio -f ls-values.yaml
   ```
   如果需要，您可以在命令行中指定版本：
   ```shell
   helm upgrade <RELEASE_NAME> heartex/label-studio -f ls-values.yaml --set global.image.tag=20210914.154442-d2d1935
   ```
   此命令会覆盖存储在 `ls-values.yaml` 中的标签值。在升级或重新部署实例时，您必须更新标签值以避免版本降级。

## 使用 Helm 卸载 Label Studio

要使用 Helm 卸载 Label Studio，请删除配置。

在命令行中运行以下命令：

```shell
helm delete <RELEASE_NAME>
```
