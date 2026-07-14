---
title: 项目组件
short: 项目组件
# type: guide  # 此页面尚未准备好，因此从所有地方移除
# tier: all
order: 118
meta_title: 项目组件
meta_description: "Label Studio 项目组件文档。"
section: "项目与团队管理"
---

本页面描述了 Label Studio UI 中的通用项目组件。

## 标注界面

Label Studio 界面允许您对数据进行标注和注释。只有在设置好项目、标注界面并导入数据后，您才能开始标注和注释数据。有关更多信息，请参阅[标注和注释数据](labeling.html#Start-labeling)页面。

## 标注

标注是对图像上的数据打标签的过程。图像可能包含人类、车辆、任何物体，使其可被机器识别。标注有多种类型，可用于教导机器了解世界中各种物体的存在。有关更多信息，请参阅[标注和注释数据](labeling.html#Start-labeling)页面。

<div class="enterprise-only">

## 审核

在多个标注员完成任务标注后，审核他们的输出以验证结果的质量。有关更多信息，请参阅[在 Label Studio 中审核标注](quality.html)页面。

## 成员

在 Label Studio Enterprise 版本中，您可以将成员添加到特定工作空间，或将成员添加到工作空间内的特定项目。有关更多信息，请参阅[向项目添加成员](setup_project.html#Add-members-to-a-project)页面。

</div>

## 机器学习

使用 Label Studio ML 后端将 Label Studio 与机器学习模型集成。有关更多信息，请参阅[将 Label Studio 集成到您的机器学习流水线](ml.html)页面。

## 云存储

您可以添加源存储连接以将数据从外部源同步到 Label Studio 项目，并添加目标存储连接以将标注从 Label Studio 同步到外部存储。有关更多信息，请参阅[从外部存储同步数据](storage.html)页面。

## Webhook

Label Studio 中的 Webhook 允许您设置集成，以订阅 Label Studio 内发生的某些事件。当事件触发时，Label Studio 会向配置的 Webhook URL 发送 HTTP POST 请求。有关更多信息，请参阅[在 Label Studio 中设置 Webhook](webhooks.html) 页面。
