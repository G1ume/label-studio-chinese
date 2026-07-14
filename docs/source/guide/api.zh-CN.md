---
title: Label Studio API 参考文档
short: API
type: guide
tier: all
order: 401
order_enterprise: 401
meta_title: API 端点
meta_description: API 文档，涵盖身份验证、数据科学项目列表、导入预测和原始数据、导出标注数据以及用户管理。
section: "集成与扩展"

---

您可以使用 Label Studio API 导入待标注的数据、导出标注结果、设置机器学习以及将任务与云存储同步。

有关更多指导和交互式示例，请参阅 [API 参考文档](https://api.labelstud.io/api-reference/introduction/getting-started)。如果您想使用 API 编写 Python 脚本，请使用 [Label Studio Python SDK](sdk.html)。

!!! info 提示
    有关使用 API 的更多指导，请参阅 [5 Tips and Tricks for Label Studio's API and SDK](https://labelstud.io/blog/5-tips-and-tricks-for-label-studio-s-api-and-sdk/)。


### API 身份验证

您必须获取访问令牌才能通过 API 进行身份验证。

您能否创建令牌以及可以创建哪种类型的令牌，取决于您的组织设置。

!!! note "API 密钥 vs. 访问令牌"
    在 Label Studio 中，**"访问令牌"**和**"API 密钥"**含义相同，可以互换使用。

访问令牌/API 密钥有两种类型：**个人访问令牌 (PAT)** 和**旧版令牌**。

您可以在此处阅读有关两者之间区别的更多信息：[访问令牌](access_tokens)。

#### 查找您的访问令牌

1. 打开 Label Studio，点击右上角的用户图标。选择**账户与设置**。
2. 在左侧选择**个人访问令牌**或**旧版令牌**。

根据您选择的不同，您需要生成新令牌或复制显示的令牌。


#### 使用个人访问令牌验证 HTTP API 请求

PAT 在 HTTP API 请求中使用 `'Authorization: Bearer <token>'` 格式，例如：

```bash
curl -X <method> <Label Studio URL>/api/<endpoint> -H 'Authorization: Bearer <token>'
```

#### 使用旧版令牌验证 HTTP API 请求

旧版令牌在 HTTP API 请求中使用 `'Authorization: Token <token>'` 格式，例如：

```bash
curl -X <method> <Label Studio URL>/api/<endpoint> -H 'Authorization: Token <token>'
```

#### 验证 Python SDK 请求

在使用 SDK 时，您无需区分旧版令牌和个人访问令牌。

```python
# 定义 Label Studio 可访问的 URL
LABEL_STUDIO_URL = 'YOUR_BASE_URL'

# API 密钥可以是您的 PAT 或旧版访问令牌
LABEL_STUDIO_API_KEY = 'YOUR_API_KEY'

# 导入 SDK 和客户端模块
from label_studio_sdk import LabelStudio
client = LabelStudio(base_url=LABEL_STUDIO_URL, api_key=LABEL_STUDIO_API_KEY)
```

请参阅 [API 身份验证文档](https://api.labelstud.io/api-reference/introduction/getting-started#authentication)。

### 列出所有项目

要使用 Label Studio API 执行大多数任务，您必须指定项目 ID，有时也称为 `pk`（主键）。如果您不知道项目 ID，可能需要获取您在 Label Studio 中可访问的所有项目的列表。请参阅 [列出项目 API 端点文档](https://api.labelstud.io/api-reference/api-reference/projects/list)。

### 创建和设置项目

使用 API 创建项目并在 Label Studio 中设置标注界面。请参阅 [创建新项目 API 端点文档](https://api.labelstud.io/api-reference/api-reference/projects/create)。

如果您想在使用 API 提交标注界面配置之前确保其有效，可以使用 [验证标注配置](https://api.labelstud.io/api-reference/api-reference/projects/validate-config) API 端点。

### 使用 API 导入任务

要使用 API 导入任务，请确保您知道要添加任务的目标项目 ID。有关更多示例和参数说明，请参阅 [导入数据端点文档](https://api.labelstud.io/api-reference/api-reference/tasks/create)。

### 获取任务
获取特定项目的分页任务列表。如果需要，您也可以使用此 API 端点获取任务和标注，作为导出标注的替代方法。有关详细信息和参数，请参阅 [列出项目任务端点文档](https://api.labelstud.io/api-reference/api-reference/tasks/list)。

### 导出标注

要导出标注，请首先查看 [项目可用的导出格式](https://api.labelstud.io/api-reference/api-reference/projects/exports/list-formats)。

从响应中选择您想要的格式，然后调用导出端点。有关更多详细信息，请参阅 [导出标注](https://api.labelstud.io/api-reference/api-reference/projects/exports/download-sync) 端点文档。
