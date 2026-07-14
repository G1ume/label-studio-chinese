---
title: 真实标注
short: 真实标注
tier: enterprise
type: guide
order: 0
order_enterprise: 305
meta_title: 真实标注
meta_description: 在 Label Studio Enterprise 中设置真实标注。
section: "审核与质量评估"
date: 2024-09-30 13:57:28
---

"真实标注"（Ground Truth）是一种经过验证的高质量标注，作为特定任务的正确答案。它作为评估其他标注准确性和机器学习模型性能的基准。

Label Studio Enterprise 将标注员的标注和模型预测与任务的真实标注进行比较，计算出一个 0 到 1 之间的准确率分数。

!!! error Enterprise
    真实标注仅在 Label Studio Enterprise Edition 中可用。如果您使用的是 Label Studio Community Edition，请参阅 [Label Studio Features](https://labelstud.io/guide/label_studio_compare.html) 了解更多信息。

## 将标注标记为真实标注

打开任务以查看其标注。点击底部的星形图标将特定标注设置为真实标注：

![真实标注截图](/images/review/ground_truth.png)

!!! note
    一个任务只能有一个标注被设置为该任务的真实标注。如果您将任务的新标注设置为真实标注，该任务之前被设置为真实标注的标注将被更新，不再是真实标注。

## 按用户设置真实标注

要批量更新任务，以确保来自特定标注员的标注应被视为真实标注，首先选择您要更新的任务。

然后选择 **Actions > Set Ground Truths**。然后您可以从标注员列表中进行选择。

![真实标注列截图](/images/review/ground-truth-set.png)

## 查看现有的真实标注

您可以使用数据管理器中的 **Ground Truth** 列来识别哪些任务已有真实标注：

![真实标注列截图](/images/review/ground_truth_dm.png)

您还可以使用过滤器来包含或排除已设置真实标注的任务。

## 在标注间一致性弹出窗口中查看真实标注

将鼠标悬停在 **Agreement** 列上，查看显示标注间一致性的弹出窗口。已标记为真实标注的标注会用星形图标 ⭐️ 表示：

<img src="/images/review/iaa-popover.png" alt="" class="gif-border" style="max-width:705px" />


## 移除真实标注

要移除单个真实标注，只需打开任务并再次点击星形图标取消设置。

要移除多个真实标注，在数据管理器中选中任务旁边的复选框，然后选择 **Actions > Delete Ground Truths**。
