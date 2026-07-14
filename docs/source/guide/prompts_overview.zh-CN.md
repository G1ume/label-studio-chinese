---
title: Prompts 概述与使用场景
short: 概述与使用场景
tier: enterprise
type: guide
order: 0
order_enterprise: 225
meta_title: Prompts 概述
meta_description: HumanSignal 平台 Prompts 功能概述
section: Prompts
date: 2024-05-15 14:30:14
---

使用 Prompts 来评估和优化您的 LLM 提示词，然后生成预测以自动化您的标注流程。

您只需要一个 LLM 部署 API 密钥和一个项目即可开始使用。

借助 Prompts，您可以：

* 大幅提高标注的速度和效率，将领域专家（SME）转变为高效的数据科学家，同时减少对非专业人工标注员的依赖。
* 提高标注吞吐量、准确性和一致性，使流程更快、更具可扩展性。
* 赋能用户充分发挥 AI 驱动文本标注的潜力，为数据标注树立效率和创新的新标准。
* 利用领域专业知识快速引导项目获取标签，从而缩短 ML 开发时间。
* 让您的领域专家专注于更高级别的任务，而不是陷入重复性的人工工作中。

## 功能、需求与限制

<div class="noheader rowheader">

| 功能 | 支持情况 |
| --- | --- |
| **支持的数据类型** | 文本<br>图像<br><br>**注意：** 图像仅支持通过云存储上传。 |
| **支持的对象标签** | `Text`<br>`HyperText`<br>`Image` |
| **支持的控件标签** | `Choices`（文本和图像）<br>`Labels`（文本）<br>`TextArea`（文本和图像）<br>`Pairwise`（文本和图像）<br>`Number`（文本和图像）<br>`Rating`（文本和图像） |
| **支持的基础模型** | 参见[支持的基础模型](prompts_keys#Supported-base-models) |
| **分类选择** | 多选（LLM 可以为每个任务应用多个标签）|
| **文本兼容性** | 任务文本必须兼容 utf-8 |
| **任务大小** | 每个任务的总大小不能超过 1MB（约 200-500 页文本） |
| **网络访问** | 如果您使用防火墙或限制对 OpenAI 模型的网络访问，需要允许以下 IP：<br>3.219.3.197<br>34.237.73.3<br>4.216.17.242 |
| **所需权限** | **Owner、Administrator、Manager** -- 可以创建 Prompt 模型并使用自动标注更新项目。Manager 只能将模型应用于自己已经是成员的项目。<br><br>**Reviewer 和 Annotator** -- 无法访问 Prompts 工具，但可以在项目内看到由提示词生成的预测结果（取决于您的[项目设置](project_settings_lse)）。 |
| **ML 后端支持** | Prompts 不应与已连接 ML 后端的项目一起使用，因为这可能影响某些评估指标的计算方式。 |
| **Enterprise 与 Open Source** | Label Studio Enterprise<br />Starter Cloud |

</div>

!!! note
    有关在本地部署环境中安装 Prompts 的信息，请参见[安装 Prompts](install_prompts)。

## 使用场景

### 使用 Prompts 进行自动标注

Prompts 允许您利用 LLM 快速生成准确的预测，实现即时标注数千个任务。

通过利用 AI 处理大部分标注工作，您可以显著提高数据标注工作流的效率和速度。这对于需要一致且准确标注的大规模数据集尤其有价值。自动化这一过程减少了对人工标注员的依赖，不仅降低了劳动力成本，还最大限度地减少了人为错误和偏见。借助 AI 从提供的基准真相标注中学习的能力，您可以在整个数据集中保持高水平的准确性和一致性，确保为训练机器学习模型提供高质量的标注数据。

#### 工作流

1. 如果您还没有项目，请创建一个项目并导入基于文本的数据集。

    * [创建项目](setup_project)
    * [从外部存储同步数据](storage)
2. 标注一部分任务，尽可能多地将标注标记为基准真相。您拥有的数据越多，对提示词评估就越有信心。

    如果您想跳过此步骤，请参见下文[使用 Prompts 引导项目](#使用-prompts-引导项目)的使用场景。

    * [标注指南](labeling)
    * [为项目定义基准真相标注](quality#Define-ground-truth-annotations-for-a-project)
    * [博客 - 什么是基准真相数据集？](https://humansignal.com/blog/what-s-a-ground-truth-dataset/)
3. 前往 Prompts 页面创建一个新的 Prompt。如果您还没有添加 API 密钥，也需要添加一个以连接到您的模型。

    * [创建 Prompt](prompts_create)
    * [模型提供商密钥](prompts_keys)
4. 编写提示词并对照基准真相数据集进行评估。

    * [草拟提示词](prompts_draft)
5. 当您的提示词返回可接受的整体准确率时，可以选择将其应用到项目中的其余任务。

    * [从提示词生成预测](prompts_predictions)

![自动标注工作流程图](/images/prompts/prompter-diagram.png)

### 使用 Prompts 引导项目

在此使用场景中，您不需要基准真相标注集。您可以使用 Prompts 为任务生成预测，而无需为生成的预测返回准确率分数。

此使用场景非常适合希望启动新项目而无需承担创建大量基准真相标注初期负担的组织，使您能够立即开始分析和利用数据。这对于时间紧迫或资源有限的项目尤其有益。

通过生成预测并将其转换为标注，您还可以快速构建一个标注数据集，随后可以在领域专家的帮助下进行修改和完善。这种方法加速了项目启动阶段，实现更快的实验和迭代。

此外，此工作流为持续扩展数据集提供了可扩展的解决方案，确保新数据能够随着项目的发展被高效地集成和标注。

!!! note
    即使您已经有基准真相标注，仍然可以遵循此使用场景。您将可以选择不将基准真相数据纳入考虑的任务样本集。


#### 工作流

1. 如果您还没有项目，请创建一个项目并导入基于文本的数据集。

    * [创建项目](setup_project)
    * [从外部存储同步数据](storage)
2. 前往 Prompts 页面创建一个新的 Prompt。如果您还没有添加 API 密钥，也需要添加一个以连接到您的模型。

    * [创建 Prompt](prompts_create)
    * [模型提供商密钥](prompts_keys)
3. 编写提示词并针对您的任务样本运行。
    * [草拟提示词](prompts_draft)

当您运行提示词时，会为所选样本创建预测（可以是部分项目任务或全部任务）。从这里您有几个选择：

* 继续优化您的提示词，每次对样本运行时生成新的预测。
* 返回项目并开始审核您的预测。如果您将预测转换为标注，可以让领域专家和标注员开始与这些标注交互。
* 在审核标注时，您可以标识基准真相。有了基准真相数据集，您可以使用准确率分数进一步优化您的提示词。

![引导项目工作流程图](/images/prompts/boostrap-diagram.png)

### 提示词评估与微调

当您对照基准真相标注评估提示词时，每个版本的提示词都会获得一个准确率分数。您可以使用此分数来迭代优化提示词版本的[清晰度、具体性和上下文](prompts_draft#Drafting-effective-prompts)。

![准确率分数截图](/images/prompts/accuracy_score.png)

此准确率分数提供了一种可衡量的方式来评估和优化提示词的性能。通过跟踪准确率，您可以确保 LLM 生成的自动标注与基准真相数据一致。

此反馈循环允许您迭代微调提示词，优化预测的准确性并提高数据标注流程的整体可靠性。在数据准确性直接影响决策和运营效率的行业中，这一能力非常宝贵。

#### 工作流

1. 如果您还没有项目，请创建一个项目并导入基于文本的数据集。

    * [创建项目](setup_project)
    * [从外部存储同步数据](storage)
2. 标注一部分任务，尽可能多地将标注标记为基准真相。您拥有的数据越多，对提示词评估就越有信心。

    * [标注指南](labeling)
    * [为项目定义基准真相标注](quality#Define-ground-truth-annotations-for-a-project)
    * [博客 - 什么是基准真相数据集？](https://humansignal.com/blog/what-s-a-ground-truth-dataset/)
3. 前往 Prompts 页面创建一个新的 Prompt。如果您还没有添加 API 密钥，也需要添加一个以连接到您的模型。

    * [创建 Prompt](prompts_create)
    * [模型提供商密钥](prompts_keys)
4. 编写提示词并对照基准真相数据集进行评估。

    * [草拟提示词](prompts_draft)
5. 继续迭代和优化您的提示词，直到达到可接受的准确率分数。

![微调工作流程图](/images/prompts/tuning-diagram.png)
