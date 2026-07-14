---
title: 将 Label Studio 集成到您的机器学习流水线
short: 机器学习集成
type: guide
tier: all
order: 251
order_enterprise: 251
meta_title: 将 Label Studio 集成到您的机器学习流水线
meta_description: 用于将模型开发流水线与数据标注工作流无缝集成的机器学习框架。
section: "机器学习"
---

您可以使用 ML 后端将模型开发流水线与数据标注工作流集成。有几种使用场景，包括：

- **预标注/自动标注数据：** 让 ML/AI 模型自主预测标签，然后由人工标注人员审核。
- **交互式标注：** 将 ML 模型集成到平台中，帮助人工更高效、更准确地标注或注释大型数据集。
- **模型评估及调优：** 标注人员审查和分析模型输出，以评估模型准确性并优化性能。

<div class="opensource-only">

!!! error enterprise
    Label Studio Enterprise 提供开箱即用的 LLM 辅助自动标注和模型评估解决方案。可基于准确性和成本比较不同的 LLM。有关更多信息，请参阅 [Prompts](https://docs.humansignal.com/guide/prompts_overview)。

</div>

<div class="enterprise-only">

!!! info 提示
    Prompts 是 LLM 辅助自动标注和模型评估的开箱即用解决方案。有关更多信息，请参阅 [Prompts](prompts_overview)。

</div>

例如，对于图像分类任务，模型预先选择图像类别供数据标注人员验证。对于音频转录，模型显示转录结果供数据标注人员修改。

连接模型后，其工作方式如下：

1. 用户打开任务。
2. Label Studio 向 ML 后端发送请求。
3. ML 后端响应其预测结果。
4. 预测结果加载到 Label Studio UI 中并显示给标注人员。

如果您只需要将静态预注释数据加载到 Label Studio 中，运行 ML 后端可能过于复杂。此时，您可以[导入预注释数据](predictions)。


!!! info 提示
    您可以使用 [Label Studio Enterprise 通过机器学习模型后端构建自动化的主动学习循环](https://docs.humansignal.com/guide/active_learning.html)。如果您使用的是开源社区版的 Label Studio，您可以手动排序任务并获取预测结果来模拟主动学习过程。

## 设置示例 ML 后端

Label Studio ML 后端是一个 SDK，它封装您的机器学习代码并将其转换为 Web 服务器。该 Web 服务器可以连接到正在运行的 Label Studio 实例，以自动执行标注任务。我们提供了一个[示例模型库](https://github.com/HumanSignal/label-studio-ml-backend?tab=readme-ov-file#models)，您可以在自己的工作流中使用，或根据需要扩展和自定义。

如果您想自己编写模型，请参阅[编写您自己的 ML 后端](ml_create)。

### 准备工作

<div class="opensource-only">

* [Label Studio](install)
* [Docker Compose](https://docs.docker.com/compose/install/)

</div>

<div class="enterprise-only">

* [Label Studio](install_enterprise)
* [Docker Compose](https://docs.docker.com/compose/install/)

</div>

### 启动模型

1. 首先，确定您要使用哪个[模型](#示例模型)，并查看所需参数（点击每个模型的链接查看完整参数列表）。

    在模型目录中的 `docker-compose.yml` 文件中设置您的参数。

2. 然后将以下命令中的 `{MODEL_NAME}` 替换为相应的目录名。

    例如，如果您使用 SAM 后端和 [SegmentAnything 模型](https://segment-anything.com/)，模型名称将是 `segment_anything_model`，这与[仓库中的目录名](https://github.com/HumanSignal/label-studio-ml-backend/tree/master/label_studio_ml/examples/segment_anything_model)相对应：

    ```bash
git clone https://github.com/HumanSignal/label-studio-ml-backend.git
cd label-studio-ml-backend/label_studio_ml/examples/segment_anything_model
docker-compose up
    ```

模型应在 `http://localhost:9090` 上开始运行（如果您使用 Docker 容器，[请参阅下面的说明](#localhost-和-Docker-容器)）。您可以通过点击模型旁的溢出菜单中的**发送测试请求**或使用以下命令来验证：

```bash
> curl http://localhost:9090
{"model_class":"SamMLBackend","status":"UP"}
```

<div class="opensource-only">

如果您看到任何错误，请参阅 [ML 后端故障排除](troubleshooting#ML-backends)和 [README 中的故障排除部分](https://github.com/HumanSignal/label-studio-ml-backend/tree/master/README.md#troubleshooting)。

</div>

<div class="enterprise-only">

如果您看到任何错误，请参阅 HumanSignal 支持中心中的 [ML 后端和预测故障排除](https://support.humansignal.com/hc/en-us/sections/23627938255117-ML-Backend-Predictions)，并查看 [README 中的故障排除部分](https://github.com/HumanSignal/label-studio-ml-backend/tree/master/README.md#troubleshooting)。

</div>

#### localhost 和 Docker 容器

`localhost` 是一个特殊的域名，直接回环到您的本地环境。

如果您在 Docker 容器中运行 Label Studio，`localhost` 回环到容器本身，而不是托管容器的机器。Docker 提供了一个特殊域名作为此问题的解决方案，即 `host.docker.internal`。如果您在 Docker 内部同时托管 Label Studio 和您的 ML 后端，请尝试使用该域名代替 localhost（`http://host.docker.internal:9090`）或内部 IP 地址。

### 将模型连接到 Label Studio

<div class="enterprise-only">

[创建项目](setup_project)后，打开项目设置并选择[**模型**](project_settings_lse#Model)。

</div>

<div class="opensource-only">

[创建项目](setup_project)后，打开项目设置并选择[**模型**](project_settings#Model)。

</div>

点击**连接模型**并填写以下字段：

| 字段 | 描述 |
| -------- | -------------------------------------------------------------------------------------- |
| **名称**   | 输入模型的名称。                        |
| **后端 URL**  | 输入模型的 URL。<br /><br />如果您按照上述步骤操作，这将是 `http://localhost:9090`。如果您在 Docker 容器中运行 Label Studio，[请参阅上面的说明](#localhost-和-Docker-容器)。 |
| **选择认证方式**   | 如果访问模型需要用户名和密码，您可以选择**基本认证**并在此输入。                                     |
| **额外参数**  | 输入您想要传递给模型的任何其他参数。                                      |
| **交互式预注释**  | 启用此选项允许模型通过提供实时预测或建议来辅助标注过程，帮助标注人员在处理任务时更高效地工作。<br /><br />换句话说，当您与数据交互时（例如，在图像上绘制区域、高亮文本或向 LLM 提问），ML 后端接收此输入并返回基于该输入的预测。有关更多信息，请参阅下面的[交互式预注释](#交互式预注释)。 |

!!! info 提示
    您也可以[使用 API 添加 ML 后端](https://api.labelstud.io/api-reference/api-reference/ml/create)。您需要项目 ID 和机器学习后端 URL。

## 示例模型

[ML 后端仓库](https://github.com/HumanSignal/label-studio-ml-backend)包含各种示例模型，您可以试验并调整以适应自己的工作流。

其中一些模型无需任何额外配置即可使用。查看**所需参数**列以了解是否需要设置任何额外参数。如果模型有必需参数，您可以在模型目录中的 `docker-compose.yml` 中设置这些参数。

- **预标注**列指示模型是否可用于 Label Studio 中的预标注：
  打开标注页面时或在批量数据运行预测后，您可以看到预注释数据。
- **交互模式**列指示模型是否可用于 Label Studio 中的交互式标注：在执行标注页面上的操作时查看交互式预测。
- **训练**列指示模型是否可用于 Label Studio 中的训练：根据提交的注释更新模型状态。

| MODEL_NAME                                                                                 | 描述                                                                                                                               | 预标注 | 交互模式 | 训练 | 所需参数                           |
|--------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------|-----------------|------------------|----------|-----------------------------------------------|
| [segment_anything_model](https://github.com/HumanSignal/label-studio-ml-backend/tree/master/label_studio_ml/examples/segment_anything_model)                 | [Meta](https://segment-anything.com/) 的图像分割                                                                               | ❌               | ✅                |   ❌       | 无                                          |
| [llm_interactive](https://github.com/HumanSignal/label-studio-ml-backend/tree/master/label_studio_ml/examples/llm_interactive)                               | 使用 [OpenAI](https://platform.openai.com/)、Azure LLM 进行提示工程。                                                               | ✅               | ✅                | ✅        | OPENAI_API_KEY                                |
| [grounding_dino](https://github.com/HumanSignal/label-studio-ml-backend/tree/master/label_studio_ml/examples/grounding_dino)                                 | 带提示的目标检测。[详情](https://github.com/IDEA-Research/GroundingDINO)                                                  | ❌               | ✅                | ❌        | 无                                          |
| [tesseract](https://github.com/HumanSignal/label-studio-ml-backend/tree/master/label_studio_ml/examples/tesseract)                                           | 交互式 OCR。[详情](https://github.com/tesseract-ocr/tesseract)                                                                    | ❌               | ✅                | ❌        | 无                                          |
| [easyocr](https://github.com/HumanSignal/label-studio-ml-backend/tree/master/label_studio_ml/examples/easyocr)                                               | 自动化 OCR。[EasyOCR](https://github.com/JaidedAI/EasyOCR)                                                                             | ✅               | ❌                | ❌        | 无                                          |
| [spacy](https://github.com/HumanSignal/label-studio-ml-backend/tree/master/label_studio_ml/examples/spacy)                                                   | 基于 [SpaCy](https://spacy.io/) 的 NER                                                                                                         | ✅               | ❌                | ❌        | 无                                          |
| [flair](https://github.com/HumanSignal/label-studio-ml-backend/tree/master/label_studio_ml/examples/flair)                                                   | 基于 [flair](https://flairnlp.github.io/) 的 NER                                                                                               | ✅               | ❌                | ❌        | 无                                          |
| [bert_classifier](https://github.com/HumanSignal/label-studio-ml-backend/tree/master/label_studio_ml/examples/bert_classifier)                               | 使用 [Huggingface](https://huggingface.co/transformers/v3.0.2/model_doc/auto.html#automodelforsequenceclassification) 进行文本分类 | ✅               | ❌                | ✅        | 无                                          |
| [huggingface_llm](https://github.com/HumanSignal/label-studio-ml-backend/tree/master/label_studio_ml/examples/huggingface_llm)                               | 使用 [Hugging Face](https://huggingface.co/tasks/text-generation) 进行 LLM 推理                                                           | ✅               | ❌                | ❌        | 无                                          |
| [huggingface_ner](https://github.com/HumanSignal/label-studio-ml-backend/tree/master/label_studio_ml/examples/huggingface_ner)                               | 基于 [Hugging Face](https://huggingface.co/docs/transformers/en/tasks/token_classification) 的 NER                                             | ✅               | ❌                | ✅        | 无                                          |
| [nemo_asr](https://github.com/HumanSignal/label-studio-ml-backend/tree/master/label_studio_ml/examples/nemo_asr)                                             | 基于 [NVIDIA NeMo](https://github.com/NVIDIA/NeMo) 的语音 ASR                                                                               | ✅               | ❌                | ❌        | 无                                          |
| [mmdetection](https://github.com/HumanSignal/label-studio-ml-backend/tree/master/label_studio_ml/examples/mmdetection-3)                                     | 基于 [OpenMMLab](https://github.com/open-mmlab/mmdetection) 的目标检测                                                              | ✅               | ❌                | ❌        | 无                                          |
| [sklearn_text_classifier](https://github.com/HumanSignal/label-studio-ml-backend/tree/master/label_studio_ml/examples/sklearn_text_classifier)               | 使用 [scikit-learn](https://scikit-learn.org/stable/) 进行文本分类                                                                 | ✅               | ❌                | ✅        | 无                                          |
| [interactive_substring_matching](https://github.com/HumanSignal/label-studio-ml-backend/tree/master/label_studio_ml/examples/interactive_substring_matching) | 简单关键词搜索                                                                                                                    | ❌               | ✅                | ❌        | 无                                          |
| [langchain_search_agent](https://github.com/HumanSignal/label-studio-ml-backend/tree/master/label_studio_ml/examples/langchain_search_agent)                 | 使用 Google Search 和 [Langchain](https://langchain.com/) 的 RAG 流水线                                                                   | ✅               | ✅                | ✅        | OPENAI_API_KEY, GOOGLE_CSE_ID, GOOGLE_API_KEY |

## 允许 ML 后端访问 Label Studio 数据

在大多数情况下，您需要设置环境变量以允许 ML 后端访问 Label Studio 中的数据。

Label Studio 任务可以有多种资源文件来源：

* 直接的 `http` 和 `https` 链接。
    示例：`task['data'] = {"image": "http://example.com/photo_1.jpg"}`

* 使用**导入**操作上传到 Label Studio 的文件。
    示例：`task['data'] = {"image": "https://ls-instance/data/upload/42/photo_1.jpg"}`

* 通过[本地存储连接](storage_local)添加的文件。
    示例：`task['data'] = {"image": "https://ls-instance/data/local-files/?d=folder/photo_1.jpg"}`

* 通过[云存储](storage)（S3、GCS、Azure）连接添加的文件。
    示例：`task['data'] = {"image": "s3://bucket/prefix/photo_1.jpg"}`

当 Label Studio 在 ML 后端上调用 `predict(tasks)` 方法时，它会发送包含数据子字典的任务，其中包含资源文件的链接。

从直接的 `http` 和 `https` 链接（上述第一个示例）下载文件很简单。然而，其他三种类型（导入的文件、本地存储文件和云存储文件）则更复杂。

为解决此问题，ML 后端使用 `label_studio_tools` 包中的 `get_local_path(url, task_id)` 函数（此包随 `label-studio-ml-backend` 预安装）：

```python
from label_studio_tools.core.utils.io import get_local_path

class MLBackend(LabelStudioMLBase)
  def predict(tasks):
    task = tasks[0]
    local_path = get_local_path(task['data']['image'], task_id=task['id'])
    with open(local_path, 'r') as f:
      f.read()
```

`get_local_path()` 函数将 URI 解析为 URL，然后下载并缓存文件。

为此，您必须在 ML 后端使用 `get_local_path` 之前为其指定 `LABEL_STUDIO_URL` 和 `LABEL_STUDIO_API_KEY` 环境变量。如果您使用 docker-compose.yml，这些变量需要添加到 environment 部分。例如：

```yaml
services:
  ml-backend-1:
    container_name: ml-backend-1
    ...
    environment:
      # 指定 Label Studio URL 和 API 密钥以访问
      # 上传的文件、本地存储文件和云存储文件。
      # 不要使用 'localhost'，因为它在 Docker 容器内无效。
      # URL 始终使用 'http://' 或 'https://' 前缀。
      # 使用 'ifconfig'（Linux/Mac）或 'ipconfig'（Windows）确定实际 IP。
      - LABEL_STUDIO_URL=http://192.168.42.42:8080/  # 替换为您的 IP！
      - LABEL_STUDIO_API_KEY=<your-label-studio-api-key>
```

请注意以下事项：

* `LABEL_STUDIO_URL` 必须能够从 ML 后端实例访问。

* 如果您在 Docker 中运行 ML 后端，`LABEL_STUDIO_URL` 不能包含 `localhost` 或 `0.0.0.0`。请使用完整 IP 地址，例如 `192.168.42.42`。您可以使用 `ifconfig`（Unix）或 `ipconfig`（Windows）命令获取此地址。

* `LABEL_STUDIO_URL` 必须以 `http://` 或 `https://` 开头。

要查找您的 `LABEL_STUDIO_API_KEY`，请打开 Label Studio 并转到您的[用户账户页面](user_account#Access-token)。

<div class="enterprise-only">

请注意，您的用户还必须有权访问您连接 ML 后端的项目。

</div>

## 模型训练

训练模型使其能够从提交的注释中学习，并可能改进其对后续任务的预测。

将模型作为机器学习后端连接到 Label Studio 并注释至少一个任务后，您就可以开始训练模型。您可以使用自动训练或手动训练。

<div class="enterprise-only">

在项目设置下的[**模型**页面](project_settings_lse#Model)中，从已连接模型旁的溢出菜单中选择**开始训练**。这将手动启动训练。如果您想控制模型训练的时间，例如在收集到特定数量的注释后或在特定时间间隔后进行训练，请使用此操作。

</div>

<div class="opensource-only">

在项目设置下的[**模型**页面](project_settings#Model)中，从已连接模型旁的溢出菜单中选择**开始训练**。这将手动启动训练。如果您想控制模型训练的时间，例如在收集到特定数量的注释后或在特定时间间隔后进行训练，请使用此操作。

</div>

您也可以通过编程方式启动训练，使用以下方式：

* 通过 API，指定机器学习后端的 ID 并运行以下命令：
   ```
   curl -X POST http://localhost:8080/api/ml/{id}/train
   ```
  更多信息请参阅[训练 API 文档](/api/#operation/api_ml_train_create)。
- [使用 Webhook 触发训练](ml_create#Trigger-training-with-webhooks)。

训练日志显示在 stdout 和控制台中。

要查看更详细的日志，请使用 `--debug` 选项启动 ML 后端服务器。

## 预注释/预测

!!! note
    "预测（predictions）"和"预注释（pre-annotations）"这两个术语可互换使用。


### 从模型获取预测

将模型连接到 Label Studio 后，如果模型已预训练，或刚完成[训练](#模型训练)后，您可以在标注界面中看到模型预测。

* 要手动添加预测，请转到数据管理器，选择要获取预测的任务，然后选择**操作 > 获取预测**。
* 要自动用预测预标注数据，请转到项目设置，启用**注释 > 使用预测预标注任务**，并确保从**选择要使用的预测或模型**下拉菜单中选择了适当的模型。


!!! note
    对于大型数据集，获取预测的 HTTP 请求可能会因超时而中断。如果您想从已连接的机器学习后端**获取所有任务的所有预测**，请为每个任务向 Label Studio API 的预测端点发送 [POST 调用](/api/#operation/api_predictions_create)，以提示机器学习后端为这些任务创建预测。

如果您想**仅使用 ML 后端**手动获取任务列表的预测，请向您的 ML 后端的 `/predict` URL 发送 POST 请求，负载为您想要查看预测的任务，格式如下所示：

```json
{
  "tasks": [
    {"data": {"text":"some text"}}
  ]
}
```

### 交互式预注释

使用交互式预注释的 ML 辅助标注适用于使用矩形、椭圆、多边形、画笔遮罩和关键点的图像分割和目标检测任务，以及 HTML 和文本命名实体识别任务。您的 ML 后端必须支持您正在执行的标注类型，能够识别您创建的输入，并能够返回预测的相关输出。

可以在添加模型时启用**交互式预注释**选项，也可以使用项目设置页面中的**编辑**操作来启用/禁用此选项。

#### 智能工具

智能工具复制您通过标注配置设置的工具（例如矩形），以便与 ML 后端进行交互。智能工具目前可用于 [`Rectangle`](/tags/rectangle)、[`Ellipse`](/tags/ellipse)、[`Polygon`](/tags/polygon)、[`KeyPoint`](/tags/keypoint) 和 [`Brush`](/tags/brush) 标签。

智能工具是动态的，可以根据上下文利用机器学习预测来改变行为或外观。例如，智能工具可以自动检测并建议图像中对象的注释，标注人员随后可以审查和完善。这可以显著加快标注过程，尤其是在处理大型数据集时。

智能工具使用 `context`（上下文），根据标注环境的当前状态和当前任务的特定需求调整其行为。例如，它们可能会根据正在标注的数据类型或任务中特定的感兴趣区域来改变其功能。当为标注任务启用智能工具时，它们可以通过向 `/predict` 端点发送数据以接收预测，从而与 ML 后端进行交互。这些预测随后用于在标注界面内提供交互式预注释。有关更多信息，请参阅[在您的 ML 后端中支持交互式预注释](ml_create#Support-interactive-pre-annotations-in-your-ML-backend)。

例如，当标注人员开始标注图像时，智能工具可以将图像发送到 ML 后端，ML 后端处理图像并返回建议的注释（如边界框或分割遮罩），标注人员可以接受、拒绝或完善这些注释。此过程通过基于模型当前对数据的理解提供注释的起点，帮助简化标注工作流。

如何使用智能工具：

* 如果在标注界面中启用了**自动注释**，智能工具会默认显示。
* 您也可以在标注配置中为您正在执行的标注类型添加 `smart="true"` 选项。
* 如果您*仅*希望智能选项出现，完全不想进行手动标注，请使用 `smartOnly="true"`。

例如：

```<Brush name="brush" toName="img" smart="true" showInline="true"/>```

开始标注后，启用**自动注释**以查看并使用智能选项来绘制形状、遮罩或分配关键点。

对于图像标注，启用自动注释后您可以选择是否**自动接受注释建议**。如果自动接受注释建议，区域会自动显示并立即创建。如果不自动接受建议，区域会出现，但您可以手动拒绝或批准它们，可以单独处理也可以一次性处理。

<br/><img src="/images/predict-owl-region.gif" alt="" class="gif-border" width="800px" height="533px" />

### 删除预测

如果您想从 Label Studio 中删除所有预测，可以使用数据管理器或 API：
- 对于特定项目，选择要删除预测的任务，然后从下拉菜单中选择**删除预测**。
- 使用 API，从命令行运行以下命令，删除特定项目 ID 的预测：
```
curl -H 'Authorization: Token <user-token-from-account-page>' -X POST \
 "<host>/api/dm/actions?id=delete_tasks_predictions&project=<id>"
```

### 选择向标注人员显示哪些预测

<div class="opensource-only">

您可以选择默认向标注人员显示哪个模型或预测集。此项在项目设置的**注释 > 实时预测**下可用。

</div>

<div class="enterprise-only">

您可以选择默认向标注人员显示哪个模型或预测集。此项在项目设置的**注释 > 实时预测**下可用。

</div>

使用下拉菜单选择在标注工作流中使用哪个模型或预测。
