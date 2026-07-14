---
title: 在 Label Studio 中设置 Webhook
short: Webhook
type: guide
tier: all
order: 407
order_enterprise: 407
meta_title: 在 Label Studio 中配置 Webhook
meta_description: 用于设置和配置 Webhook 的 Label Studio 文档，以便将 Label Studio 与您的机器学习流水线集成。
section: "集成与扩展"

---

Label Studio 中的 Webhook 允许您设置集成，订阅 Label Studio 内部发生的某些事件。当事件触发时，Label Studio 会向配置的 Webhook URL 发送 HTTP POST 请求。例如：
```json
{
    "action": "TASK_CREATED",
    "tasks": [
        {"id": 1, ...},
        ...
    ]
}
```

然后，您的应用程序或服务可以以任何您想要的方式响应该事件信息。

## Label Studio Webhook 的用途

Label Studio 中发生的某些事件是对 API 调用的响应。对于这些事件，API 响应足以知道操作已发生。但其他事件发生在 Label Studio UI 内部，例如项目更改和任务标注活动。要立即获知这些更改，您可以使用 Webhook 通知您的应用程序或机器学习流水线采取特定操作。

您的应用程序或流水线中 Webhook 所执行的操作可能因事件而异。例如，您可以使用发送到 Webhook URL 的事件来执行以下操作：
- 在标注了足够数量的任务后开始训练机器学习模型。
- 在任务被标注后执行主动学习。
- 在项目完全设置好后提示标注人员开始工作。
- 在数据集版本控制仓库中创建训练数据的新版本。

通过仅订阅与您的用例相关的事件来限制对服务器的请求数量。

## 可用的 Label Studio Webhook
Label Studio 提供两大类事件用于 Webhook 集成：项目级任务事件和组织事件。

<table>
<thead>
  <tr>
    <th>事件操作</th>
    <th>详情</th>
    <th>用例</th>
  </tr>
  </thead>
  <tr>
    <td><a href="webhook_reference.html#Task-Created">任务已创建</a></td>
    <td>针对特定项目，当新任务被创建时触发。每次导入操作触发一个事件。批量任务创建作为一个事件发送。</td>
    <td>用于在机器学习流水线中执行操作。</td>
  </tr>
  <tr>
    <td><a href="webhook_reference.html#Task-Deleted">任务已删除</a></td>
    <td>针对特定项目，当任务被删除时触发。每次删除操作触发一个事件。批量任务删除作为一个事件发送。</td>
    <td>用于更新训练数据集版本。</td>
  </tr>
  <tr>
    <td><a href="webhook_reference.html#Annotation-Created">标注已创建</a></td>
    <td>针对特定项目，当任何任务的新标注被创建时触发。每个标注一个事件。</td>
    <td>用于在主动学习场景中启动训练。</td>
  </tr>
  <tr>
    <td><a href="webhook_reference.html#Annotation-Updated">标注已更新</a></td>
    <td>针对特定项目，当现有标注被更新、覆盖或任务被跳过时触发。</td>
    <td>用于触发模型重新训练。</td>
  </tr>
  <tr>
    <td><a href="webhook_reference.html#Annotation-Deleted">标注已删除</a></td>
    <td>针对特定项目，当标注被删除时触发。批量标注删除作为一个事件发送。</td>
    <td>用于创建训练数据集的新版本。</td>
  </tr>
  <tr>
    <td><a href="webhook_reference.html#Project-Created">项目已创建</a></td>
    <td>针对组织，当项目被创建时触发。您必须<a href="webhooks.html#Enable-organization-level-webhooks">启用组织级 Webhook</a>才能发送此事件。</td>
    <td>用于创建新的数据管理流水线。</td>
  </tr>
  <tr>
    <td><a href="webhook_reference.html#Project-Updated">项目已更新</a></td>
    <td>当项目设置（例如标注配置）被更新、创建或保存时触发。</td>
    <td>用于更新现有的数据管理流水线。</td>
  </tr>
  <tr>
    <td><a href="webhook_reference.html#Project-Deleted">项目已删除</a></td>
    <td>针对组织，当项目被删除时触发。您必须<a href="webhooks.html#Enable-organization-level-webhooks">启用组织级 Webhook</a>才能发送此事件。</td>
    <td>用于移除数据管理流水线。</td>
  </tr>
</table>

### 启用组织级 Webhook

要使用为每个项目触发事件的组织级 Webhook，您必须[设置环境变量](https://labelstud.io/guide/start#Set-environment-variables)。
```shell
LABEL_STUDIO_ALLOW_ORGANIZATION_WEBHOOKS=true
```

## 如何集成 Webhook

要在 Label Studio 中集成 Webhook，您必须执行以下操作：
1. 在您的应用程序或机器学习模型流水线中创建一个 Webhook 端点或 URL。
2. 确保 Webhook 端点或 URL 可以被您的 Label Studio 实例访问，并且能够接受 HTTP POST 请求。
3. [在 Label Studio 中添加 Webhook](webhooks.html#Add-a-new-webhook-in-Label-Studio)。

设置您的 Webhook 端点以读取来自 Label Studio 的事件负载，并根据事件采取相应操作。

## 在 Label Studio 中添加新的 Webhook

在 Label Studio 中设置一个 Webhook URL，并将其与一个或多个事件触发器关联。您可以在 Label Studio UI 中或使用 API 来设置 Webhook URL。您可以根据需要配置任意数量的 Webhook 连接，但过多的连接可能会使您的实例不堪重负。

### 在 Label Studio UI 中添加 Webhook

向 Label Studio 添加 Webhook URL。该 Webhook URL 必须设置为能够接受 HTTP POST 请求。

1. 在 Label Studio UI 中，打开您想要关联 Webhook URL 的项目。
2. 点击 **设置（Settings）**，然后点击 **Webhooks**。
3. 点击 **添加 Webhook（Add Webhook）**。
4. 在 **负载 URL（Payload URL）** 字段中，提供发送事件负载的目标 URL。例如，`https://www.example.com/webhook`。
5. （可选）切换 **是否活跃（Is Active）** 选项以停用 Webhook，直到准备好使用为止。否则，Webhook 在保存后立即生效，事件会被发送到该 URL。
6. （可选）点击 + 号添加 Webhook URL 所需的任何请求头。指定请求头名称和值。您可以使用请求头来验证对 Webhook URL 的请求。例如，`Authorization` 和 `Basic bGFiZWxzdHVkaW86ZXhhbXBsZQ==`。
7. （可选）选择是否随事件发送负载数据。默认情况下会发送负载数据。如果您不发送负载数据，则仅发送 `action` 键。例如，选择发送`标注已创建`事件的负载数据，以便根据负载中发送的项目 ID 更新正确的流水线。
8. （可选）选择是发送 Label Studio 中 Webhook 支持的所有操作的事件，还是仅发送特定事件。默认情况下，会发送所有操作的事件。例如，选择 `标注已创建` 事件。
9. 保存 Webhook。

### 使用 Label Studio API 添加 Webhook

向[创建 Webhook](/api#tag/Webhooks/) 端点发送 POST 请求，以使用 API 添加 Webhook。如果您想通过自定义事件扩展 Webhook，请参阅[创建自定义 Webhook 事件](webhook_create.html)。

## 故障排除 Webhook 连接

Webhook 连接在 1 秒后超时。您可以通过设置环境变量 `WEBHOOK_TIMEOUT` 来调整超时时间。

如果 Label Studio 无法访问 Webhook URL，您可以在日志中的回溯信息中看到相关错误。

Label Studio 不会重试失败的 Webhook 连接。您可以在 DEBUG 模式下查看日志中成功的 Webhook 交付记录。
