---
title: Label Studio 术语表
short: 术语表
tier: all
type: guide
order: 16
order_enterprise: 16
meta_title: Label Studio 术语表
meta_description: Label Studio 中常见术语的词汇表。
section: "发现与学习"
date: 2023-10-27 11:38:25
---

下表描述了你在使用 Label Studio 时可能遇到的一些术语：


| 术语 | 描述 |
|--|-----|
| 标注（Annotations） | 标注任务的输出。以前称为"完成项"（completions）。"标注"（annotations）和"标签"（labels）这两个术语经常互换使用。 |
| 边界框（Bounding box） | 图像中的区域。 |
| 数据集（Dataset） | 你导入到 Label Studio 的数据，由单独的项目或标注任务组成。 |
| 标签（Labels） | 在 Label Studio 中标注任务时，你为每个区域添加的内容。 |
| 标注流（Label stream） | 当你从数据管理器中点击 **Label All Tasks**（标注所有任务）时，你正在标注流中工作。 |
| 标注配置（Labeling configuration） | 标注配置决定了标注者和审核员将看到的内容。它在项目设置中进行配置。 |
| 预测/预标注（Predictions / Pre-annotations） | 机器学习模型为未标注数据集创建的 Label Studio 格式的标注。请参阅[导入预标注](predictions.html)。 |
| 关系（Relation） | 两个标注区域之间的已定义关系。 |
| 结果（Result） | 应用于特定区域的标签，存储在标注或预测中。请参阅[已完成标注任务的 Label Studio JSON 格式](export.html#Label-Studio-JSON-format-of-annotated-tasks)。 |
| 快速查看（Quick view） | 当你在数据管理器中点击单个项目以打开它时看到的视图（与在"标注流"中查看不同）。 |
| 记录（Record） | 数据集中的项目。 |
| 区域（Region） | 任务中已标识用于标注的部分。例如，在处理文本时，这可能是一个特定的文本片段或字段。对于图像，区域的一个例子是边界框。对于文本，区域的一个例子是文本片段。通常会有标签分配给它。 |
| 任务（Task） | 当你上传数据到 Label Studio 时，数据集中的每个项目都会成为一个标注*任务*。任务是数据集中一个独立的项目，可以被标注、预标注或已被标注。例如：一段文本片段、一张图片或一个视频片段。 |
| 标签配置（Tags） | 用于自定义标注界面的配置选项。请参阅[更多关于标签的信息](/tags)。 |
| 模板（Templates） | 示例标注配置，你可以使用它们来指定你正在使用的数据集所执行的标注类型。请参阅[所有可用模板](/templates)。 |

