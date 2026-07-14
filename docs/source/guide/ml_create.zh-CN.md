---
title: 编写自己的 ML 后端
short: 编写自己的 ML 后端
type: guide
tier: all
order: 254
order_enterprise: 254
meta_title: 编写自己的 ML 后端
meta_description: 设置您的机器学习模型以在数据科学和数据标注项目中输出和使用预测结果。
section: "机器学习"

---

使用 Label Studio ML 后端将 Label Studio 与机器学习模型集成。Label Studio ML 后端是一个 SDK，您可以用它来封装您的机器学习模型代码并转化为一个 Web 服务器。该机器学习服务器使用 [uWSGI](https://uwsgi-docs.readthedocs.io/en/latest/) 和 [supervisord](http://supervisord.org/)，并通过 [RQ](https://python-rq.org/) 处理后台训练任务。

按照以下步骤使用 Label Studio ML SDK 封装自定义机器学习模型代码，或查看[我们的 ML 后端示例库](ml_tutorials.html)以集成流行的机器学习框架和工具，如 [Huggingface Transformers](https://huggingface.co/docs/transformers/index)、[OpenAI](https://openai.com/)、[Langchain](https://www.langchain.com/) 等。

有关使用 Label Studio 示例后端的信息，请参见[设置示例 ML 后端](ml#Set-up-an-example-ML-backend)。

<div class="opensource-only">

!!! error enterprise
    Label Studio Enterprise 提供了开箱即用的 LLM 辅助标注解决方案。您只需要连接我们支持的模型之一即可完成设置。有关更多信息，请参见 [Prompts](https://docs.humansignal.com/guide/prompts_overview)。

</div>

<div class="enterprise-only">

!!! info 提示
    Prompts 是一个开箱即用的 LLM 辅助标注解决方案。您只需要连接我们支持的模型之一即可完成设置。有关更多信息，请参见 [Prompts](prompts_overview)。

</div>

有关视频教程，请观看以下内容：

<iframe width="560" height="315" src="https://www.youtube.com/embed/cEwMNOlOUjI?si=sEPTUPOyyzZlA-n5" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>


## 1. 安装 ML 后端仓库

从仓库下载并安装 `label-studio-ml-backend`：

```bash
git clone https://github.com/HumanSignal/label-studio-ml-backend.git
cd label-studio-ml-backend/
pip install -e .
```

## 2. 创建一个空的 ML 后端

```bash
label-studio-ml create my_ml_backend
```

这将创建以下目录结构，您可以修改它以实现自己的推理逻辑：

```
my_ml_backend/
├── Dockerfile
├── .dockerignore
├── docker-compose.yml
├── model.py
├── _wsgi.py
├── README.md
├── requirements-base.txt
├── requirements-test.txt
├── requirements.txt
└── test_api.py
```

其中：

* `Dockerfile`、`docker-compose.yml` 和 `.dockerignore` 用于使用 Docker 运行 ML 后端。
* `model.py` 是您实现自己的训练和推理逻辑的主要文件。
* `_wsgi.py` 是用于使用 Docker 运行 ML 后端的辅助文件（您无需修改此文件）。
* `README.md` 必须包含如何运行 ML 后端的说明。
* `requirements.txt` 是您放置 Python 依赖项的位置。
* `requirements_base.txt` 和 `requirements_test.txt` 是基本依赖项（您无需修改这些文件）
* `test_api.py` 是您放置模型测试的位置


## 3. 实现预测逻辑

在您的模型目录中，找到 `model.py` 文件（例如 `my_ml_backend/model.py`）。

`model.py` 文件包含一个继承自 `LabelStudioMLBase` 的类声明。该类提供了 Label Studio 用于与 ML 后端通信的 API 方法包装器。您可以重写这些方法来实现自己的逻辑：

```python
def predict(self, tasks, context, **kwargs):
    """为任务生成预测结果。"""
    return predictions
```

`predict` 方法用于为任务生成预测。它使用以下参数：

- `tasks`：[JSON 格式的 Label Studio 任务](task_format)
- `context`：[JSON 格式的 Label Studio 上下文](#在您的 ML 后端中支持交互式预标注)——用于交互式标注场景
- `predictions`：[JSON 格式的预测数组](export#Raw-JSON-format-of-completed-tasks)

一旦实现了 `predict` 方法，您就可以在 Label Studio 中看到来自连接的 ML 后端的预测结果。

### 在您的 ML 后端中支持交互式预标注

如果您希望在机器学习后端中支持交互式预标注，请使用 `predict()` 方法编写推理调用。有关为文本标注项目实现此功能的示例，请参见[此子字符串匹配代码示例](https://github.com/HumanSignal/label-studio-ml-backend/tree/master/label_studio_ml/examples/interactive_substring_matching)。

完成以下步骤：

1. 使用上述 `predict()` 方法定义推理调用。`predict()` 方法接收任务数据和上下文数据：
  - `tasks` 参数包含有关正在预标注的任务的详细信息。参见[Label Studio 任务的 JSON 格式](task_format)。
  - `context` 参数包含有关在 Label Studio 中执行的标注操作的详细信息，例如发送的高亮文本字符串，格式为 [Label Studio 标注结果格式](export#Label-Studio-JSON-format-of-annotated-tasks)。

    `context` 具有以下属性。
    - `annotation_id`：标注 ID。
    - `draft_id`：草稿标注 ID。
    - `user_id`：用户 ID。
    - `result`：这是标注结果，但包含一个 `is_positive: true` 标志，用户可以进行更改。例如，通过按 **Alt** 键并使用关键点与 UI 中的图像交互。

2. 使用任务和上下文数据，利用从 Label Studio 接收到的数据构建预测。
3. 按照 [Label Studio 预测格式](predictions.html#Format-pre-annotations-for-Label-Studio)返回结果，该格式因执行的标注类型而异。

有关如何在 NER 标注项目中实现此功能的更多详细信息，请参考上面链接的代码示例。

有关启用预标注的更多信息，请参见[交互式预标注](ml#Interactive-pre-annotations)。


## 4. 实现训练逻辑（可选）

您还可以实现 `fit` 方法来训练您的模型。`fit` 方法通常用于在标注数据上训练模型，但也可用于任何需要数据持久化的任意操作（例如，将标注数据存储到数据库、保存模型权重、保留 LLM 提示历史等）。

默认情况下，`fit` 方法会在 Label Studio 中的任何数据操作时调用，例如创建新任务或更新标注。您可以使用 [Webhooks](webhooks) 修改此行为。

要实现 `fit` 方法，您需要在 `model.py` 文件中重写 `fit` 方法：

```python
def fit(self, event, data, **kwargs):
    """在标注数据上训练模型。"""
    old_model = self.get('old_model')
    # 编写更新模型的逻辑
    self.set('new_model', new_model)
```

其中：

- `event`：事件类型。可以是 `'ANNOTATION_CREATED'`、`'ANNOTATION_UPDATED'` 等。
- `data`：从事件接收的负载数据（参见 [Webhook 事件参考](webhook_reference)）。

此外，有两个辅助方法可用于在 ML 后端中存储和检索数据：

- `self.set(key, value)` - 在 ML 后端中存储数据
- `self.get(key)` - 从 ML 后端检索数据

这两个方法都可以在 ML 后端代码的其他地方使用，例如，在 `predict` 方法中获取新的模型权重。

### 使用 Webhooks 触发训练

从 Label Studio 1.4.1 版本开始，当您向项目添加 ML 后端时，Label Studio 会创建一个指向 ML 后端的 webhook，以便在每次创建或更新标注时发送事件。

默认情况下，webhook 事件的负载不包含标注本身。您可以[修改由 Label Studio 发送的 webhook 事件](webhooks)以发送完整负载，或者使用 Label Studio API 通过[按 ID 获取标注端点](/api#operation/api_annotations_read)检索标注，使用 [SDK](sdk.html) 的[按 ID 获取任务方法](https://labelstud.io/sdk/project.html#label_studio_sdk.project.Project.get_task)，或者从您[设置的目标存储](storage)中检索标注。

有关 webhook 事件的更多详细信息，请参见[标注 webhook 事件参考](webhook_reference#Annotation-Created)。


### 其他方法和参数

`LabelStudioMLBase` 类中还有其他可用的方法和参数：

- `self.label_interface` - 返回包含标注任务所有信息的 Label Studio 标签界面对象。
- `self.model_version` - 返回当前模型版本。

## 4. 确保 ML 后端可以访问 Label Studio 数据

如果您的数据存储在云端、本地目录中，或已导入 Label Studio，您需要设置 `LABEL_STUDIO_URL` 和 `LABEL_STUDIO_API_KEY` 环境变量。

有关更多信息，请参见[允许 ML 后端访问 Label Studio 数据](ml#Allow-the-ML-backend-to-access-Label-Studio-data)。

## 5. 运行 ML 后端服务器

使用 Docker Compose 运行：

   ```bash
   docker-compose up
```

ML 后端服务器可在 `http://localhost:9090` 访问。在[将 ML 后端连接到 Label Studio](ml#Connect-the-model-to-Label-Studio) 时可以使用此 URL。

!!! note
    `localhost` 是一个特殊的域名，直接回环到您的本地环境。在 Docker 托管的容器中，这会回环到容器本身，而不是容器所在的宿主机。Docker 提供了一个特殊域名作为此问题的变通方案：`host.docker.internal`。如果您在 Docker 内托管 Label Studio 和 ML 后端，请尝试使用该域名代替 localhost。（`http://host.docker.internal:9090`）


### 不使用 Docker 运行

要在不使用 Docker 的情况下运行（例如用于调试目的），可以使用以下命令：

```bash
pip install -r my_ml_backend
label-studio-ml start my_ml_backend
```

### 修改主机和端口

要修改主机和端口，请使用以下命令行参数：

```bash
label-studio-ml start my_ml_backend -p 9091 --host 0.0.0.0
```

### 测试您的 ML 后端

修改 `my_ml_backend/test_api.py` 以确保您的 ML 后端按预期工作。

## 6. 将 ML 后端连接到 Label Studio

您可以使用 API 或**设置 > 模型**。有关更多信息，请参见[将模型连接到 Label Studio](ml#Connect-the-model-to-Label-Studio)。
