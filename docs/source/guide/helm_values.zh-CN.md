---
title: Label Studio Helm Chart 的可用 Helm 值
short: 可用 Helm 值
tier: all
type: guide
order: 72
order_enterprise: 74
meta_title: Label Studio Helm Chart 的可用 Helm 值
meta_description: 当您需要自定义 Label Studio Kubernetes 部署时，请参考这些可在 Helm Chart 中设置的可用 Helm 值。
section: "安装与设置"
parent: "install_k8s"
parent_enterprise: "install_enterprise_k8s"
---

<!-- 修复表格单元格中较长值的显示问题 -->
<style>
  td:first-child {
    max-width: 350px;
  }
  td:first-child code {
    white-space: normal;
    word-break: break-word;
    margin-left: 0;
    padding-left: 3px;
  }
  td:nth-child(3) {
    white-space: normal;
    word-break: break-word;
    max-width: 130px;
  }
  td:last-child {
    max-width: 150px;
  }
  td:last-child code {
    white-space: normal;
    word-break: break-all;
  }
</style>

<div class="opensource-only">

在配置 Label Studio 的 Kubernetes 部署时，请参考以下表格中 `values.yaml` 文件的可用 Helm Chart 值。更多信息请参阅[在 Kubernetes 上部署 Label Studio](install_k8s)。

</div>

<div class="enterprise-only">

在配置 Label Studio 的 Kubernetes 部署时，请参考以下表格中 `values.yaml` 文件的可用 Helm Chart 值。更多信息请参阅[在 Kubernetes 上部署 Label Studio](install_enterprise_k8s)。

</div>

## 全局参数

Helm Chart 的全局参数。

| 参数                                                                        | 描述                                                                                                            | 默认值                      |
|-----------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------|----------------------------|
| `global.imagePullSecrets`                                                   | 全局 Docker 仓库 Secret 名称（数组形式）                                                                                | `[]`                       |
| `global.image.repository`                                                   | 镜像仓库                                                                                                               | `heartexlabs/label-studio` |
| `global.image.pullPolicy`                                                   | 镜像拉取策略                                                                                                           | `IfNotPresent`             |
| `global.image.tag`                                                          | 镜像标签（建议使用不可变标签）                                                                                         | `develop`                  |
| `global.pgConfig.host`                                                      | PostgreSQL 主机名                                                                                                      | `""`                       |
| `global.pgConfig.port`                                                      | PostgreSQL 端口                                                                                                        | `5432`                     |
| `global.pgConfig.dbName`                                                    | PostgreSQL 数据库名称                                                                                                  | `""`                       |
| `global.pgConfig.userName`                                                  | PostgreSQL 数据库用户账户                                                                                              | `""`                       |
| `global.pgConfig.password.secretName`                                       | 包含 PostgreSQL 数据库用户密码的现有 Secret 名称                                                                       | `""`                       |
| `global.pgConfig.password.secretKey`                                        | 包含 PostgreSQL 数据库用户密码的现有 Secret 的键                                                                       | `""`                       |
| `global.pgConfig.ssl.pgSslMode`                                             | [PostgreSQL SSL 模式](https://www.postgresql.org/docs/current/libpq-connect.html#LIBPQ-CONNECT-SSLMODE)                | `""`                       |
| `global.pgConfig.ssl.pgSslSecretName`                                       | 包含 PostgreSQL 主机 SSL 证书的现有 Secret 名称                                                                        | `""`                       |
| `global.pgConfig.ssl.pgSslRootCertSecretKey`                                | 包含 PostgreSQL 主机 SSL 证书的现有 Secret 的键                                                                        | `""`                       |
| `global.pgConfig.ssl.pgSslCertSecretKey`                                    | 包含 PostgreSQL 主机 SSL 证书私钥的现有 Secret 名称                                                                    | `""`                       |
| `global.pgConfig.ssl.pgSslKeySecretKey`                                     | 包含 PostgreSQL 主机 SSL 证书私钥的现有 Secret 的键                                                                    | `""`                       |
| `global.redisConfig.host`                                                   | Redis 连接字符串，格式：redis://[:password]@localhost:6379/1                                                           | `""`                       |
| `global.redisConfig.password.secretName`                                    | 包含 Redis 数据库密码的现有 Secret 名称                                                                                | `""`                       |
| `global.redisConfig.password.secretKey`                                     | 包含 Redis 数据库密码的现有 Secret 的键                                                                                | `""`                       |
| `global.redisConfig.ssl.redisSslCertReqs`                                   | 是否验证服务器公钥或忽略。接受值（`""`、`"optional"`、`"required"`）                                                   | `""`                       |
| `global.redisConfig.ssl.redisSslSecretName`                                 | 包含 Redis 主机 SSL 证书的现有 Secret 名称                                                                             | `""`                       |
| `global.redisConfig.ssl.redisSslCaCertsSecretKey`                           | 包含 Redis 主机 SSL 证书的现有 Secret 的键                                                                             | `""`                       |
| `global.redisConfig.ssl.redisSslCertFileSecretKey`                          | 包含 Redis 主机 SSL 证书私钥的现有 Secret 名称                                                                         | `""`                       |
| `global.redisConfig.ssl.redisSslKeyFileSecretKey`                           | 包含 Redis 主机 SSL 证书私钥的现有 Secret 的键                                                                         | `""`                       |
| `global.extraEnvironmentVars`                                               | 额外环境变量的键/值映射，例如 `PYTHONUNBUFFERED: 1`                                                                     | `{}`                       |
| `global.extraEnvironmentSecrets`                                            | 额外 Secret 的键/值映射                                                                                                | `{}`                       |
| `global.persistence.enabled`                                                | 启用持久化存储。[查看更多关于设置持久化存储的信息](https://labelstud.io/guide/persistent_storage.html)                   | `true`                     |
| `global.persistence.type`                                                   | 持久化存储类型                                                                                                         | `volume`                   |
| `global.persistence.config.s3.accessKey`                                    | 用于访问 AWS S3 的 Access Key                                                                                          | `""`                       |
| `global.persistence.config.s3.secretKey`                                    | 用于访问 AWS S3 的 Secret Key                                                                                          | `""`                       |
| `global.persistence.config.s3.accessKeyExistingSecret`                      | 用于提取访问 AWS S3 的 Access Key 的现有 Secret 名称                                                                    | `""`                       |
| `global.persistence.config.s3.accessKeyExistingSecretKey`                   | 用于提取访问 AWS S3 的 Access Key 的现有 Secret 的键                                                                    | `""`                       |
| `global.persistence.config.s3.secretKeyExistingSecret`                      | 用于提取访问 AWS S3 的 Secret Key 的现有 Secret 名称                                                                    | `""`                       |
| `global.persistence.config.s3.secretKeyExistingSecretKey`                   | 用于提取访问 AWS S3 的 Secret Key 的现有 Secret 的键                                                                    | `""`                       |
| `global.persistence.config.s3.region`                                       | AWS S3 区域                                                                                                            | `""`                       |
| `global.persistence.config.s3.bucket`                                       | AWS S3 Bucket 名称                                                                                                     | `""`                       |
| `global.persistence.config.s3.folder`                                       | AWS S3 文件夹名称                                                                                                      | `""`                       |
| `global.persistence.config.s3.urlExpirationSecs`                            | 预签名 URL 的有效秒数                                                                                                  | `86400`                    |
| `global.persistence.config.s3.endpointUrl`                                  | 连接到 S3 时使用的自定义 S3 URL，包括 scheme                                                                             | `""`                       |
| `global.persistence.config.volume.storageClass`                             | 持久卷的 StorageClass                                                                                                  | `""`                       |
| `global.persistence.config.volume.size`                                     | 持久卷大小                                                                                                             | `10Gi`                     |
| `global.persistence.config.volume.accessModes`                              | PVC 访问模式                                                                                                           | `[ReadWriteOnce]`          |
| `global.persistence.config.volume.annotations`                              | 持久卷的额外注解                                                                                                       | `{}`                       |
| `global.persistence.config.volume.existingClaim`                            | 要使用的现有 PVC 名称                                                                                                  | `""`                       |
| `global.persistence.config.volume.resourcePolicy`                           | PVC 资源策略                                                                                                           | `""`                       |
| `global.persistence.config.volume.annotations`                              | 持久卷的额外注解                                                                                                       | `{}`                       |
| `global.persistence.config.azure.storageAccountName`                        | 用于访问 Azure Blob Storage 的 Azure 存储账户名称                                                                      | `""`                       |
| `global.persistence.config.azure.storageAccountKey`                         | 用于访问 Azure Blob Storage 的 Azure 存储账户密钥                                                                      | `""`                       |
| `global.persistence.config.azure.storageAccountNameExistingSecret`          | 用于提取 Azure 存储账户名称以访问 Azure Blob Storage 的现有 Secret 名称                                                | `""`                       |
| `global.persistence.config.azure.storageAccountNameExistingSecretKey`       | 用于提取 Azure 存储账户名称以访问 Azure Blob Storage 的现有 Secret 的键                                                | `""`                       |
| `global.persistence.config.azure.storageAccountKeyExistingSecret`           | 用于提取 Azure 存储账户密钥以访问 Azure Blob Storage 的现有 Secret 名称                                                | `""`                       |
| `global.persistence.config.azure.storageAccountKeyExistingSecretKey`        | 用于提取 Azure 存储账户密钥以访问 Azure Blob Storage 的现有 Secret 的键                                                | `""`                       |
| `global.persistence.config.azure.containerName`                             | Azure Storage 容器名称                                                                                                 | `""`                       |
| `global.persistence.config.azure.folder`                                    | Azure Storage 文件夹名称                                                                                               | `""`                       |
| `global.persistence.config.azure.urlExpirationSecs`                         | 预签名 URL 的有效秒数                                                                                                  | `86400`                    |
| `global.persistence.config.gcs.projectID`                                   | 要使用的 GCP 项目 ID                                                                                                   | `""`                       |
| `global.persistence.config.gcs.applicationCredentialsJSON`                  | 用于访问 GCS 的服务账户密钥                                                                                            | `""`                       |
| `global.persistence.config.gcs.applicationCredentialsJSONExistingSecret`    | 用于提取服务账户密钥以访问 GCS 的现有 Secret 名称                                                                      | `""`                       |
| `global.persistence.config.gcs.applicationCredentialsJSONExistingSecretKey` | 用于提取服务账户密钥以访问 GCS 的现有 Secret 的键                                                                      | `""`                       |
| `global.persistence.config.gcs.bucket`                                      | GCS Bucket 名称                                                                                                        | `""`                       |
| `global.persistence.config.gcs.folder`                                      | GCS 文件夹名称                                                                                                         | `""`                       |
| `global.persistence.config.gcs.urlExpirationSecs`                           | 预签名 URL 的有效秒数                                                                                                  | `86400`                    |
| `global.featureFlags`                                                       | 功能标志的键/值映射                                                                                                    | `{}`                       |
| `global.envInjectSources`                                                   | 用于加载额外环境变量的 Shell 脚本文件名列表。在使用 Vault Agent Injector 时很有用                                        | `[]`                       |
| `global.cmdWrapper`                                                         | 在启动应用之前运行的额外命令。适用于在启动命令之前运行包装脚本                                                          | `""`                       |
| `global.customCaCerts`                                                      | 要添加到信任链中的 SSL 证书文件名列表                                                                                  | `[]`                       |

## App 参数

特定于 Label Studio 部署中 `app` 部分的参数。

| 参数                                           | 描述                                                                                   | 默认值                     |
|------------------------------------------------|------------------------------------------------------------------------------------------------|--------------------------|
| `app.args`                                     | 覆盖默认容器参数（使用自定义镜像时很有用）                                                  | `["label-studio-uwsgi"]` |
| `app.deploymentStrategy.type`                  | 部署策略类型                                                                                  | `RollingUpdate`          |
| `app.replicas`                                 | App Pod 副本数量                                                                             | `1`                      |
| `app.NameOverride`                             | 用于部分覆盖 Release 模板名称的字符串                                                        | `""`                     |
| `app.FullnameOverride`                         | 用于完全覆盖 Release 模板名称的字符串                                                        | `""`                     |
| `app.resources.requests.memory`                | App 容器请求的内存资源                                                                       | `384Mi`                  |
| `app.resources.requests.cpu`                   | App 容器请求的 CPU 资源                                                                      | `250m`                   |
| `app.resources.limits.memory`                  | App 容器的内存资源限制                                                                       | `""`                     |
| `app.resources.limits.cpu`                     | App 容器的 CPU 资源限制                                                                      | `""`                     |
| `app.initContainer.resources.requests`         | 初始化容器 db-migrations 的资源请求                                                          | `{}`                     |
| `app.initContainer.resources.limits`           | 初始化容器 db-migrations 的资源限制                                                          | `{}`                     |
| `app.readinessProbe.enabled`                   | 启用 readinessProbe                                                                          | `false`                  |
| `app.readinessProbe.path`                      | readinessProbe 的路径                                                                        | `/version`               |
| `app.readinessProbe.failureThreshold`          | 当探针失败时，Kubernetes 将在放弃前重试 failureThreshold 次                                   | `2`                      |
| `app.readinessProbe.initialDelaySeconds`       | 容器启动后到探针开始执行之间的秒数                                                            | `60`                     |
| `app.readinessProbe.periodSeconds`             | 执行探针的频率（秒）                                                                         | `10`                     |
| `app.readinessProbe.successThreshold`          | 探针失败后被认为成功所需的最小连续成功次数                                                    | `1`                      |
| `app.readinessProbe.timeoutSeconds`            | 探针超时的秒数                                                                               | `5`                      |
| `app.livenessProbe.enabled`                    | 启用 livenessProbe                                                                           | `true`                   |
| `app.livenessProbe.path`                       | livenessProbe 的路径                                                                         | `/health`                |
| `app.livenessProbe.failureThreshold`           | 当探针失败时，Kubernetes 将在放弃前重试 failureThreshold 次                                   | `3`                      |
| `app.livenessProbe.initialDelaySeconds`        | 容器启动后到探针开始执行之间的秒数                                                            | `60`                     |
| `app.livenessProbe.periodSeconds`              | 执行探针的频率（秒）                                                                         | `10`                     |
| `app.livenessProbe.successThreshold`           | 探针失败后被认为成功所需的最小连续成功次数                                                    | `1`                      |
| `app.livenessProbe.timeoutSeconds`             | 探针超时的秒数                                                                               | `5`                      |
| `app.extraEnvironmentVars`                     | 要设置的额外环境变量映射                                                                     | `{}`                     |
| `app.extraEnvironmentSecrets`                  | 要设置的额外环境 Secret 映射                                                                 | `{}`                     |
| `app.nodeSelector`                             | Pod 分配的标签，格式为多行字符串或 YAML 映射                                                  | `{}`                     |
| `app.annotations`                              | 附加到 App Pod 的 k8s 注解                                                                   | `{}`                     |
| `app.extraLabels`                              | 附加的额外 k8s 标签                                                                          | `{}`                     |
| `app.affinity`                                 | Pod 分配的亲和性                                                                             | `{}`                     |
| `app.tolerations`                              | Pod 的容忍设置                                                                               | `[]`                     |
| `app.nginx.resources.requests`                 | Nginx 边车容器：资源请求                                                                     | `{}`                     |
| `app.nginx.resources.limits`                   | Nginx 边车容器：资源限制                                                                     | `{}`                     |
| `app.dnsPolicy`                                | Pod DNS 策略                                                                                 | `ClusterFirst`           |
| `app.enableServiceLinks`                       | 服务环境变量                                                                                 | `false`                  |
| `app.shareProcessNamespace`                    | 在 Pod 中启用共享进程命名空间                                                                 | `false`                  |
| `app.automountServiceAccountToken`             | 为服务器服务账户自动挂载服务账户令牌                                                          | `true`                   |
| `app.serviceAccount.create`                    | 为 App Pod 启用 ServiceAccount 的创建                                                         | `true`                   |
| `app.serviceAccount.name`                      | 创建的 ServiceAccount 名称                                                                   |                          |
| `app.serviceAccount.annotations`               | App ServiceAccount 的自定义注解                                                              | `{}`                     |
| `app.podSecurityContext.enabled`               | 启用 Pod 安全上下文                                                                          | `true`                   |
| `app.podSecurityContext.fsGroup`               | Pod 的组 ID                                                                                  | `1001`                   |
| `app.containerSecurityContext.enabled`         | 启用容器安全上下文                                                                           | `true`                   |
| `app.containerSecurityContext.runAsUser`       | 容器的用户 ID                                                                                | `1001`                   |
| `app.containerSecurityContext.runAsNonRoot`    | 避免权限提升到 root 用户                                                                      | `true`                   |
| `app.extraVolumes`                             | 添加额外卷的数组                                                                             | `[]`                     |
| `app.extraVolumeMounts`                        | 添加额外挂载的数组（通常与 extraVolumes 一起使用）                                            | `[]`                     |
| `app.topologySpreadConstraints`                | Pod 分配的拓扑分布约束                                                                       | `[]`                     |
| `app.nginx.args`                               | 覆盖默认容器参数（使用自定义镜像时很有用）                                                    | `["nginx"]`              |
| `app.nginx.livenessProbe.enabled`              | Nginx 边车容器：启用 livenessProbe                                                           | `true`                   |
| `app.nginx.livenessProbe.path`                 | Nginx 边车容器：livenessProbe 的路径                                                         | `/nginx_health`          |
| `app.nginx.livenessProbe.failureThreshold`     | Nginx 边车容器：当探针失败时，Kubernetes 将在放弃前重试 failureThreshold 次                   | `2`                      |
| `app.nginx.livenessProbe.initialDelaySeconds`  | Nginx 边车容器：容器启动后到探针开始执行之间的秒数                                            | `60`                     |
| `app.nginx.livenessProbe.periodSeconds`        | Nginx 边车容器：执行探针的频率（秒）                                                         | `5`                      |
| `app.nginx.livenessProbe.successThreshold`     | Nginx 边车容器：探针失败后被认为成功所需的最小连续成功次数                                    | `1`                      |
| `app.nginx.livenessProbe.timeoutSeconds`       | Nginx 边车容器：探针超时的秒数                                                               | `3`                      |
| `app.nginx.readinessProbe.enabled`             | Nginx 边车容器：启用 readinessProbe                                                          | `true`                   |
| `app.nginx.readinessProbe.path`                | Nginx 边车容器：readinessProbe 的路径                                                        | `/version`               |
| `app.nginx.readinessProbe.failureThreshold`    | Nginx 边车容器：当探针失败时，Kubernetes 将在放弃前重试 failureThreshold 次                   | `2`                      |
| `app.nginx.readinessProbe.initialDelaySeconds` | Nginx 边车容器：容器启动后到探针开始执行之间的秒数                                            | `60`                     |
| `app.nginx.readinessProbe.periodSeconds`       | Nginx 边车容器：执行探针的频率（秒）                                                         | `10`                     |
| `app.nginx.readinessProbe.successThreshold`    | Nginx 边车容器：探针失败后被认为成功所需的最小连续成功次数                                    | `1`                      |
| `app.nginx.readinessProbe.timeoutSeconds`      | Nginx 边车容器：探针超时的秒数                                                               | `5`                      |
| `app.service.type`                             | k8s Service 类型                                                                             | `ClusterIP`              |
| `app.service.port`                             | k8s Service 端口                                                                             | `80`                     |
| `app.service.targetPort`                       | k8s Service 目标端口                                                                         | `8085`                   |
| `app.service.portName`                         | k8s Service 端口名称                                                                         | `service`                |
| `app.service.annotations`                      | App Service 的自定义注解                                                                     | `{}`                     |
| `app.service.sessionAffinity`                  | App Service 的会话亲和性                                                                     | `None`                   |
| `app.service.sessionAffinityConfig`            | sessionAffinity 的附加设置                                                                   | `{}`                     |
| `app.ingress.enabled`                          | 设置为 true 以启用 Ingress 记录生成                                                           | `false`                  |
| `app.ingress.className`                        | 用于实现 Ingress 的 IngressClass（Kubernetes 1.18+）                                          | `""`                     |
| `app.ingress.host`                             | Ingress 资源的默认主机                                                                       | `""`                     |
| `app.ingress.path`                             | Label Studio 的路径。您可能需要将其设置为 '/*' 以便与 ALB Ingress 控制器配合使用               | `/`                      |
| `app.ingress.extraPaths`                       | 要添加到主机配置前面的额外路径                                                               | `[]`                     |
| `app.ingress.tls`                              | TLS Secret 定义                                                                              | `[]`                     |
| `app.ingress.annotations`                      | 额外的 Ingress 注解                                                                          | `{}`                     |
| `app.rbac.create`                              | 指定是否应为 App Service 创建 RBAC 资源                                                       | `false`                  |
| `app.rbac.rules`                               | 为 App Service 设置的自定义 RBAC 规则                                                         | `[]`                     |
| `app.contextPath`                              | 为健康/就绪检查附加的上下文路径                                                               | `/`                      |
| `app.cmdWrapper`                               | 在启动应用之前运行的额外命令。适用于在启动命令之前运行包装脚本                                 | `""`                     |

## Rqworker 参数

特定于 Label Studio Enterprise 部署中 `rqworkers` 服务的参数。

| 参数                                             | 描述                                                                     | 默认值                                   |
|--------------------------------------------------|---------------------------------------------------------------------------------|----------------------------------------|
| `rqworker.enabled`                               | 启用 rqworker Pod                                                                | `true`                                 |
| `rqworker.NameOverride`                          | 用于部分覆盖 Release 模板名称的字符串                                             | `""`                                   |
| `rqworker.FullnameOverride`                      | 用于完全覆盖 Release 模板名称的字符串                                             | `""`                                   |
| `rqworker.deploymentStrategy.type`               | 部署策略类型                                                                     | `Recreate`                             |
| `rqworker.extraEnvironmentVars`                  | 要设置的额外环境变量映射                                                         | `{}`                                   |
| `rqworker.extraEnvironmentSecrets`               | 要设置的额外环境 Secret 映射                                                     | `{}`                                   |
| `rqworker.nodeSelector`                          | Pod 分配的标签，格式为多行字符串或 YAML 映射                                      | `{}`                                   |
| `rqworker.annotations`                           | 附加到 rqworker Pod 的 k8s 注解                                                  | `{}`                                   |
| `rqworker.extraLabels`                           | 附加的额外 k8s 标签                                                              | `{}`                                   |
| `rqworker.affinity`                              | Pod 分配的亲和性                                                                 | `{}`                                   |
| `rqworker.tolerations`                           | Pod 的容忍设置                                                                   | `[]`                                   |
| `rqworker.queues.high.replicas`                  | Rqworker 队列 "high" 的副本数                                                     | `1`                                    |
| `rqworker.queues.high.args`                      | Rqworker 队列 "high" 的启动参数                                                   | `"high"`                               |
| `rqworker.queues.low.replicas`                   | Rqworker 队列 "low" 的副本数                                                      | `1`                                    |
| `rqworker.queues.low.args`                       | Rqworker 队列 "low" 的启动参数                                                    | `"low"`                                |
| `rqworker.queues.default.replicas`               | Rqworker 队列 "default" 的副本数                                                   | `1`                                    |
| `rqworker.queues.default.args`                   | Rqworker 队列 "default" 的启动参数                                                 | `"default"`                            |
| `rqworker.queues.critical.replicas`              | Rqworker 队列 "critical" 的副本数                                                  | `1`                                    |
| `rqworker.queues.critical.args`                  | Rqworker 队列 "critical" 的启动参数                                                | `"critical"`                           |
| `rqworker.queues.all.replicas`                   | Rqworker 队列 "all" 的副本数                                                       | `1`                                    |
| `rqworker.queues.all.args`                       | Rqworker 队列 "all" 的启动参数                                                     | `"low", "default", "critical", "high"` |
| `rqworker.dnsPolicy`                             | Pod DNS 策略                                                                     | `ClusterFirst`                         |
| `rqworker.enableServiceLinks`                    | 服务环境变量                                                                     | `false`                                |
| `rqworker.shareProcessNamespace`                 | 在 Pod 中启用共享进程命名空间                                                     | `false`                                |
| `rqworker.automountServiceAccountToken`          | 为服务器服务账户自动挂载服务账户令牌                                              | `true`                                 |
| `rqworker.readinessProbe.enabled`                | 启用 readinessProbe                                                              | `false`                                |
| `rqworker.readinessProbe.path`                   | readinessProbe 的路径                                                            | `/version`                             |
| `rqworker.readinessProbe.failureThreshold`       | 当探针失败时，Kubernetes 将在放弃前重试 failureThreshold 次                       | `2`                                    |
| `rqworker.readinessProbe.initialDelaySeconds`    | 容器启动后到探针开始执行之间的秒数                                                | `60`                                   |
| `rqworker.readinessProbe.periodSeconds`          | 执行探针的频率（秒）                                                             | `5`                                    |
| `rqworker.readinessProbe.successThreshold`       | 探针失败后被认为成功所需的最小连续成功次数                                        | `1`                                    |
| `rqworker.readinessProbe.timeoutSeconds`         | 探针超时的秒数                                                                   | `3`                                    |
| `rqworker.livenessProbe.enabled`                 | 启用 livenessProbe                                                               | `false`                                |
| `rqworker.livenessProbe.path`                    | livenessProbe 的路径                                                             | `/health`                              |
| `rqworker.livenessProbe.failureThreshold`        | 当探针失败时，Kubernetes 将在放弃前重试 failureThreshold 次                       | `2`                                    |
| `rqworker.livenessProbe.initialDelaySeconds`     | 容器启动后到探针开始执行之间的秒数                                                | `60`                                   |
| `rqworker.livenessProbe.periodSeconds`           | 执行探针的频率（秒）                                                             | `5`                                    |
| `rqworker.livenessProbe.successThreshold`        | 探针失败后被认为成功所需的最小连续成功次数                                        | `1`                                    |
| `rqworker.livenessProbe.timeoutSeconds`          | 探针超时的秒数                                                                   | `3`                                    |
| `rqworker.serviceAccount.create`                 | 为 rqworker Pod 启用 ServiceAccount 的创建                                        | `true`                                 |
| `rqworker.serviceAccount.name`                   | 创建的 ServiceAccount 名称                                                       | `""`                                   |
| `rqworker.podSecurityContext.enabled`            | 启用 Pod 安全上下文                                                              | `true`                                 |
| `rqworker.podSecurityContext.fsGroup`            | Pod 的组 ID                                                                      | `1001`                                 |
| `rqworker.containerSecurityContext.enabled`      | 启用容器安全上下文                                                               | `true`                                 |
| `rqworker.containerSecurityContext.runAsUser`    | 容器的用户 ID                                                                    | `1001`                                 |
| `rqworker.containerSecurityContext.runAsNonRoot` | 避免权限提升到 root 用户                                                          | `true`                                 |
| `rqworker.serviceAccount.annotations`            | App ServiceAccount 的自定义注解                                                   | `{}`                                   |
| `rqworker.extraVolumes`                          | 添加额外卷的数组                                                                 | `[]`                                   |
| `rqworker.extraVolumeMounts`                     | 添加额外挂载的数组（通常与 extraVolumes 一起使用）                                | `[]`                                   |
| `rqworker.topologySpreadConstraints`             | Pod 分配的拓扑分布约束                                                           | `[]`                                   |
| `rqworker.rbac.create`                           | 指定是否应为 rqworker Service 创建 RBAC 资源                                      | `false`                                |
| `rqworker.rbac.rules`                            | 为 rqworker Service 设置的自定义 RBAC 规则                                        | `[]`                                   |
| `rqworker.cmdWrapper`                            | 在启动应用之前运行的额外命令。适用于在启动命令之前运行包装脚本                     | `""`                                   |

<div class="enterprise-only">

## Label Studio Enterprise 参数

| 参数                                      | 描述                                                          | 默认值      |
|-------------------------------------------|----------------------------------------------------------------------|-----------|
| `enterprise.enabled`                      | 启用 Enterprise 功能                                                 | `false`   |
| `enterprise.enterpriseLicense.secretName` | 包含 Label Studio Enterprise 许可证信息的现有 Secret 名称             | `""`      |
| `enterprise.enterpriseLicense.secretKey`  | 包含 Enterprise 许可证信息的现有 Secret 的键                          | `license` |

</div>

## 子 Chart 参数

| 参数                       | 描述                                                                                    | 默认值          |
|----------------------------|-----------------------------------------------------------------------------------------------|---------------|
| `postgresql.enabled`       | 启用 PostgreSQL 子 Chart                                                                      | `true`        |
| `postgresql.architecture`  | PostgreSQL 架构（standalone 或 replication）                                                  | `standalone`  |
| `postgresql.image.tag`     | PostgreSQL 镜像标签                                                                          | `13.8.0`      |
| `postgresql.auth.username` | 要创建的自定义用户名称                                                                        | `labelstudio` |
| `postgresql.auth.password` | 要创建的自定义用户密码。如果提供了带有 password 键的 `auth.existingSecret`，则忽略此设置       | `labelstudio` |
| `postgresql.auth.database` | 要创建的自定义数据库名称                                                                      | `labelstudio` |
| `redis.enabled`            | 启用 Redis 子 Chart                                                                           | `false`       |
| `redis.architecture`       | Redis 架构。允许值：`standalone` 或 `replication`                                             | `standalone`  |
| `redis.auth.enabled`       | 启用密码认证                                                                                  | `false`       |

## 其他参数
| 参数                      | 描述                                    | 默认值            |
|---------------------------|------------------------------------------------|-----------------|
| `upgradeCheck.enabled`    | 启用升级检查                                    | `false`         |
| `ci`                      | 指示部署用于 CI 目的                            | `false`         |
| `clusterDomain`           | Kubernetes 集群域名                             | `cluster.local` |
| `checkConfig.skipEnvValues` | 跳过环境变量验证                              | `false`         |

## `global.extraEnvironmentVars` 的使用

`global.extraEnvironmentVars` 部分可用于配置 Label Studio 的环境属性。

此部分下设置的任何键值对都会转换为用于控制 Label Studio 配置的环境变量。每个键在设置环境变量之前都会转换为大写。

示例：

```yaml
global:
  extraEnvironmentVars:
     PG_USER: labelstudio
```

!!! note
    如果您要部署到生产环境，应该设置 `SSRF_PROTECTION_ENABLED: true`。请参阅[保护 Label Studio](security#Enable-SSRF-protection-for-production-environments)。
