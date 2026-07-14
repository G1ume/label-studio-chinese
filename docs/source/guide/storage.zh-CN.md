---
title: 从外部存储同步数据
short: 添加项目存储
type: guide
tier: all
order: 151
order_enterprise: 151
meta_title: 云存储和外部存储集成
meta_description: "Label Studio 与 Amazon AWS S3、Google Cloud Storage、Microsoft Azure、Redis 以及本地文件目录集成的文档。"
section: "导入与导出"

---

将流行的云存储和外部存储系统与 Label Studio 集成，以收集上传到存储桶、容器、数据库或目录中的新项目，并返回标注结果，以便在机器学习流水线中使用。

| 存储 | 社区版 | 企业版 |
|---|---|---|
| [Amazon S3](storage_s3) | ✅ | ✅ |
| [Amazon S3（使用 IAM 角色）](storage_s3#Amazon-S3-with-IAM-role) | ❌ | ✅ |
| [Google Cloud Storage](storage_gcp) | ✅ | ✅ |
| [Google Cloud Storage WIF 身份验证](storage_gcp#Google-Cloud-Storage-with-Workload-Identity-Federation-WIF) | ❌ | ✅ |
| [Google Cloud Storage（GKE 服务账号模拟）](storage_gcp#Google-Cloud-Storage-with-service-account-impersonation-for-GKE) | ❌ | ✅ |
| [Microsoft Azure Blob Storage](storage_azure) | ✅ | ✅ |
| [Microsoft Azure Blob Storage（使用服务主体）](storage_azure#Azure-blob-storage-with-Service-Principal) | ❌ | ✅ |
| [Databricks 文件（UC Volumes）](storage_databricks) | ❌ | ✅ |
| [Databricks 文件（UC Volumes）使用服务主体](storage_databricks) | ❌ | ✅ |
| [Redis 数据库](storage_redis)| ✅ | ✅ |
| [本地存储](storage_local) | ✅ | ✅ |

您可以添加源存储连接以将数据从外部源同步到 Label Studio 项目，也可以添加目标存储连接以将标注从 Label Studio 同步到外部存储。

每个源存储和目标存储设置都是针对特定项目的。您可以为项目连接多个存储桶、容器、数据库或目录作为源存储或目标存储。

## 源存储

Label Studio 不会自动从源存储同步数据。如果您将新数据上传到已连接的云存储桶，请使用 UI 同步存储连接，将新的标注任务添加到 Label Studio 而无需重启。您也可以使用 API 来设置或同步存储连接。请参阅 [Label Studio API](https://api.labelstud.io/api-reference/introduction/getting-started) 并找到相关的存储连接类型。

从云存储同步的任务数据不会存储在 Label Studio 中。相反，数据通过预签名 URL 进行访问。您还可以使用 VPC 和 IP 限制来保护对云存储的访问。有关详细信息，请参阅 [保护云存储访问](security.html#Secure-access-to-cloud-storages)。

### 源存储权限

* 如果将导入方法设置为"文件"，Label Studio 后端仅需要 LIST 权限，不会从您的存储桶下载任何数据。

* 如果将导入方法设置为"任务"，Label Studio 后端将需要 GET 权限来读取 JSON 文件并将其转换为 Label Studio 任务。

当您的用户访问标注界面时，后端将尝试将 URI（例如 s3://）解析为 URL（https://）链接。URL 将返回给前端并由用户的浏览器加载。要加载这些 URL，浏览器需要您的云存储的 HEAD 和 GET 权限。HEAD 请求在开始时发出，允许浏览器确定音频、视频或其他文件的大小。然后浏览器发出 GET 请求以获取文件内容。

### 源存储同步和 URI 解析

源存储功能可以分为两个部分：
* 同步——Label Studio 扫描您的存储并从中导入任务。
* URI 解析——Label Studio 后端请求云存储将 URI 链接（例如 `s3://bucket/1.jpg`）解析为 HTTPS（`https://aws.amazon.com/bucket/1.jpg`）。这样，用户的浏览器就能加载媒体文件。

<img src="/images/source-cloud-storages.png" class="make-intense-zoom">

### 导入方法

!!! info
    "将每个存储桶对象视为源文件"选项已重命名并重新引入为"导入方法"下拉菜单。

Label Studio 源存储提供"导入方法"下拉菜单。此设置支持两种不同的将任务加载到 Label Studio 的方法。

##### 任务

当设置为"任务"时，JSON、JSONL/NDJSON 或 Parquet 格式的任务可以直接从存储桶加载到 Label Studio 中。这种方法在处理涉及多个媒体源的复杂任务时特别有用。

<img src="/images/source-storages-treat-off.png" class="make-intense-zoom">

您可以将多个任务放在同一个 JSON 文件中，但不能在同一文件中混合使用不同的任务格式。

{% details <b>简单任务示例</b> %}


`task_01.json`
```
{
  "image": "s3://bucket/1.jpg",
  "text": "opossums are awesome"
}
```

`task_02.json`
```
{
  "image": "s3://bucket/2.jpg",
  "text": "cats are awesome"
}
```

或者：

`tasks.json`
```
[
  {
    "image": "s3://bucket/1.jpg",
    "text": "opossums are awesome"
  },
  {
    "image": "s3://bucket/2.jpg",
    "text": "cats are awesome"
  }
]
```

{% enddetails %}

<br>

{% details <b>包含任务、标注和预测的示例</b> %}

`task_with_predictions_and_annotations_01.json`
```
{
    "data": {
        "image": "s3://bucket/1.jpg",
        "text": "opossums are awesome"
    },
    "annotations": [...],  
    "predictions": [...]
}
```

`task_with_predictions_and_annotations_02.json`
```
{
    "data": {
      "image": "s3://bucket/2.jpg",
      "text": "cats are awesome"
    }
    "annotations": [...],  
    "predictions": [...]
}
```

或者：

`tasks_with_predictions_and_annotations.json`
```
[
  {
      "data": {
          "image": "s3://bucket/1.jpg",
          "text": "opossums are awesome"
      },
      "annotations": [...],  
      "predictions": [...]
  },
  {
      "data": {
        "image": "s3://bucket/2.jpg",
        "text": "cats are awesome"
      }
      "annotations": [...],  
      "predictions": [...]
  }
]
```

{% enddetails %}

<br>

{% details <b>JSONL 示例</b> %}

`tasks.jsonl`
```
{ "image": "s3://bucket/1.jpg", "text": "opossums are awesome" }
{ "image": "s3://bucket/2.jpg", "text": "cats are awesome" }
```

{% enddetails %}

在 Label Studio Enterprise 和 Starter Cloud 版本中，Parquet 文件也可以像 JSON 和 JSONL 一样用于导入任务。

<br>

##### 文件

当设置为"文件"时，Label Studio 自动列出存储桶中的文件并构建任务。这仅适用于涉及单个媒体源（如图像、文本等）的简单标注任务。*

<img src="/images/source-storages-treat-on.png" class="make-intense-zoom">


### 预签名 URL vs. 存储代理

Label Studio 从云存储获取媒体数据有两种安全机制：通过预签名 URL 和通过代理。使用哪种取决于您在设置源存储时是否开启了**使用预签名 URL**。**使用预签名 URL** 是默认启用的。当**使用预签名 URL** 关闭时，代理存储将被启用。

<div class="enterprise-only">

!!! note
    您可以控制组织是否允许使用存储代理。前往**组织 > 使用与许可证**，选择**启用存储代理**。此操作必须由具有 Owner 角色的用户执行。

    当**启用存储代理**被禁用时，组织中的用户将无法创建或修改"预签名 URL"关闭的源存储连接。此限制确保所有存储连接必须使用预签名 URL。

</div>

<br/>

#### 预签名 URL

在此场景中，您的浏览器收到 HTTP 303 重定向到有时间限制的 S3/GCS/Azure 预签名 URL。这是默认行为。

使用预签名 URL 的主要好处是，如果您希望尽可能地将媒体文件与 Label Studio 网络**隔离**。

<img src="/images/storages/storage-proxy-presigned.png" style="max-width:600px; margin: 0 auto" alt="预签名 URL 流程图">

所需的权限已包含在下方的云存储配置文档中。


#### 代理存储

在代理模式下，Label Studio 后端在服务器端获取对象并将其直接流式传输到浏览器。

<img src="/images/storages/storage-proxy.png" style="max-width:600px; margin: 0 auto" alt="代理流程图">

这有多种好处，包括：

- **安全性**
    - 媒体文件的访问进一步受到 Label Studio 用户角色和项目访问权限的限制。
    - 此访问控制也适用于缓存文件。这意味着即使媒体被缓存，如果用户对任务的访问被撤销，该文件的访问也将受到限制。
    - 数据保留在 Label Studio 网络边界内。这对于希望为其网络流量维护单一入口点的本地部署环境特别有用。
- **配置**
    - 无需 CORS 设置。
    - 无需预签名权限。

要允许代理存储，您需要确保权限包含以下内容：

{% details <b>AWS S3</b> %}

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetObject",
                "s3:ListBucket"
            ],
            "Resource": [
                "arn:aws:s3:::your-bucket-name",
                "arn:aws:s3:::your-bucket-name/*"
            ]
        }
    ]
}

```

{% enddetails %}

<br>

{% details <b>Google Cloud Storage</b> %}

- `storage.objects.get` - 读取对象数据和元数据
- `storage.objects.list` - 列出存储桶中的对象（如果使用前缀）

{% enddetails %}

<br>

{% details <b>Azure Blob Storage</b> %}

添加**Storage Blob Data Reader**角色，其中包括：
- `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read`
- `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/getTags/action`

{% enddetails %}

<br>

!!! note 本地部署注意事项
    大型媒体文件以连续的 8 MB 块流式传输，这些块被拆分为不同的 GET 请求。这可能导致频繁请求后端以获取下一部分数据，并消耗额外的资源。

    您可以使用以下环境变量进行配置：

    * `RESOLVER_PROXY_MAX_RANGE_SIZE` - 默认为 8 MB，定义每个请求返回的最大块大小。
    * `RESOLVER_PROXY_TIMEOUT` - 默认为 20 秒，定义 uWSGI worker 在单个请求上花费的最长时间。


## 目标存储

如果您配置了目标存储，您的标注将保存在两个位置：Label Studio 数据库和您的目标存储中。

* 当标注者在标注任务时点击**提交**或**更新**，标注将同时发送到目标存储和 Label Studio 数据库。
* 当用户点击目标存储上的**同步**按钮时，所有标注将从头重新保存。

目标存储接收每个标注的 JSON 格式导出。有关导出任务在目标存储中的显示方式，请参阅 [已标注任务的 Label Studio JSON 格式](export#Label-Studio-JSON-format-of-annotated-tasks)。

您还可以在 Label Studio 中删除标注时同步删除目标存储中的标注。默认情况下此选项是关闭的。

### 目标存储权限

要使用此类型的存储，您必须具有 PUT 权限，DELETE 权限是可选的。

## 故障排除

在使用外部云存储连接时，请注意以下几点：

* 对于源存储：
   * 当选择**文件**导入方法时，Label Studio 不会导入存储在存储桶中的数据，而是创建对对象的*引用*。因此，您可以完全控制要同步并在标注屏幕上显示的数据。
   * 当选择**任务**导入方法时，存储桶文件被视为不可变；将更新后的文件状态推送到 Label Studio 的唯一方法是使用新文件名上传到存储，或删除与该文件关联的所有任务并重新同步。
* 与外部存储桶的同步操作是单向的。它要么从存储桶中的对象创建任务（源存储），要么将标注推送到输出存储桶（目标存储）。在存储桶端更改内容不能保证结果的一致性。
* 我们建议为每个 Label Studio 项目使用单独的存储桶文件夹。
* 存储区域：为了最小化延迟并提高效率，请将数据存储在离您的团队更近而不是离 Label Studio 服务器更近的云存储桶中。

<div class="opensource-only">

有关更多故障排除信息，请参阅 [Label Studio 故障排除](troubleshooting)。

</div>

<div class="enterprise-only">

有关更多故障排除信息，请参阅 HumanSignal 支持中心中的 [导入、导出和存储故障排除](https://support.humansignal.com/hc/en-us/sections/16982163062029-Import-Export-Storage)。

</div>
