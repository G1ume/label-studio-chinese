---
title: 保护 Label Studio 安全
type: guide
tier: all
order: 99
order_enterprise: 99
meta_title: 保护 Label Studio 安全
meta_description: 关于不同 Label Studio 版本所使用的安全与加固流程，以及如何配置更安全的数据标注项目。
section: "安装与设置"
---

Label Studio 提供了多种方式来保护对您的数据和部署架构的访问安全。

所有应用程序组件之间的交互均使用 TLS 协议进行加密。

<div class="enterprise-only">

基于角色的访问控制仅在 Label Studio Enterprise 中可用。

</div>

<!--如果您需要满足严格的隐私法规、法律要求，或者希望在您的基础设施或任何公有云（AWS、Google、Azure 等）中进行自定义安装，Label Studio Enterprise 支持本地部署。这是一个自包含版本（无需互联网连接），所有数据都不会离开您的基础设施。为了使安装尽可能易于访问，我们提供了 Docker 镜像。-->

Label Studio 通过强制使用 HTTPS 和安全 Cookie 来建立与 Web 应用程序的安全连接。

<div class="opensource-only">

如果您在生产环境中运行开源版本，请限制对 Label Studio 服务器的访问。
通过仅在服务器上开放[所需端口](/guide/install.html#Port_requirements)来限制对服务器本身的访问。

</div>

## 保护用户对 Label Studio 的访问

保护用户对 Label Studio 的访问，以保护数据完整性，并确保只有拥有系统访问权限的人员才能执行更改。

每个用户必须创建一个密码长度在 8 到 128 个字符之间的账户，以便您追踪谁访问了 Label Studio 以及他们执行了哪些操作。

<div class="opensource-only">

您可以将注册限制为仅限拥有注册页面链接的人员，并且注册页面的邀请链接可以重置。有关更多信息，请参阅[为 Label Studio 设置用户账户](/guide/signup.html)。

</div>

<div class="enterprise-only">

您可以将注册限制为仅限拥有注册页面链接的人员，并且注册页面的邀请链接可以重置。有关更多信息，请参阅[为 Label Studio 设置用户账户](/guide/manage_users.html#Signup)。

</div>

<div class="enterprise-only">

如果您使用的是 Label Studio Enterprise，您可以通过多种方式进一步保护用户访问：

- 为特定用户账户分配特定角色，以设置基于角色的访问控制。有关 Label Studio Enterprise 中不同角色和权限的更多信息，请参阅[管理 Label Studio 的访问权限](/guide/manage_users.html)。
- 设置组织、工作空间和项目，以在不同的用户组之间分隔项目和数据。一个组织中的用户无法查看其他组织中的工作空间或项目。有关如何使用组织、工作空间和项目来保护访问的更多信息，请参阅[在 Label Studio 中组织项目](/guide/manage_users.html#Roles-and-workspaces)。

</div>

## 保护 API 对 Label Studio 的访问

对 REST API 的访问受用户角色限制，并且需要特定于用户账户的访问令牌。访问令牌可以随时从 Label Studio UI 或使用 API 重置。

## 为生产环境启用 SSRF 保护

在将 Label Studio 部署到生产环境时，请将 `SSRF_PROTECTION_ENABLED` 环境变量设置为 `true`。

此变量默认处于禁用状态，以支持在本地环境中处理数据的用户。但是，在生产环境中使用时应当启用它。

## 保护对 Label Studio 中数据的访问

Label Studio 中的数据存储在一个或两个位置，具体取决于您的部署配置。

- 项目设置和配置详情存储在 Label Studio 的内部数据库中。
- 输入数据（文本、图像、音频文件）托管在外部数据存储中，并通过 URI 链接提供给 Label Studio。数据不会直接存储在 Label Studio 中，内容仅在客户端检索。
- 项目标注存储在内部数据库中，也可以选择存储在本地文件目录、Redis 数据库或 Amazon Web Services（AWS）、Google Cloud Platform（GCP）或 Microsoft Azure 上的云存储桶中。

!!! info 提示
    针对 AWS 和 GCP 存储，还有一些高级安全选项，包括：
    * [GCP 的应用默认凭据](storage_gcp#Application-Default-Credentials-for-enhanced-security-for-GCS)（仅限本地部署）
    * [GCP 存储的 IP 过滤](storage_gcp#IP-filtering-for-enhanced-security-for-GCS)
    * [S3 的 IP 过滤和 VPN](storage_s3#IP-filtering-and-VPN-for-enhanced-security-for-S3-storage)

### 保护数据库访问

Label Studio 不允许从应用程序直接访问内部数据库，以防止 SQL 注入攻击和其他数据窃取尝试。

相反，应用程序使用 URI 来访问存储在数据库中的数据。这些 URI 只能由 Label Studio 标注界面和 API 访问，因为使用这些 URI 检索数据的请求会通过 Basic Authentication 请求头进行验证和代理。

通过 REST API 暴露的所有特定对象属性都已添加到允许列表中。API 端点只能使用特定的 HTTP 动词访问，并且必须由实现了适当的跨域资源共享（CORS）策略的基于浏览器的客户端访问。API 令牌是用户特定的，可以随时重置。

PostgreSQL 数据库启用了 SSL 模式，并且需要有效的证书。

### 保护对云存储的访问

Label Studio 中的每个项目都可以链接到各种云存储选项，如 AWS S3、Google Cloud Storage 等。用户主要通过 Label Studio 生成的预签名 URL 访问云存储中的文件。您可以为每个项目配置多个云存储连接，并使用不同的凭据来管理数据访问。了解如何设置[云存储设置](storage)。

结合使用工作空间、项目、用户和角色。这种方法有助于有效地配置和保护云存储访问。

#### 源存储逻辑与安全

Label Studio 的云存储集成执行两个关键操作：
* **任务同步和导入**
* **媒体文件服务**

下面从安全角度对两者进行说明。

##### 任务同步和导入

将存储连接到项目后，您有多个选项可以将任务加载到项目中。根据所选的选项，您需要提供特定的权限：

* **同步媒体文件**（需要 **LIST** 权限）：当启用 **Tasks** 导入方法时，存储同步会根据您存储中的文件列表自动创建 Label Studio 任务。Label Studio 不会读取文件内容；它只是引用文件（例如 `{"image": "s3://bucket/1.jpg"}`）。

* **同步 JSON 任务文件**（需要 **LIST** 和 **GET** 权限）：当启用 **Tasks** 导入方法时，存储同步从您的存储桶中读取 JSON 文件中的 Label Studio 任务，并将整个 JSON 内容加载到 Label Studio 数据库中。

* **不同步**（不需要任何权限）：您可以手动导入包含 Label Studio 任务的 JSON 文件，并在任务中引用存储 URI（例如 `{"image": "s3://bucket/1.jpg"}`）。

##### 媒体文件服务

创建 Label Studio 任务后，用户可以在浏览器中查看和编辑任务。要访问存储在您存储桶中的媒体文件，将执行以下步骤：

1. **预签名 URL 生成**：Label Studio 后端为存储桶中的文件生成预签名 URL。此步骤需要 **GET** 权限用于预签名 URL 生成，但 Label Studio 不会下载您的数据。

2. **用户浏览器下载**：用户在查看或标注任务时，其浏览器会下载并显示媒体文件。这需要用户的浏览器能够直接访问预签名 URL。

#### VPC 背后的源存储

为了确保 VPC 背后数据的最大安全性和隔离性，仅允许 Label Studio 后端和内部网络中的用户访问。为此，您可以使用以下技术——尤其适用于 Label Studio SaaS（云端，`app.humansignal.com`）：

1. 为您的存储设置 **IP 限制**，以**允许 Label Studio 执行任务同步并为媒体文件服务生成预签名 URL**。IP 限制通过确保只有受信任的网络才能访问您的存储来增强安全性。需要 GET（S3 的 `s3:GetObject`）和 LIST（S3 的 `s3:ListBucket`）权限。<span class="enterprise-only">`app.humansignal.com` 的 IP 范围可在[此处](saas#IP-range)的文档中找到。</span>

2. **建立存储与用户浏览器之间的安全连接**：
    - 配置 VPC 私有端点并将 VPN 流量路由到该端点，以便用户浏览器可以仅通过您的虚拟专用网络（VPN）安全地访问 S3 存储桶。
    - 或者将您的存储访问限制为特定的 IP 或 VPC。

**配置示例：**
  - [AWS S3 存储：IP 过滤和 VPN 以增强安全性](storage#IP-Filtering-and-VPN-for-Enhanced-Security-for-S3-storage)。
  - [Google Cloud Storage：IP 过滤以增强安全性](storage#IP-Filtering-for-Enhanced-Security-for-GCS-storage)。

<i>此图展示了如何使用 VPC 和 IP 限制安全地配置 Label Studio 的源云存储</i>

<img width="49%" style="display: inline-block; margin-right: 5px;" src="/images/storages/cloud-storage-ip-restriction.jpg" alt="Label Studio + Cloud Storage IP Restriction" class="make-intense-zoom" />

<img width="49%" style="display: inline-block;" src="/images/storages/cloud-storage-vpn.jpg" alt="Label Studio + Cloud Storage VPC" class="make-intense-zoom" />

### 保护对 Redis 存储的访问

如果您使用 Redis 作为数据和标注的外部存储数据库，则该设置支持 TLS/SSL，并要求 Label Studio 客户端使用有效证书向数据库进行身份验证。

<div class="enterprise-only">

## 审计日志

Label Studio Enterprise 会自动记录所有用户活动，以便您监控应用程序中正在执行的操作。

</div>

## Label Studio 收集的信息

Label Studio 收集使用统计数据，包括页面访问次数、标注数量以及您在标注配置中使用的数据类型。我们收集的这些信息有助于改善在 Label Studio 中标注数据的体验，并帮助我们规划未来要支持的数据类型和标注配置。

<div class="opensource-only">

您可以通过将环境变量 `COLLECT_ANALYTICS` 设置为 `False` 来禁用数据收集。

</div>

## 将自签名证书添加到受信任的根存储区

<div class="code-tabs">
  <div data-name="Docker Compose">

1. 将您的自签名证书作为卷挂载到 `app` 容器中：

```yaml
volumes:
  - ./my.cert:/tmp/my.cert:ro
```
2. 添加名为 `CUSTOM_CA_CERTS` 的环境变量，以逗号分隔的方式列出应添加到信任存储区的所有证书：

```yaml
CUSTOM_CA_CERTS=/tmp/my.cert
```
  </div>

  <div data-name="Kubernetes">

1. 将您的自签名证书上传为 k8s Secret。
   上传 `my.cert` 作为名为 `test-my-root-cert` 的 Secret：

```yaml
kubectl create secret generic test-my-root-cert --from-file=file=my.cert
```

2. 在您的 values.yaml 文件中添加卷，并在 `.global.customCaCerts` 中引用它们：

```yaml
global:
  customCaCerts:
   - /opt/heartex/secrets/ca_certs/file/file

app:
  extraVolumes:
    - name: foo
      secret:
        secretName: test-my-root-cert
  extraVolumeMounts:
    - name: foo
      mountPath: "/opt/heartex/secrets/ca_certs/file"
      readOnly: true

rqworker:
  extraVolumes:
    - name: foo
      secret:
        secretName: test-my-root-cert
  extraVolumeMounts:
    - name: foo
      mountPath: "/opt/heartex/secrets/ca_certs/file"
      readOnly: true
```
  </div>
</div>


### 将自签名证书添加到 S3 存储的受信任根存储区

连接云存储 S3 使用的是 Boto 库。需要将 `AWS_CA_BUNDLE` 设置为环境变量。
<div class="code-tabs">
  <div data-name="Docker Compose">

1. 将您的自签名证书作为卷挂载到 `app` 容器中：（必须为 .pem 文件类型）

```yaml
volumes:
  - ./ca.pem:/tmp/ca.pem:ro
```
2. 添加名为 `AWS_CA_BUNDLE` 的环境变量，以便 Boto 库信任该证书。

```yaml
AWS_CA_BUNDLE=/tmp/ca.pem
```
