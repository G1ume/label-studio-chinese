---
title: 设置 Google Cloud Storage
short: Google Cloud
type: guide
tier: all
order: 153
order_enterprise: 153
meta_title: 设置 Google Cloud Storage
meta_description: "如何为 Google Cloud 设置源存储和目标存储。"
section: "导入与导出"
parent: "storage"
parent_enterprise: "storage"
---

动态导入任务并将标注导出到 Label Studio 中的 Google Cloud Storage (GCS) 存储桶。有关 Label Studio 如何保护对云存储的访问的详细信息，请参阅[云存储安全访问](security#Secure-access-to-cloud-storage)。

## 配置对 Google Cloud Storage 存储桶的访问

首先，请查看[项目云存储](storage)和[云存储安全访问](security#Secure-access-to-cloud-storage)中的信息。

然后，您需要完成以下准备工作：

#### 1. 启用对存储桶的编程访问

请参阅 Google Cloud Storage 文档中的[Cloud Storage 客户端库](https://cloud.google.com/storage/docs/reference/libraries)，了解如何设置对 GCS 存储桶的访问。

#### 2. 设置存储桶的身份验证

您的账号必须具有 **Service Account Token Creator** 和 **Storage Object Viewer** 角色以及 **storage.buckets.get** 访问权限。请参阅 Google Cloud Storage 文档中的[设置身份验证](https://cloud.google.com/storage/docs/reference/libraries#setting_up_authentication)和[Cloud Storage 的 IAM 权限](https://cloud.google.com/storage/docs/access-control/iam-permissions)。

<div class="enterprise-only">

!!! note
    如果您使用 WIF，请参阅下方的[服务账号权限](#Service-account-permissions)。

</div>

#### 3. 配置 CORS

为您的存储桶设置跨域资源共享 (CORS) 访问，使用允许从与您的 Label Studio 部署相同的主机名进行 GET 访问的策略。请参阅 Google Cloud 用户指南中的[配置跨域资源共享 (CORS)](https://cloud.google.com/storage/docs/configuring-cors#configure-cors-bucket)。

!!! note
    仅在使用预签名 URL 时需要此配置。如果您使用代理方式，则无需配置 CORS。有关更多信息，请参阅[预签名 URL 与存储代理](storage#Pre-signed-URLs-vs-Storage-proxies)。

使用或修改以下示例：

```shell
echo '[
   {
      "origin": ["*"],
      "method": ["GET"],
      "responseHeader": ["Content-Type","Access-Control-Allow-Origin"],
      "maxAgeSeconds": 3600
   }
]' > cors-config.json
```

在以下命令中将 `YOUR_BUCKET_NAME` 替换为您实际的存储桶名称，以更新存储桶的 CORS 配置：

```shell
gsutil cors set cors-config.json gs://YOUR_BUCKET_NAME
```

## Google Cloud Storage

开始之前：

* 查看[项目云存储](storage)和[云存储安全访问](security.html#Secure-access-to-cloud-storage)中的信息。
* [配置对存储桶的访问](#Configure-access-to-your-GCS-bucket)。

### Google 应用程序凭据

您需要提供 Google 应用程序凭据。这是一个在设置存储时输入的 JSON 文件。

1. 在 Google Cloud Console 中，转到 **IAM & Admin > Service Accounts**。
2. 选择您需要凭据的特定服务账号。如果没有，请创建一个新的。
3. 在服务账号详情中，转到 **Keys** 选项卡，点击 **Add Key > Create new key**。
4. 选择 JSON 密钥类型，点击 **Create**。JSON 文件将被生成并自动下载到您的计算机。

另请参阅：

* [为附加了服务账号的资源设置 ADC](https://docs.cloud.google.com/docs/authentication/set-up-adc-attached-service-account)
* [设置应用程序默认凭据](https://docs.cloud.google.com/docs/authentication/provide-credentials-adc)。

!!! note
    如果您使用服务账号授权访问 Google Cloud Platform，请确保激活它。请参阅 [gcloud auth activate-service-account](https://cloud.google.com/sdk/gcloud/reference/auth/activate-service-account)。

### 创建源存储连接

在 Label Studio 中，打开您的项目，选择 **Settings > Cloud Storage > Add Source Storage**。

选择 **Google Cloud Storage**，点击 **Next**。

#### 配置连接

填写以下字段，然后点击 **Test connection**：

<div class="noheader rowheader">

<table style="width: 100%; border-collapse: collapse;">
  <tr>
    <th style="width: 25%;">字段</th>
    <th>描述</th>
  </tr>

  <tr>
    <td>Storage Title</td>
    <td>输入一个名称以标识存储连接。</td>
  </tr>

  <tr>
    <td>Bucket Name</td>
    <td>
      输入您的 GCS 存储桶名称。
    </td>
  </tr>

  <tr>
    <td>Google Application Credentials</td>
    <td>
      输入您创建的用于管理存储桶身份验证的 GCS 凭据 JSON 文件。<br /><br /><strong>本地部署用户：</strong>或者，您可以使用 <code>GOOGLE_APPLICATION_CREDENTIALS</code> 环境变量和/或设置应用程序默认凭据，这样用户就不需要手动配置凭据。<br /><br />请参阅下方的<a href="#Application-Default-Credentials-for-enhanced-security-for-GCS">使用应用程序默认凭据增强安全性</a>。
    </td>
  </tr>

  <tr>
    <td>Google Project ID</td>
    <td>
      输入存储桶所在的 Google 项目 ID（例如 <code>my-label-studio-project</code>）。<br /><br />如果不确定，您可以在 Google Cloud Console 中的 <strong>IAM & Admin > Settings</strong> 下找到它。
    </td>
  </tr>

  <tr>
    <td>Use pre-signed URLs (On) /<br/> Proxy through the platform (Off)</td>
    <td>
      这决定了如何从您的存储桶加载数据：
      <ul>
        <li><strong>使用预签名 URL</strong>：Label Studio 生成指向您的 S3/GCS/Azure 对象的限时 HTTPS 链接，并将浏览器重定向到那里（HTTP 303），因此标注者的浏览器直接从云存储下载媒体文件。这通常更快且扩展性更好，但需要在存储桶上正确配置 CORS 和预签名权限。这也意味着流量从浏览器流向存储，而不是经过 Label Studio。</li>
        <li><strong>通过平台代理</strong> – 后端从云存储下载文件并将其流式传输到浏览器，因此所有媒体流量都经过 Label Studio 服务器。这使数据完全保留在 Label Studio/网络边界内，对每个请求强制执行任务级访问检查，并避免了 CORS/预签名设置，但会使用更多的 Label Studio 工作线程资源，速度可能稍慢。</li>
      </ul>
      <br/>
      有关更多信息，请参阅
      <a href="storage#Pre-signed-URLs-vs-Storage-proxies">预签名 URL 与存储代理</a>。
    </td>
  </tr>

  <tr>
    <td>Expire pre-signed URLs (minutes)</td>
    <td>控制预签名 URL 保持有效的时长。</td>
  </tr>

</table>
</div>

#### 导入设置与预览

填写以下字段，然后点击 **Load preview** 以确保您正在同步正确的数据：

<div class="noheader rowheader">

| | |
| --- | --- |
| Bucket Prefix | 可选，输入您要使用的存储桶内目录名称。例如 `data-set-1` 或 `data-set-1/subfolder-2`。 |
| Import Method | 选择是为存储桶中的每个文件创建一个任务，还是使用 JSON/JSONL/Parquet 文件定义每个任务的数据。 |
| File Name Filter | 指定一个正则表达式来过滤存储桶对象。使用 `.*` 收集所有对象。 |
| Scan all sub-folders | 启用此选项可对容器内的子文件夹执行递归扫描。 |

</div>

#### 审核与确认

如果一切看起来正确，点击 **Save & Sync** 立即同步，或点击 **Save** 保存设置稍后同步。


!!! info Tip
    您也可以使用 API [同步导入存储](https://api.labelstud.io/api-reference/api-reference/import-storage/gcs/sync)。

### 创建目标存储连接

在 Label Studio 中，打开您的项目，选择 **Settings > Cloud Storage > Add Target Storage**。

选择 **Google Cloud Storage**，点击 **Next**。

填写以下字段：

<div class="noheader rowheader">

<table style="width: 100%; border-collapse: collapse;">

  <tr>
    <td style="width: 25%;">Storage Title</td>
    <td>输入一个名称以标识存储连接。</td>
  </tr>

  <tr>
    <td>Bucket Name</td>
    <td>
      输入您的 GCS 存储桶名称。
    </td>
  </tr>

  <tr>
    <td>Bucket Prefix</td>
    <td>
      可选，输入您要使用的存储桶内目录名称。例如 <code>data-set-1</code> 或 <code>data-set-1/subfolder-2</code>。
    </td>
  </tr>

  <tr>
    <td>Google Application Credentials</td>
    <td>
      输入您创建的用于管理存储桶身份验证的 GCS 凭据 JSON 文件。<br /><br /><strong>本地部署用户：</strong>或者，您可以使用 <code>GOOGLE_APPLICATION_CREDENTIALS</code> 环境变量和/或设置应用程序默认凭据，这样用户就不需要手动配置凭据。<br /><br />请参阅下方的<a href="#Application-Default-Credentials-for-enhanced-security-for-GCS">使用应用程序默认凭据增强安全性</a>。
    </td>
  </tr>

  <tr>
    <td>Google Project ID</td>
    <td>
      输入存储桶所在的 Google 项目 ID（例如 <code>my-label-studio-project</code>）。<br /><br />如果不确定，您可以在 Google Cloud Console 中的 <strong>IAM & Admin > Settings</strong> 下找到它。
    </td>
  </tr>

  <tr>
    <td>Can delete objects from storage</td>
    <td>如果您希望在 Label Studio 中删除标注时同时删除存储在存储桶中的标注，请启用此选项。您的凭据必须包含删除存储桶对象的能力。</td>
  </tr>

</table>
</div>

添加存储后，点击 **Sync**。

!!! info Tip
    您也可以使用 API [同步导出存储](https://api.labelstud.io/api-reference/api-reference/export-storage/gcs/sync)。

### 使用应用程序默认凭据增强 GCS 安全性

如果您在本地部署 Label Studio 并使用 Google Cloud Storage，可以设置[应用程序默认凭据](https://cloud.google.com/docs/authentication/provide-credentials-adc)来为所有项目全局提供云存储身份验证，这样用户就不需要手动配置凭据。

推荐的方法是使用 `GOOGLE_APPLICATION_CREDENTIALS` 环境变量。例如：

```bash
  export GOOGLE_APPLICATION_CREDENTIALS=json-file-with-GCP-creds-23441-8f8sd99vsd115a.json
  ```

## 使用 Workload Identity Federation (WIF) 的 Google Cloud Storage

<div class="opensource-only">

在 Label Studio Enterprise 中，您可以将 Workload Identity Federation (WIF) 池与 Google Cloud Storage 配合使用。

与应用程序凭据不同，WIF 允许您使用临时凭据。每次向 GCS 发出请求时，Label Studio 都会连接到您的身份池以请求临时凭据。

有关更多信息，请参阅我们 Enterprise 文档中的 [Google Cloud Storage with Workload Identity Federation (WIF)](https://docs.humansignal.com/guide/storage_gcp.html#Google-Cloud-Storage-with-Workload-Identity-Federation-WIF)。

</div>

<div class="enterprise-only">

您也可以将 Workload Identity Federation (WIF) 池与 Google Cloud Storage 配合使用。

与应用程序凭据不同，WIF 允许您使用临时凭据。每次向 GCS 发出请求时，Label Studio 都会连接到您的身份池以请求临时凭据。

有关 WIF 的更多信息，请参阅 [Google Cloud - Workload Identity Federation](https://cloud.google.com/iam/docs/workload-identity-federation)。

开始之前：

* 查看[项目云存储](storage)和[云存储安全访问](security.html#Secure-access-to-cloud-storage)中的信息。
* [配置对存储桶的访问](#Configure-access-to-your-GCS-bucket)。

### 服务账号权限

您需要一个具有以下权限的服务账号：

- 存储桶：**Storage Admin** (`roles/storage.admin`)
- 项目：**Service Account Token Creator** (`roles/iam.serviceAccountTokenCreator`)
- 项目：**Storage Object Viewer** (`roles/storage.viewer`)

请参阅 Google Cloud 文档中的[创建服务账号](https://cloud.google.com/iam/docs/service-accounts-create?hl=en)。

### 创建 Workload Identity Pool

您可以使用多种方法创建 WIF 池。

<details>
<summary>使用 Terraform</summary>
<br>

下面提供了一个示例脚本。确保设置所有必需的变量：

* GCP 项目变量：

  * `var.gcp_project_name`

  * `var.gcp_region`

* HumanSignal 提供的 SaaS：

  * `var.aws_account_id` = `490065312183`

  * `var.aws_role_name` = `label-studio-app-production`

然后运行：

```bash
terraform init
terraform plan
terraform apply
```

应用后，您将拥有一个信任 Label Studio AWS IAM Role 的可正常工作的 Workload Identity Pool。

```json
## Variables
/* AWS 变量用于使 AWS 托管的 Label Studio 资源能够请求凭据 */

variable "gcp_project_name" {
  type        = string
  description = "GCP Project name"
}

variable "gcp_region" {
  type        = string
  description = "GCP Region"
}

variable "label_studio_gcp_sa_name" {
  type        = string
  description = "GCP Label Studio Service Account Name"
}

variable "aws_account_id" {
  type        = string
  description = "AWS Project ID"
}

variable "aws_role_name" {
  type        = string
  description = "AWS Role name"
}

variable "external_ids" {
  type        = list(string)
  default = []
  description = "List of external ids"
}

## Outputs

output "GCP_WORKLOAD_ID" {
  value = google_iam_workload_identity_pool_provider.label-studio-provider-jwt.workload_identity_pool_id
}

output "GCP_WORKLOAD_PROVIDER" {
  value = google_iam_workload_identity_pool_provider.label-studio-provider-jwt.workload_identity_pool_provider_id
}

## Main

provider "google" {
  project = var.gcp_project_name
  region  = var.gcp_region
}

resource "random_id" "random" {
  byte_length = 4
}

locals {
  aws_assumed_role = "arn:aws:sts::${var.aws_account_id}:assumed-role/${var.aws_role_name}"

  external_id_condition = (
    length(var.external_ids) > 0
    ? format("(attribute.aws_role == \"%s\") && (attribute.external_id in [%s])",
      local.aws_assumed_role,
      join(", ", formatlist("\"%s\"", var.external_ids))
    )
    : format("(attribute.aws_role == \"%s\")", local.aws_assumed_role)
  )
}

resource "google_iam_workload_identity_pool" "label-studio-pool" {
  workload_identity_pool_id = "label-studio-pool-${random_id.random.hex}"
  project                   = var.gcp_project_name
}

resource "google_iam_workload_identity_pool_provider" "label-studio-provider-jwt" {
  workload_identity_pool_id          = google_iam_workload_identity_pool.label-studio-pool.workload_identity_pool_id
  workload_identity_pool_provider_id = "label-studio-jwt-${random_id.random.hex}"

  attribute_condition = local.external_id_condition

  attribute_mapping = {
    "google.subject"        = "assertion.arn"
    "attribute.aws_account" = "assertion.account"
    "attribute.aws_role"    = "assertion.arn.contains('assumed-role') ? assertion.arn.extract('{account_arn}assumed-role/') + 'assumed-role/' + assertion.arn.extract('assumed-role/{role_name}/') : assertion.arn"
    "attribute.external_id" = "assertion.external_id"
  }

  aws {
    account_id = var.aws_account_id
  }
}

data "google_service_account" "existing_sa" {
  account_id = var.label_studio_gcp_sa_name
}

resource "google_service_account_iam_binding" "label-studio-sa-oidc" {
  service_account_id = data.google_service_account.existing_sa.name
  role               = "roles/iam.workloadIdentityUser"

  members = [
    "principalSet://iam.googleapis.com/${google_iam_workload_identity_pool.label-studio-pool.name}/attribute.aws_role/${local.aws_assumed_role}"
  ]
}
```

</details>

<details>
<summary>使用 gcloud 命令行</summary>
<br>

将方括号中的变量（`[PROJECT_ID]`、`[POOL_ID]`、`[PROVIDER_ID]` 等）替换为您自己的值。

确保在必要时对引号进行转义或使用单引号。

1. 创建 Workload Identity Pool：

   ```shell
   gcloud iam workload-identity-pools create [POOL_ID] \
  --project=[PROJECT_ID] \
  --location="global" \
  --display-name="[POOL_DISPLAY_NAME]"
   ```
   其中：

   * `[POOL_ID]` 是您要分配给 WIF 池的 ID（例如 `label-studio-pool-abc123`）。请记下它，因为稍后需要重用。
   * `[PROJECT_ID]` 是您的 Google Cloud 项目 ID。
   * `[POOL_DISPLAY_NAME]` 是池的人类可读名称（可选，但建议使用）。

1. 为 AWS 创建提供程序。

    这允许具有正确外部 ID 和 AWS 角色配置的 AWS 主体模拟 Google Cloud 服务账号。这是必要的，因为发出请求的 Label Studio 资源托管在 AWS 中。

    ```shell
    gcloud iam workload-identity-pools providers create-aws [PROVIDER_ID] \
    --workload-identity-pool="[POOL_ID]" \
    --account-id="490065312183" \
    --attribute-condition="attribute.aws_role==\"arn:aws:sts::490065312183:assumed-role/label-studio-app-production\"" \
    --attribute-mapping="google.subject=assertion.arn,attribute.aws_account=assertion.account,attribute.aws_role=assertion.arn,attribute.external_id=assertion.external_id"

    ```
    其中：

    * `[PROVIDER_ID]` 是提供程序 ID（例如 `label-studio-app-production`）。
    * `[POOL_ID]`：您在步骤 1 中提供的池 ID。

2. 授予您之前创建的[服务账号](#Service-account-permissions) `iam.workloadIdentityUser` 角色。

    ```shell
    gcloud iam service-accounts add-iam-policy-binding [SERVICE_ACCOUNT_EMAIL] \
    --role="roles/iam.workloadIdentityUser" \
    --member="principalSet://iam.googleapis.com/projects/[PROJECT_NUMBER]/locations/global/workloadIdentityPools/[POOL_ID]/attribute.aws_role/arn:aws:sts::490065312183:assumed-role/label-studio-app-production"
    ```

    其中：

    * `[SERVICE_ACCOUNT_EMAIL]` 是与您的 GCS 服务账号关联的电子邮件（例如 `my-service-account@[PROJECT_ID].iam.gserviceaccount.com`）。
    * `[PROJECT_NUMBER]`：您的 Google 项目编号。这与项目 ID 不同。您可以使用以下命令查找项目编号：

        `gcloud projects describe $PROJECT_ID --format="value(projectNumber)"`
    * `[POOL_ID]`：您在步骤 1 中提供的池 ID。

在 Label Studio 中设置连接之前，请记下您为以下变量提供的值（您将被要求提供它们）：

* `[POOL_ID]`
* `[PROVIDER_ID]`
* `[SERVICE_ACCOUNT_EMAIL]`
* `[PROJECT_NUMBER]`
* `[PROJECT_ID]`

</details>

<details>
<summary>使用 Google Cloud Console</summary>
<br>

开始之前，确保您处于正确的项目中：

![GCS 控制台截图，项目已高亮](/images/storages/gcs-project.png)

1. 在 Google Cloud Console 中，导航到 [**IAM & Admin > Workload Identity Pools**](https://console.cloud.google.com/iam-admin/workload-identity-pools)。

2. 点击 **Get Started** 启用 API。

3. 在 **Create an identity pool** 下，填写以下字段：

    * **Name**：这是池 ID（例如 `label-studio-pool-abc123`）。请记下此 ID，因为稍后需要再次使用。
    * **Description**：这是池的显示名称（例如 "Label Studio Pool"）。

4. 在 **Add a provider pool** 下，填写以下字段：

    * **Select a provider**：选择 AWS。这是负责发出请求的 Label Studio 组件所在的位置。
    * **Provider name**：输入 `Label Studio App Production`（您可以使用其他显示名称，但需要确保相应的提供程序 ID 仍为 `label-studio-app-production`）
    * **Provider ID**：输入 `label-studio-app-production`。
    * **AWS Account ID**：输入 `490065312183`。

5. 在 **Configure provider attributes** 下，输入以下内容：

    * 点击 **Add condition**，然后输入以下内容：

        `attribute.aws_role=="arn:aws:sts::490065312183:assumed-role/label-studio-app-production"`

    * 点击 **Edit mapping**，然后添加以下内容：

        - `google.subject = assertion.arn`
        - `attribute.aws_role = assertion.arn.contains('assumed-role') ? assertion.arn.extract('{account_arn}assumed-role/') + 'assumed-role/' + assertion.arn.extract('assumed-role/{role_name}/') : assertion.arn`（此项可能会默认填写）
        - `attribute.aws_account = assertion.account`
        - `attribute.external_id = assertion.external_id`

6. 点击 **Save**。

7. 转到 **IAM & Admin > Service Accounts**，找到您希望允许 AWS（Label Studio）模拟的服务账号。请参阅上方的[服务账号权限](#Service-account-permissions)。

8. 在 **Principals with access** 选项卡中，点击 **Grant Access**。

    ![授予访问权限按钮截图](/images/storages/gcs-grant-access.png)

9. 在 **New principals** 字段中，添加以下内容：

    `principalSet://iam.googleapis.com/projects/[PROJECT_NUMBER]/locations/global/workloadIdentityPools/[POOL_ID]/attribute.aws_role/arn:aws:sts::490065312183:assumed-role/label-studio-app-production`

    其中：

    * `[PROJECT_NUMBER]` - 将其替换为您的 Google 项目编号。这与项目 ID 不同。要查找项目编号，请转到 **IAM & Admin > Settings**。
    * `[POOL_ID]` - 将其替换为池 ID（即您在上述步骤 3 中输入的 **Name**，例如 `label-studio-pool-abc123`）。

9. 在 **Assign Roles** 下，使用 **Role** 下拉菜单中的搜索字段查找 **Workload Identity User** 角色。

    ![主体窗口截图](/images/storages/gcs-principal.png)

10. 点击 **Save**

在 Label Studio 中设置连接之前，请记下以下信息（您将被要求提供它们）：

* 您的池 ID - 可从 **IAM & Admin > Workload Identity Pools** 获取
* 您的提供程序 ID - 可从 **IAM & Admin > Workload Identity Pools** 获取（应为 `label-studio-app-production`）
* 您的服务账号电子邮件 - 可从 **IAM & Admin > Service Accounts** 获取。选择服务账号后，电子邮件列在 **Details** 下。
* 您的 Google 项目编号 - 可从 **IAM & Admin > Settings** 获取
* 您的 Google 项目 ID - 可从 **IAM & Admin > Settings** 获取

</details>

### 创建源存储连接

在 Label Studio 中，打开您的项目，选择 **Settings > Cloud Storage > Add Source Storage**。

选择 **Google Cloud Storage (WIF Auth)**，点击 **Next**。

#### 配置连接

填写以下字段，然后点击 **Test connection**：


<div class="noheader rowheader">

<table style="width: 100%; border-collapse: collapse;">
  <tr>
    <th style="width: 25%;">字段</th>
    <th>描述</th>
  </tr>

  <tr>
    <td>Storage Title</td>
    <td>输入一个名称以标识存储连接。</td>
  </tr>

  <tr>
    <td>Bucket Name</td>
    <td>
      输入您的 GCS 存储桶名称。
    </td>
  </tr>

  <tr>
    <td>Workload Identity Pool ID</td>
    <td>
      这是您在创建 Workload Identity Pool 时指定的 ID。您可以在 Google Cloud Console 中的 <strong>IAM & Admin > Workload Identity Pools</strong> 下找到它。
    </td>
  </tr>

  <tr>
    <td>Workload Identity Provider ID</td>
    <td>
      这是您在设置提供程序时指定的 ID。您可以在 Google Cloud Console 中的 <strong>IAM & Admin > Workload Identity Pools</strong> 下找到它。
    </td>
  </tr>

  <tr>
    <td>Service Account Email</td>
    <td>
      这是您在准备工作中设置的服务账号关联的电子邮件。您可以在服务账号的 <strong>Details</strong> 页面中找到它（位于 <strong>IAM & Admin > Service Accounts</strong> 下）。例如 <code>labelstudio@random-string-382222.iam.gserviceaccount.com</code>。
    </td>
  </tr>

  <tr>
    <td>Google Project ID</td>
    <td>
      您的 Google 项目 ID。您可以在 Google Cloud Console 中的 <strong>IAM & Admin > Settings</strong> 下找到它。
    </td>
  </tr>

  <tr>
    <td>Google Project Number</td>
    <td>
      您的 Google 项目编号。您可以在 Google Cloud Console 中的 <strong>IAM & Admin > Settings</strong> 下找到它。
    </td>
  </tr>

  <tr>
    <td>Use pre-signed URLs (On) /<br/> Proxy through the platform (Off)</td>
    <td>
      这决定了如何从您的存储桶加载数据：
      <ul>
        <li><strong>使用预签名 URL</strong>：Label Studio 生成指向您的 S3/GCS/Azure 对象的限时 HTTPS 链接，并将浏览器重定向到那里（HTTP 303），因此标注者的浏览器直接从云存储下载媒体文件。这通常更快且扩展性更好，但需要在存储桶上正确配置 CORS 和预签名权限。这也意味着流量从浏览器流向存储，而不是经过 Label Studio。</li>
        <li><strong>通过平台代理</strong> – 后端从云存储下载文件并将其流式传输到浏览器，因此所有媒体流量都经过 Label Studio 服务器。这使数据完全保留在 Label Studio/网络边界内，对每个请求强制执行任务级访问检查，并避免了 CORS/预签名设置，但会使用更多的 Label Studio 工作线程资源，速度可能稍慢。</li>
      </ul>
      <br/>
      有关更多信息，请参阅
      <a href="storage#Pre-signed-URLs-vs-Storage-proxies">预签名 URL 与存储代理</a>。
    </td>
  </tr>

  <tr>
    <td>Expire pre-signed URLs (minutes)</td>
    <td>控制预签名 URL 保持有效的时长。</td>
  </tr>

</table>
</div>

#### 导入设置与预览

填写以下字段，然后点击 **Load preview** 以确保您正在同步正确的数据：

<div class="noheader rowheader">

| | |
| --- | --- |
| Bucket Prefix | 可选，输入您要使用的存储桶内目录名称。例如 `data-set-1` 或 `data-set-1/subfolder-2`。 |
| Import Method | 选择是为存储桶中的每个文件创建一个任务，还是使用 JSON/JSONL/Parquet 文件定义每个任务的数据。 |
| File Name Filter | 指定一个正则表达式来过滤存储桶对象。使用 `.*` 收集所有对象。 |
| Scan all sub-folders | 启用此选项可对容器内的子文件夹执行递归扫描。 |

</div>

#### 审核与确认

如果一切看起来正确，点击 **Save & Sync** 立即同步，或点击 **Save** 保存设置稍后同步。


!!! info Tip
    您也可以使用 API [同步导入存储](https://api.labelstud.io/api-reference/api-reference/import-storage/gcswif/sync)。

### 创建目标存储连接

在 Label Studio 中，打开您的项目，选择 **Settings > Cloud Storage > Add Target Storage**。

选择 **Google Cloud Storage (WIF Auth)**，点击 **Next**。

填写以下字段：

<div class="noheader rowheader">

<table style="width: 100%; border-collapse: collapse;">

  <tr>
    <td style="width: 25%;">Storage Title</td>
    <td>输入一个名称以标识存储连接。</td>
  </tr>

  <tr>
    <td>Bucket Name</td>
    <td>
      输入您的 GCS 存储桶名称。
    </td>
  </tr>

  <tr>
    <td>Bucket Prefix</td>
    <td>
      可选，输入您要使用的存储桶内目录名称。例如 <code>data-set-1</code> 或 <code>data-set-1/subfolder-2</code>。
    </td>
  </tr>

<tr>
    <td>Workload Identity Pool ID</td>
    <td>
      这是您在创建 Workload Identity Pool 时指定的 ID。您可以在 Google Cloud Console 中的 <strong>IAM & Admin > Workload Identity Pools</strong> 下找到它。
    </td>
  </tr>

  <tr>
    <td>Workload Identity Provider ID</td>
    <td>
      这是您在设置提供程序时指定的 ID。您可以在 Google Cloud Console 中的 <strong>IAM & Admin > Workload Identity Pools</strong> 下找到它。
    </td>
  </tr>

  <tr>
    <td>Service Account Email</td>
    <td>
      这是您在准备工作中设置的服务账号关联的电子邮件。您可以在服务账号的 <strong>Details</strong> 页面中找到它（位于 <strong>IAM & Admin > Service Accounts</strong> 下）。例如 <code>labelstudio@random-string-382222.iam.gserviceaccount.com</code>。
    </td>
  </tr>

  <tr>
    <td>Google Project ID</td>
    <td>
      您的 Google 项目 ID。您可以在 Google Cloud Console 中的 <strong>IAM & Admin > Settings</strong> 下找到它。
    </td>
  </tr>

  <tr>
    <td>Google Project Number</td>
    <td>
      您的 Google 项目编号。您可以在 Google Cloud Console 中的 <strong>IAM & Admin > Settings</strong> 下找到它。
    </td>
  </tr>

  <tr>
    <td>Can delete objects from storage</td>
    <td>如果您希望在 Label Studio 中删除标注时同时删除存储在存储桶中的标注，请启用此选项。您的凭据必须包含删除存储桶对象的能力。</td>
  </tr>

</table>
</div>

添加存储后，点击 **Sync**。

!!! info Tip
    您也可以使用 API [同步导出存储](https://api.labelstud.io/api-reference/api-reference/export-storage/gcswif/sync)。

</div>


## 使用 GKE 服务账号模拟的 Google Cloud Storage

<div class="opensource-only">

在 Label Studio Enterprise 中，如果您在 GKE 中部署 Label Studio，可以设置服务账号模拟。

Google Cloud Storage 服务账号模拟允许 Google Cloud 服务账号临时获取另一个服务账号的权限，而无需访问其密钥。

有关更多信息，请参阅我们 Enterprise 文档中的 [Google Cloud Storage with service account impersonation for GKE](https://docs.humansignal.com/guide/storage_gcp.html#Google-Cloud-Storage-with-service-account-impersonation-for-GKE)。

</div>

<div class="enterprise-only">

Google Cloud Storage 服务账号模拟允许 Google Cloud 服务账号临时获取另一个服务账号的权限，而无需访问其密钥。

在 Google Kubernetes Engine (GKE) 上部署 Label Studio 时，工作负载通常使用通过 Workload Identity 映射到 Google Cloud 服务账号的 Kubernetes 服务账号进行身份验证。但是，这个直接链接的 GCP 服务账号可能没有对您的 GCS 存储桶的广泛访问权限。

通过服务账号模拟，工作负载的 GCP 服务账号可以模拟一个单独的、权限更高的、具有必要存储权限的服务账号。这种方法无需创建、分发和轮换长期有效的服务账号密钥，从而降低了凭据泄露的风险。

有关更多信息，请参阅：

* [服务账号模拟](https://cloud.google.com/iam/docs/service-account-impersonation)
* [使用服务账号模拟](https://cloud.google.com/docs/authentication/use-service-account-impersonation)

开始之前：

* 查看[项目云存储](storage)和[云存储安全访问](security.html#Secure-access-to-cloud-storage)中的信息。
* [配置对存储桶的访问](#Configure-access-to-your-GCS-bucket)。
* 您需要从 Label Studio 平台管理员处获取**基础服务账号电子邮件**。这是通过 GKE Workload Identity 绑定到运行 Label Studio 的 Kubernetes 服务账号的 GCP 服务账号。如果尚未设置，请参阅下方的[配置 GKE 项目（平台管理员）](#Configure-the-GKE-project-platform-administrator)。

您将在两个 Google Cloud 项目中工作：

* [您的基础 GKE 项目。](#Configure-the-GKE-project-platform-administrator) 这是您使用 GKE 部署 Label Studio 的项目。
* [您的目标 Google Cloud 项目。](#Configure-the-target-Google-Cloud-project) 这是您的 GCS 存储桶所在的项目。


### 配置 GKE 项目（平台管理员）

这是由管理 GKE 中 Label Studio 部署的平台管理员执行的一次性设置。如果已完成此设置，您可以跳到[配置目标 Google Cloud 项目](#Configure-the-target-Google-Cloud-project)，并向管理员请求基础服务账号电子邮件。


#### 启用功能标志

您必须在 Label Studio 部署中启用 `fflag_feat_bros_763_gcs_sa_impersonation` 功能标志才能使用服务账号模拟。您可以在 helm chart 中的 [extraEnvironmentVars](helm_values#The-global-extraEnvironmentVars-usage) 下添加此项：

```yaml
global:
  extraEnvironmentVars:
      fflag_feat_bros_763_gcs_sa_impersonation: "true"
```

!!! note
    环境变量名称必须为**小写**。Label Studio 功能标志解析器以区分大小写的方式匹配 `fflag_` 前缀。

#### 创建基础服务账号

在 GKE 项目中创建一个 GCP 服务账号，作为 Label Studio pod 的身份。这是"基础"服务账号，稍后将被授予模拟其他项目中目标服务账号的权限。

1. 在 Google Cloud Console 中，确保您处于 **GKE 项目**（部署 Label Studio 的项目）中。
2. 转到 [**IAM & Admin > Service Accounts**](https://console.cloud.google.com/iam-admin/serviceAccounts)。
3. 点击 **Create Service Account**。
4. 输入名称和描述（例如 `lse-base`）。
5. 点击 **Done**。此阶段无需授予其他角色。
6. 记下服务账号电子邮件（例如 `lse-base@your-gke-project.iam.gserviceaccount.com`）。这是您将与需要配置存储桶访问权限的数据团队共享的**基础服务账号电子邮件**。

#### 将基础服务账号绑定到 Kubernetes 服务账号

使用 [GKE Workload Identity](https://cloud.google.com/kubernetes-engine/docs/how-to/workload-identity) 将 GCP 基础服务账号链接到 Label Studio pod 使用的 Kubernetes 服务账号。这允许 pod 自动以基础服务账号的身份进行身份验证，而无需管理密钥。

1. 将 Workload Identity 注解添加到 Label Studio 使用的 Kubernetes 服务账号：

    ```shell
    kubectl annotate serviceaccount label-studio-sa \
      --namespace=label-studio \
      iam.gke.io/gcp-service-account=BASE_SA_EMAIL
    ```

    将 `label-studio-sa` 和 `label-studio` 替换为您实际的 Kubernetes 服务账号名称和命名空间，将 `BASE_SA_EMAIL` 替换为上一步中的电子邮件。

2. 授予 Workload Identity User 角色，以便 Kubernetes 服务账号可以作为 GCP 服务账号运行：

    ```shell
    gcloud iam service-accounts add-iam-policy-binding BASE_SA_EMAIL \
      --role="roles/iam.workloadIdentityUser" \
      --member="serviceAccount:GKE_PROJECT_ID.svc.id.goog[NAMESPACE/KSA_NAME]"
    ```

    其中：

    * `BASE_SA_EMAIL` 是基础服务账号电子邮件（例如 `lse-base@your-gke-project.iam.gserviceaccount.com`）。
    * `GKE_PROJECT_ID` 是您的 GKE 项目 ID。
    * `NAMESPACE` 是部署 Label Studio 的 Kubernetes 命名空间（例如 `label-studio`）。
    * `KSA_NAME` 是 Label Studio 使用的 Kubernetes 服务账号名称（例如 `label-studio-sa`）。

3. 通过运行测试 pod 验证绑定是否正常工作：

    ```shell
    kubectl run workload-identity-test \
      --image=google/cloud-sdk:slim \
      --serviceaccount=label-studio-sa \
      --namespace=label-studio \
      -it --rm -- gcloud auth list
    ```

    输出应显示基础服务账号电子邮件为活跃账号。

有关更多信息，请参阅 Google Cloud 文档中的[使用 Workload Identity](https://cloud.google.com/kubernetes-engine/docs/how-to/workload-identity)。

<details>
<summary>使用 Terraform</summary>
<br>

```hcl
resource "google_service_account" "lse_base" {
  project      = "your-gke-project"
  account_id   = "lse-base"
  display_name = "Label Studio Base SA"
}

resource "google_service_account_iam_member" "workload_identity_binding" {
  service_account_id = google_service_account.lse_base.name
  role               = "roles/iam.workloadIdentityUser"
  member             = "serviceAccount:your-gke-project.svc.id.goog[label-studio/label-studio-sa]"
}
```

将 `your-gke-project`、`label-studio` 和 `label-studio-sa` 替换为您实际的项目 ID、命名空间和 Kubernetes 服务账号名称。

</details>

### 配置目标 Google Cloud 项目

#### 步骤 1：在目标 Google Cloud 项目中创建服务账号

在您的存储桶所在的 Google Cloud 项目中，创建一个 Label Studio 将模拟以访问数据的服务账号。

1. 在 Google Cloud Console 中，转到 [**IAM & Admin > Service Accounts**](https://console.cloud.google.com/iam-admin/serviceAccounts)。
2. 点击 **Create Service Account**。
3. 输入名称和描述（例如 `sa-label-studio-data`）。
4. 点击 **Create and Continue**。
5. 记下服务账号电子邮件（例如 `sa-label-studio-data@your-data-project.iam.gserviceaccount.com`）。您稍后会需要它。

请参阅 Google Cloud 文档中的[创建服务账号](https://cloud.google.com/iam/docs/service-accounts-create)。

#### 步骤 2：授予目标服务账号对存储桶的访问权限

授予目标服务账号对您要连接到 Label Studio 的 Google Cloud Storage 存储桶的适当角色。您需要的角色取决于您计划如何使用存储：

* 对于**源存储**（导入数据）：`roles/storage.objectViewer`
* 对于**目标存储**（导出标注）：`roles/storage.objectAdmin`

目标存储需要 `roles/storage.objectAdmin`，因为 Label Studio 需要在存储桶中创建、覆盖和可选地删除对象。如果您只需要写入权限而无需删除，`roles/storage.objectCreator` 就足够了，但**Can delete objects from storage** 选项将不起作用。

1. 转到 [**Cloud Storage > Buckets**](https://console.cloud.google.com/storage/browser) 并选择您的存储桶。
2. 点击 **Permissions**，然后点击 **Grant Access**。
3. 在 **New principals** 字段中，输入您在[步骤 1](#Step-1-Create-a-service-account-in-your-target-Google-Cloud-project)中收到的目标服务账号电子邮件。
4. 在 **Role** 下拉菜单中，为源存储选择 **Storage Object Viewer** (`roles/storage.objectViewer`)，或为目标存储选择 **Storage Object Admin** (`roles/storage.objectAdmin`)。如需两者都需要，请使用 **Add another role** 添加。
5. 点击 **Save**。

<details>
<summary>使用 gcloud</summary>
<br>

对于源存储（读取访问）：

```shell
gcloud storage buckets add-iam-policy-binding gs://YOUR_BUCKET_NAME \
  --member="serviceAccount:TARGET_SA_EMAIL" \
  --role="roles/storage.objectViewer"
```

对于目标存储（写入和删除访问）：

```shell
gcloud storage buckets add-iam-policy-binding gs://YOUR_BUCKET_NAME \
  --member="serviceAccount:TARGET_SA_EMAIL" \
  --role="roles/storage.objectAdmin"
```

将 `YOUR_BUCKET_NAME` 和 `TARGET_SA_EMAIL` 替换为您的值。

</details>

#### 步骤 3：允许从 Label Studio 基础服务账号进行模拟

此步骤授权 Label Studio 基础服务账号（在 GKE 中运行）模拟您的目标服务账号。

在目标服务账号上向基础服务账号授予 `roles/iam.serviceAccountTokenCreator`。此角色还包含 `signBlob` 权限，Label Studio 使用该权限生成预签名 URL，以实现对存储对象的直接浏览器访问。

1. 转到 [**IAM & Admin > Service Accounts**](https://console.cloud.google.com/iam-admin/serviceAccounts)，选择您在[步骤 1](#Step-1-Create-a-service-account-in-your-target-Google-Cloud-project)中创建的目标服务账号。
2. 点击 **Permissions** 选项卡，然后点击 **Grant Access**。
3. 在 **New principals** 字段中，输入[您的 Label Studio 平台管理员在基础账号中提供的基础服务账号电子邮件](#Create-a-base-service-account)。
4. 在 **Role** 下拉菜单中，选择 **Service Account Token Creator** (`roles/iam.serviceAccountTokenCreator`)。
5. 点击 **Save**。

<details>
<summary>使用 gcloud</summary>
<br>

```shell
gcloud iam service-accounts add-iam-policy-binding TARGET_SA_EMAIL \
  --member="serviceAccount:BASE_SA_EMAIL" \
  --role="roles/iam.serviceAccountTokenCreator"
```

其中：

* `TARGET_SA_EMAIL` 是您在[步骤 1](#Step-1-Create-a-service-account-in-your-target-Google-Cloud-project)中收到的目标服务账号电子邮件。
* `BASE_SA_EMAIL` 是[您的 Label Studio 平台管理员在基础账号中提供的基础服务账号电子邮件](#Create-a-base-service-account)。

</details>

#### 步骤 4：在存储桶上配置 CORS（使用预签名 URL 时需要）

如果您计划使用**预签名 URL**（默认），则必须在 GCS 存储桶上配置跨域资源共享 (CORS)，以便标注者的浏览器可以直接从 Google Cloud Storage 加载媒体文件。

1. 创建一个名为 `cors.json` 的文件，内容如下：

    ```json
    [
      {
        "origin": ["https://your-label-studio-domain.com"],
        "method": ["GET", "HEAD"],
        "responseHeader": ["Content-Type", "Content-Range", "Content-Disposition"],
        "maxAgeSeconds": 3600
      }
    ]
    ```

    将 `https://your-label-studio-domain.com` 替换为您的 Label Studio 实例可访问的 URL。

2. 将 CORS 配置应用到存储桶：

    ```shell
    gcloud storage buckets update gs://YOUR_BUCKET_NAME --cors-file=cors.json
    ```

3. 验证 CORS 配置：

    ```shell
    gcloud storage buckets describe gs://YOUR_BUCKET_NAME --format="default(cors_config)"
    ```

如果您使用**通过平台代理**模式而不是预签名 URL，则不需要配置 CORS，因为所有流量都经过 Label Studio 服务器。

### 创建源存储连接

在 Label Studio 中，打开您的项目，选择 **Settings > Cloud Storage > Add Source Storage**。

选择 **Google Cloud Storage (SA Impersonation)**，点击 **Next**。

#### 配置连接

填写以下字段，然后点击 **Test connection**：

<div class="noheader rowheader">

<table style="width: 100%; border-collapse: collapse;">
  <tr>
    <th style="width: 25%;">字段</th>
    <th>描述</th>
  </tr>

  <tr>
    <td>Storage Title</td>
    <td>输入一个名称以标识存储连接。</td>
  </tr>

  <tr>
    <td>Bucket Name</td>
    <td>
      输入您的 Google Cloud Storage 存储桶名称。
    </td>
  </tr>

  <tr>
    <td>Target Service Account Email</td>
    <td>
      输入您在<a href="#Step-1-Create-a-service-account-in-your-target-Google-Cloud-project">步骤 1</a>中创建的目标服务账号电子邮件（例如 <code>sa-label-studio-data@your-data-project.iam.gserviceaccount.com</code>）。
    </td>
  </tr>

  <tr>
    <td>Google Project ID</td>
    <td>
      输入存储桶所在的 Google 项目 ID（例如 <code>your-data-project</code>）。您可以在 Google Cloud Console 中的 <strong>IAM & Admin > Settings</strong> 下找到它。
    </td>
  </tr>

  <tr>
    <td>Use pre-signed URLs (On) /<br/> Proxy through the platform (Off)</td>
    <td>
      这决定了如何从您的存储桶加载数据：
      <ul>
        <li><strong>使用预签名 URL</strong>：Label Studio 生成指向您的 GCS 对象的限时 HTTPS 链接，并将浏览器重定向到那里（HTTP 303），因此标注者的浏览器直接从云存储下载媒体文件。这通常更快且扩展性更好，但需要在存储桶上进行<a href="#Step-4-Configure-CORS-on-the-bucket-required-for-pre-signed-URLs">CORS 配置</a>。URL 签名所需的 <code>signBlob</code> 权限已包含在<a href="#Step-3-Allow-impersonation-from-the-Label-Studio-base-service-account">步骤 3</a>中授予的 <code>roles/iam.serviceAccountTokenCreator</code> 角色中。</li>
        <li><strong>通过平台代理</strong> – 后端从云存储下载文件并将其流式传输到浏览器，因此所有媒体流量都经过 Label Studio 服务器。这使数据完全保留在 Label Studio/网络边界内，对每个请求强制执行任务级访问检查，并避免了 CORS/预签名设置，但会使用更多的 Label Studio 工作线程资源，速度可能稍慢。</li>
      </ul>
      <br/>
      有关更多信息，请参阅
      <a href="storage#Pre-signed-URLs-vs-Storage-proxies">预签名 URL 与存储代理</a>。
    </td>
  </tr>

  <tr>
    <td>Expire pre-signed URLs (minutes)</td>
    <td>控制预签名 URL 保持有效的时长。</td>
  </tr>

</table>
</div>

#### 导入设置与预览

填写以下字段，然后点击 **Load preview** 以确保您正在同步正确的数据：

<div class="noheader rowheader">

| | |
| --- | --- |
| Bucket Prefix | 可选，输入您要使用的存储桶内目录名称。例如 `data-set-1` 或 `data-set-1/subfolder-2`。 |
| Import Method | 选择是为存储桶中的每个文件创建一个任务，还是使用 JSON/JSONL/Parquet 文件定义每个任务的数据。 |
| File Name Filter | 指定一个正则表达式来过滤存储桶对象。使用 `.*` 收集所有对象。 |
| Scan all sub-folders | 启用此选项可对容器内的子文件夹执行递归扫描。 |

</div>

#### 审核与确认

如果一切看起来正确，点击 **Save & Sync** 立即同步，或点击 **Save** 保存设置稍后同步。

### 创建目标存储连接

在 Label Studio 中，打开您的项目，选择 **Settings > Cloud Storage > Add Target Storage**。

选择 **Google Cloud Storage (SA Impersonation)**，点击 **Next**。

填写以下字段：

<div class="noheader rowheader">

<table style="width: 100%; border-collapse: collapse;">

  <tr>
    <td style="width: 25%;">Storage Title</td>
    <td>输入一个名称以标识存储连接。</td>
  </tr>

  <tr>
    <td>Bucket Name</td>
    <td>
      输入您的 GCS 存储桶名称。
    </td>
  </tr>

  <tr>
    <td>Bucket Prefix</td>
    <td>
      可选，输入您要使用的存储桶内目录名称。例如 <code>data-set-1</code> 或 <code>data-set-1/subfolder-2</code>。
    </td>
  </tr>

  <tr>
    <td>Target Service Account Email</td>
    <td>
      输入您在步骤 1 中创建的目标服务账号电子邮件（例如 <code>sa-label-studio-data@your-data-project.iam.gserviceaccount.com</code>）。
    </td>
  </tr>

  <tr>
    <td>Google Project ID</td>
    <td>
      输入存储桶所在的 Google 项目 ID（例如 <code>your-data-project</code>）。您可以在 Google Cloud Console 中的 <strong>IAM & Admin > Settings</strong> 下找到它。
    </td>
  </tr>

  <tr>
    <td>Can delete objects from storage</td>
    <td>如果您希望在 Label Studio 中删除标注时同时删除存储在存储桶中的标注，请启用此选项。这需要目标服务账号对存储桶具有 <code>roles/storage.objectAdmin</code>（请参阅<a href="#Step-2-Grant-the-target-service-account-access-to-your-bucket">步骤 2</a>）。</td>
  </tr>

</table>
</div>

添加存储后，点击 **Sync**。

### 故障排除

| 错误 | 原因 | 解决方案 |
|-------|-------|----------|
| Label Studio 中没有 **Google Cloud Storage (SA Impersonation)** 选项 | 您尚未启用所需的功能标志。 | 在 Label Studio GKE 部署中设置功能标志（请参阅[启用功能标志](#Enable-the-feature-flag)）。请注意，环境变量必须为**小写**：`fflag_feat_bros_763_gcs_sa_impersonation`。 |
| `Permission 'iam.serviceAccounts.getAccessToken' denied on resource (or it may not exist)` | 基础服务账号没有模拟目标服务账号的权限。 | 验证基础服务账号在目标服务账号上是否具有 `roles/iam.serviceAccountTokenCreator`（请参阅[步骤 3](#Step-3-Allow-impersonation-from-the-Label-Studio-base-service-account)）。 |
| `404 Service account not found` | 目标服务账号电子邮件不正确或账号不存在。 | 验证电子邮件是否以 `.iam.gserviceaccount.com` 结尾，以及该账号是否存在于您的 GCP 项目中。 |
| `Permission 'storage.buckets.get' denied on resource` 或 `403 Access denied to bucket` | 目标服务账号没有存储桶上的必要权限。 | 在存储桶上向目标服务账号授予 `roles/storage.objectViewer`（用于源存储）或 `roles/storage.objectAdmin`（用于目标存储）（请参阅[步骤 2](#Step-2-Grant-the-target-service-account-access-to-your-bucket)）。 |
| 图像或媒体文件未加载；浏览器控制台中出现 CORS 错误 | GCS 存储桶未为您的 Label Studio 域名配置 CORS。这仅在使用预签名 URL 时适用。 | 向存储桶添加 CORS 策略，允许从您的 Label Studio URL 进行 `GET` 和 `HEAD` 请求（请参阅[步骤 4](#Step-4-Configure-CORS-on-the-bucket-required-for-pre-signed-URLs)）。 |

</div>


## 使用 Label Studio API 添加存储

您也可以使用 API 以编程方式创建连接。[请参阅我们的 API 文档。](https://api.labelstud.io/api-reference/introduction/getting-started)

## 使用 IP 过滤增强 GCS 安全性

Google Cloud Storage 提供[存储桶 IP 过滤](https://cloud.google.com/storage/docs/ip-filtering-overview)作为强大的安全机制，可根据源 IP 地址限制对数据的访问。此功能有助于防止未经授权的访问，并提供对谁可以与您的存储桶交互的细粒度控制。

阅读有关 [VPC 后的源存储](security.html#Source-storage-behind-your-VPC) 的更多信息。

**常见用例：**
- 将存储桶访问限制为仅限您组织的 IP 范围
- 仅允许来自基础设施中特定 VPC 网络的访问
- 通过限制已知 IP 地址的访问来保护敏感数据
- 通过将第三方集成的 IP 加入白名单来控制访问

<details>
<summary>如何设置 IP 过滤</summary>
<br>

1. 首先，通过控制台或 CLI 创建您的 GCS 存储桶
2. 创建一个 JSON 配置文件来定义 IP 过滤规则。您有两种选择：
   对于公共 IP 范围：
```json
{
  "mode": "Enabled",
  "publicNetworkSource": {
    "allowedIpCidrRanges": [
      "xxx.xxx.xxx.xxx", // 您的第一个 IP 地址
      "xxx.xxx.xxx.xxx", // 您的第二个 IP 地址
      "xxx.xxx.xxx.xxx/xx" // 您的 CIDR 表示法的 IP 范围
    ]
  }
}
```

<div class="enterprise-only">

!!! note
    如果您在 app.humansignal.com 上使用 Label Studio Enterprise 并从您的办公网络访问它：
    - 添加 Label Studio Enterprise 的出站 IP 地址（请参阅 [IP 范围](saas.html#IP-ranges)）
    - 添加您办公网络的 IP 范围（例如 192.168.1.0/24）
    - 如果 Label Studio Enterprise 和您的办公室位于同一 VPN 网络上（例如 10.0.0.0/16），您只需要添加该 VPN 子网

</div>

对于 VPC 网络源：
```json
{
  "mode": "Enabled",
  "vpcNetworkSources": [
    {
      "network": "projects/PROJECT_ID/global/networks/NETWORK_NAME",
      "allowedIpCidrRanges": [
        RANGE_CIDR
      ]
    }
  ]
}
```

3. 使用以下命令将 IP 过滤规则应用到您的存储桶：
```bash
gcloud alpha storage buckets update gs://BUCKET_NAME --ip-filter-file=IP_FILTER_CONFIG_FILE
```

4. 在不再需要时移除 IP 过滤规则：
```bash
gcloud alpha storage buckets update gs://BUCKET_NAME --clear-ip-filter
```

#### 需注意的限制
- 所有规则最多 200 个 IP CIDR 块
- IP 过滤规则中最多 25 个 VPC 网络
- 不支持双区域存储桶
- 可能影响来自某些 Google Cloud 服务的访问

[阅读有关 GCS IP 过滤的更多信息](https://cloud.google.com/storage/docs/ip-filtering-overview)

</details>
