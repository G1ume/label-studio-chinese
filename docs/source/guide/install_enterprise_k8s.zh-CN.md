---
title: 在 Kubernetes 上部署 Label Studio Enterprise
short: 使用 Kubernetes 安装
tier: enterprise
type: guide
order: 0
order_enterprise: 69
meta_title: 在 Kubernetes 上部署 Label Studio Enterprise
meta_description: 在 Kubernetes（例如 Amazon Elastic Container Service for Kubernetes）上部署 Label Studio Enterprise，在可扩展的容器化环境中创建机器学习和数据科学项目。
section: "安装与设置"
parent_enterprise: "install_enterprise"
---

使用 Helm 3 在 Kubernetes 集群上部署 Label Studio Enterprise。您可以使用此 Helm Chart 将 Label Studio Enterprise 部署到 Kubernetes 集群上，并安装、升级和管理该应用程序。

您的 Kubernetes 集群可以自托管，也可以安装到 Amazon EKS 等平台。有关在 Amazon EKS 上部署应用的更多信息，请参阅 Amazon 教程[使用 Amazon Elastic Container Service for Kubernetes 部署 Kubernetes 应用程序](https://aws.amazon.com/getting-started/hands-on/deploy-kubernetes-app-amazon-eks/)。

<div class="enterprise-only">

!!! warning
    要安装 Label Studio 社区版，请参阅<a href="https://labelstud.io/guide/install_k8s.html">在 Kubernetes 上部署 Label Studio</a>。本页面专门针对 Label Studio 的企业版。

</div>

!!! note
    Label Studio Enterprise 的本地部署不支持学术许可证。

以下是 Label Studio Enterprise 部署的高层架构图，展示了主要组件。

<img src="/images/LSE_k8s_scheme.png"/>

!!! warning
    Label Studio Enterprise 2.2.9 已移除 MinIO 服务。

Label Studio Enterprise 运行在 Python 上，并使用 rqworker 执行其他任务。元数据和标注存储在捆绑的 PostgreSQL 版本中，作为持久化存储。如果您在云端托管 Label Studio Enterprise，请使用[云端持久化存储](persistent_storage.html)代替 MinIO。

## 在 Kubernetes 上安装 Label Studio Enterprise

如果您想在 Kubernetes 上安装 Label Studio Enterprise，并且您的 K8s 集群可以无限制地访问互联网，请按照以下步骤操作。

1. 确认您满足[必需软件前提条件](#Required-software-prerequisites)并查看[容量规划](#Capacity-planning)指南。
2. [准备 Kubernetes 集群](#Prepare-the-Kubernetes-cluster)。
3. [添加 Helm Chart 仓库](#Add-the-Helm-chart-repository)。
4. [配置 Kubernetes Secrets](#Configure-Kubernetes-secrets)
5. [配置持久化存储](persistent_storage.html)。
6. [配置 Ingress](ingress_config.html)。
7. [配置 values.yaml 文件](#Configure-values-yaml)。
8. （可选）[为 PostgreSQL 设置 TLS](#Optional-set-up-TLS-for-PostgreSQL)
9. （可选）[为 Redis 设置 TLS](#Optional-set-up-TLS-for-Redis)
10. [使用 Helm 在 Kubernetes 集群上安装 Label Studio Enterprise](#Use-Helm-to-install-Label-Studio-Enterprise-on-your-Kubernetes-cluster)。

如果您通过代理从 Kubernetes 集群访问互联网，或者集群处于离线环境，请参阅[在没有公网访问的情况下安装 Label Studio Enterprise](install_k8s_airgapped.html)。

### 必需软件前提条件

- **Kubernetes** — 版本 1.17 或更高
- **Helm** — 版本 3.6.3 或更高
- **Redis** — 版本 6.0.5 或更高
- **PostgreSQL** — 版本 13.0 或更高
- **持久化存储** — 具有 ReadWriteMany 访问模式的 PVC 或兼容 S3 的对象存储

Redis 和 PostgreSQL 是 Label Studio Enterprise 的必需组件，缺一不可。此 Chart 不包含也不安装 Redis 或 PostgreSQL；您必须预先配置好它们（例如，按照您公司的标准），并在 Helm values 中提供连接信息。

**持久化存储**必须使用以下之一：具有 **ReadWriteMany** 访问模式的 Persistent Volume Claim (PVC)，或兼容 S3 的对象存储。请参阅[设置持久化存储](persistent_storage.html)了解配置详情。

此 Chart 已经过测试，确认可与 [NGINX Ingress Controller](https://kubernetes.github.io/ingress-nginx/) 和 [cert-manager](https://cert-manager.io/docs/) 配合使用。有关 Label Studio 的 Ingress 设置更多信息，请参阅[为 Label Studio Kubernetes 部署设置 Ingress 控制器](ingress_config.html)。

您的 Kubernetes 集群可以自托管，也可以安装到 Amazon EKS 等平台。

### 容量规划

要规划 Kubernetes 集群的容量，请参考以下指南。

Label Studio Enterprise 对资源请求、资源限制和副本数有以下默认配置：

<div class="enterprise-only">

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

rqworker:
  queues:
    high:
      replicas: 1
    low:
      replicas: 1
    default:
      replicas: 4
    critical:
      replicas: 1
  resources:
    requests:
      memory: 256Mi
      cpu: 100m
    limits:
      memory: 1024Mi
      cpu: 1000m
```

</div>

在更改这些值之前，请先熟悉 Kubernetes 文档中的 [Pod 和容器资源管理](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/) 指南。

如果您选择更改这些默认设置，请考虑以下情况：

| 场景                                        | 调整措施                                                                                                     |
|---------------------------------------------|----------------------------------------------------------------------------------------------------------------|
| 超过 10 个并发标注者                         | 调整 `app` Pod 中 `resources` 的 requests 和 limits                                                             |
| 提高容错能力                                 | 增加 `app` 和/或 `rqworker` 服务的副本数                                                                       |
| 生产环境部署（副本数）                       | 副本数等于或大于 Kubernetes 集群中可用区的数量                                                                  |

#### RQ Worker 副本数

`default` 队列是最主要的队列。建议为 `default` 队列设置的副本数是其他队列的 4 倍。其他队列（`critical`、`high`、`low`）可以使用相同数量的副本。您可以从每个队列 1 个副本开始。

### 准备 Kubernetes 集群

在安装 Label Studio 之前，使用 [kubectl](https://kubernetes.io/docs/reference/kubectl/) 准备 Kubernetes 集群。

安装 Label Studio Enterprise 并使用 Helm Chart 设置 PostgreSQL 和 Redis 数据库来存储相关的 Label Studio Enterprise 配置和标注。您必须在使用 Helm 安装时在 YAML 文件中为您的部署指定特定的配置值。

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

<div class="enterprise-only">

### 配置 Kubernetes Secrets

1. 确保您有许可证密钥和 Docker Hub 凭据，或向 Heartex 团队索取。
2. 创建一个密钥用于从 Docker 仓库拉取最新的 Label Studio Enterprise 镜像。在集群的命令行中运行以下命令：
    ```shell
    kubectl create secret docker-registry heartex-pull-key \
        --docker-server=https://index.docker.io/v2/ \
        --docker-username=heartexlabs \
        --docker-password=<CUSTOMER_PASSWORD>
    ```
3. 将 Label Studio Enterprise 许可证创建为 Kubernetes Secret。您可以将其指定为文件或指定的 URL。
   在命令行中，将许可证指定为文件：
   ```shell
   kubectl create secret generic lse-license --from-file=license=path/to/lic
   ```
   或者在命令行中，将许可证指定为 URL：
   ```shell
   kubectl create secret generic lse-license --from-literal=license=https://lic.heartex.ai/db/<CUSTOMER_LICENSE_ID>
   ```

</div>

<div class="enterprise-only">

### 配置 values.yaml

您必须为 Label Studio Enterprise 部署配置一个 `values.yaml` 文件。以下文件包含了 Label Studio 最小安装的默认值。此 Chart 已经过测试，确认可与 [NGINX Ingress Controller](https://kubernetes.github.io/ingress-nginx/) 和 [cert-manager](https://cert-manager.io/docs/) 配合使用。

Label Studio Enterprise 最小安装的示例 `values.yaml` 文件：
```yaml
global:
  image:
    repository: heartexlabs/label-studio-enterprise
    tag: REPLACE_ME

  imagePullSecrets:
    # 通过前面的 kubectl 命令定义
    - name: heartex-pull-key

  pgConfig:
    # PostgreSQL 实例主机名
    host: "postgresql"
    # PostgreSQL 数据库名称
    dbName: "my-database"
    # PostgreSQL 用户名
    userName: "postgres"
    # PostgreSQL 密码在 Kubernetes Secret 中的坐标信息
    password:
      secretName: "postgresql"
      secretKey: "postgresql-password"

  redisConfig:
    # Redis 连接字符串
    host: redis://host:port/db

  # extraEnvironmentVars 是要在部署中设置的额外环境变量列表，默认为空
  extraEnvironmentVars: {}
  # extraEnvironmentSecrets 是要在部署中设置的额外环境 Secret 列表，默认为空
  extraEnvironmentSecrets: {}

enterprise:
   enabled: true
   # 此值指向您创建的包含企业许可证的 Kubernetes Secret
   enterpriseLicense:
      secretName: "lse-license"
      secretKey: "license"

app:
  # 高可用（HA）模式：根据您的资源进行调整
  replicas: 1
  # Label Studio 的 Ingress 配置
  ingress:
    enabled: true
    host: studio.yourdomain.com
    # 您可能需要将 path 设置为 '/*' 以便与 ALB Ingress 控制器配合使用。
    path: /
    # Ingress 控制器所需的注解，默认为空
    annotations: {}
# 如果您有 cert-manager，请取消注释以下部分
#    tls:
#      - secretName: ssl-cert-studio.yourdomain.com
#        hosts:
#          - studio.yourdomain.com

# 默认的计算资源用于运行 Label Studio Enterprise 的基本安装。请根据业务需求进行调整
  resources:
    requests:
      memory: 1024Mi
      cpu: 1000m
    limits:
      memory: 6144Mi
      cpu: 4000m

rqworker:
   # HA 模式：根据您的业务需求/资源进行调整
   queues:
      high:
         replicas: 2
      low:
         replicas: 2
      default:
         replicas: 2
      critical:
         replicas: 2

postgresql:
  enabled: false

redis:
  enabled: false
```

根据您的环境调整这些默认值，将其复制到一个新文件中并保存为 `ls-values.yaml`。

!!! note
    对于更复杂的配置，您可以基于[所有可用 Helm 值列表](helm_values.html)创建自己的文件。

</div>

## 可选：为 PostgreSQL 设置 TLS
要配置 Label Studio Enterprise 使用 TLS 进行与 PostgreSQL 的客户端连接，请执行以下操作：

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

4. 使用 Helm 安装或升级 Label Studio Enterprise。

## 可选：为 Redis 设置 TLS
要配置 Label Studio Enterprise 使用 TLS 进行与 Redis 的客户端连接，请执行以下操作：

1. 为您的 Redis 实例启用 TLS，并保存根 TLS 证书、客户端证书及其密钥以供后续步骤使用。
2. 使用您的证书创建 Kubernetes Secret，将 `<PATH_TO_CA>`、`<PATH_TO_CLIENT_CRT>` 和 `<PATH_TO_CLIENT_KEY>` 替换为证书路径：

```shell
kubectl create secret generic <YOUR_SECRET_NAME> --from-file=ca.crt=<PATH_TO_CA> --from-file=client.crt=<PATH_TO_CLIENT_CRT> --from-file=client.key=<PATH_TO_CLIENT_KEY>
```
3. 使用新创建的 Kubernetes Secret 更新您的 `ls-values.yaml` 文件：

!!! note
    如果您使用的是自签名证书且主机无法验证，则必须将 `redisSslCertReqs` 设置为 `None`

```yaml
global:
  redisConfig:
    ssl:
      redisSslCertReqs: "required"
      redisSslSecretName: "<YOUR_SECRET_NAME>"
      redisSslCaCertsSecretKey: "ca.crt"
      redisSslCertFileSecretKey: "client.crt"
      redisSslKeyFileSecretKey: "client.key"
```

4. 使用 Helm 安装或升级 Label Studio Enterprise。

## 可选：设置 Redis 用户名和密码

使用以下选项之一为 Redis 设置密码和用户名：

**1. 通过 Kubernetes Secret 设置密码**。适用于以下情况：
* 您希望避免在 `values.yaml` 中嵌入凭据
* 您已经在集群中管理 Secret
* 您需要简单的认证，没有多个 Redis 用户且没有用户名

```yaml
global:
  redisConfig:
    host: "redis://redis.example.com:6379/1"
    password:
      secretName: "my-redis-secret"   # Kubernetes Secret 名称
      secretKey: "redis-password"      # Secret 中的键
```

**2. 在 URL 中设置用户名 + 密码**。适用于以下情况：
* Redis v.7 或更高版本，且启用了 ACL 用户
* 您需要专用 Redis 用户来进行权限范围控制
* 您需要快速、临时的设置或本地测试

```yaml
global:
  redisConfig:
    host: "redis://myuser:mypassword@redis.example.com:6379/1"
```

**3. 在环境变量中设置用户名 + 在 Secret 中设置密码**。适用于以下情况：
* Redis v.7 或更高版本，且启用了 ACL 用户
* 您希望保持密码保密，但仍需指定用户名

```yaml
global:
  redisConfig:
    host: "redis://redis.example.com:6379/1"
    password:
      secretName: "my-redis-secret"   # Kubernetes Secret 名称
      secretKey: "redis-password"      # Secret 中的键
  extraEnvironmentVars:
    REDIS_USERNAME: "myuser"           # 注入 Pod 环境中
```

## 使用 Helm 在 Kubernetes 集群上安装 Label Studio Enterprise

使用 Helm 在您的 Kubernetes 集群上安装 Label Studio Enterprise。提供您的自定义资源定义 YAML 文件。使用 `helm install` 命令的 `--set` 参数指定 Label Studio Enterprise 安装所需的任何环境变量。

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

## 使用 Helm 重启 Label Studio Enterprise

通过以下命令行操作重启您的 Helm Release：

1. 确定最新 Label Studio Enterprise Release 的 &lt;RELEASE_NAME&gt;：
```shell
helm list
```
2. 重启 Label Studio 的 rqworker：
```shell
kubectl rollout restart deployment/<RELEASE_NAME>-ls-rqworker
```
3. 重启 Label Studio Enterprise 应用：
```shell
kubectl rollout restart deployment/<RELEASE_NAME>-ls-app
```

## 使用 Helm 卸载 Label Studio Enterprise

要使用 Helm 卸载 Label Studio Enterprise，请删除配置。

在命令行中运行以下命令：
```shell
helm delete <RELEASE_NAME>
```
