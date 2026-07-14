---
title: 在项目中使用数据管理器
short: 数据管理器
type: guide
tier: all
order: 122
order_enterprise: 122
meta_title: 在项目中使用数据管理器
meta_description: 管理、筛选和排序标注项目中的项目数据。
section: "创建和管理项目"
parent: "manage_projects_lso"
parent_enterprise: "manage_projects" 
---

数据管理器页面是您可以查看所有标注任务、排序和筛选数据、导入和导出数据以及执行各种任务相关操作的地方。

有关设置项目的信息，请参阅[创建和配置项目](setup_project)。

<div class="opensource-only">

![数据管理器截图](/images/project/dm-community.png)

</div>


<div class="enterprise-only">

![数据管理器截图](/images/project/dm-enterprise.png)

</div>

数据管理器中的每一行代表数据集中的一个标注任务。

<div class="enterprise-only">

## 任务状态

每个任务在对它执行操作时会经历一系列状态。有关更多信息，请参阅[项目和任务状态管理](project_states)。

</div>

## 筛选或排序项目数据

借助筛选器和标签页，您可以将数据分成不同的部分，由不同的标注人员进行标注，或者按特定顺序排列数据，以便按预测分数或数据中的其他值进行标注。

在标注之前筛选或排序数据时，您是在修改标注时看到的任务和任务顺序。[任务采样](https://labelstud.io/guide/start#Set-up-task-sampling-for-your-project)会影响整个项目的任务顺序且无法更改，而筛选和排序任务可以随时更改。

<img src="/images/data-manager-filters.png" class="gif-border">

<div class="opensource-only">

!!! error Enterprise
    在 Label Studio Enterprise 和 Starter Cloud 中，您可以使用针对注释结果的高级筛选器。有关更多信息，请参阅我们的 [Enterprise 文档](https://docs.humansignal.com/guide/manage_data#Filter-annotation-results)。

</div>

<div class="enterprise-only">

### 筛选注释结果

您还可以对任务中的单个注释结果进行筛选：

<img src="/images/data-manager-filters-lse.png" class="gif-border" >

!!! note
    在 Label Studio Community 中，`Annotation results` 筛选器是对任务所有注释的非结构化文本搜索，上述示例无法实现。

支持以下标签：
- 所有 `Labels` 标签（例如 `Labels`、`ParagraphLabels`……）
- `Choices`
- `Taxonomy`
- `Rating`

**已知限制：**
- 使用 `apiUrl` 加载的[分类体系](/templates/taxonomy)将不会被检测到。


</div>

### 示例：优先标注新数据
按日期排序项目中的数据，以便优先标注最新数据。

1. 在项目中，将数据的**排序**方式从默认更改为**创建时间**。
2. 将项目排序方式更新为升序，使最新的项目排在最前面。
3. 选择**按显示顺序标注任务**，从最新到最旧开始标注任务。

### 示例：按预测分数排序
如果您上传了带有预测分数的[预注释数据](predictions.html)，或者您的[机器学习后端](ml.html)在模型输出中产生了预测分数，您可以按预测分数排序项目中的数据。

1. 在项目中，将数据的**排序**方式从默认更改为**预测分数**字段。
2. 将项目排序方式更新为升序或降序，以便根据较高置信度或较低置信度的预测进行标注。
3. 选择**按显示顺序标注任务**，按预测分数顺序开始标注任务。
您还可以使用[任务采样](https://labelstud.io/guide/start#Set-up-task-sampling-for-your-project)来使用预测分数排序。

### 示例：使用标签页和筛选器拆分数据集
如果要标注大型数据集，您可能希望使用标签页和筛选器将其拆分为较小的部分，并将不同的标注人员分配到不同的标签页。在 Label Studio Community Edition 中无法将标注人员分配给特定任务，但您可以按特定标注人员命名标签页，作为使用标签页基本分配任务的一种方式。

例如，您可以将包含 300 张图像的数据集拆分为 3 个不同的标签页，让不同的标注人员专注于每个标签页：
1. 在项目中，创建一个筛选器，条件是 **ID** 字段**介于**值 "1" 和 "100" 之间。点击筛选器外部以查看标签页中筛选出的项目。
2. 点击标签页的垂直省略号，选择**重命名**。以您希望专注于该标签页项目的特定标注人员的名字命名。
3. 点击 **+** 图标创建新标签页。点击新标签页的垂直省略号，选择**重命名**，以第二位标注人员的名字命名。
4. 在新标签页上，创建一个筛选器，条件是 **ID** 字段**介于**值 "101" 和 "200" 之间。点击筛选器外部以查看标签页中筛选出的项目。
5. 点击 **+** 图标创建新标签页。点击新标签页的垂直省略号，选择**重命名**，以第三位标注人员的名字命名。
6. 在新标签页上，创建一个筛选器，条件是 **ID** 字段**介于**值 "201" 和 "300" 之间。点击筛选器外部以查看标签页上筛选出的项目。
7. 任何标注人员都可以登录并导航到其工作相关的标签页，点击**标注**按钮开始标注其标签页上的任务子集。

## 将筛选器保存为标签页

您可以在数据管理器上创建标签页，以便[拆分数据集](#示例使用标签页和筛选器拆分数据集)进行标注、按状态（已注释、已预测、未标注）分离任务，或出于其他原因。

您创建的标签页取决于您的[标注配置设置](setup.html)，因为标注配置定义了可用于筛选和排序的数据字段。

!!! note
    如果您更改了标注配置，数据管理器中的所有标签页都会被删除。请确保在数据管理器中设置复杂标签页之前完成项目设置。

<div class="enterprise-only">

## 向标注人员分配任务

在 Label Studio Enterprise 中，如果您是管理员或项目经理，可以将标注人员分配给特定项目中的任务。在[将标注人员添加到项目或工作区](setup_project.html#Add-members-to-a-project)之后，将标注人员分配到特定任务。如果您使用[手动任务分发](setup_project.html#Set-up-task-distribution-for-labeling)，则必须向标注人员分配任务。

1. 对于特定项目，在数据管理器上选择任务。
2. 使用下拉菜单选择**分配标注人员**。
3. 选择标注人员的姓名，点击 `>` 箭头将其分配给选定的任务。
4. 点击**分配**。

如果要批量分配任务，可以使用筛选器精确识别要分配的任务，然后选中数据管理器顶部的复选框以选择所有显示的任务，并将其分配给标注人员。

</div>


## 删除任务或注释
如果您有重复任务，或想删除注释，可以从 Label Studio 中删除任务和注释。

1. 在 Label Studio UI 中，打开要更新的项目。
2. 筛选数据管理器页面，仅显示要删除的数据。例如，特定注释，或由特定标注人员注释的任务。
3. 勾选要删除的任务或注释旁的复选框。
4. 使用带有任务数量的下拉菜单，选择**删除任务**或**删除注释**。
5. 点击**确定**确认您的操作。

如果要更改标注界面或执行不同类型的数据标注，请先选择数据集的所有注释并将其删除。

<div class="enterprise-only">

## 一致性列

数据管理器中的一致性列反映了标注人员之间对某个任务的一致性。有关一致性及其计算方式的更多信息，请参阅[任务一致性](stats)。

您将在数据管理器中看到以下一致性列：

* **Agreement（一致性）** —— 这是任务的整体一致性。

    计算方式是特定任务所有控制标签之间的一致性分数均值。请参阅[整体一致性](stats#Overall-agreement)。
* **[控制标签] 一致性** —— 每个控制标签都有自己的一致性分数。

    控制标签一致性的计算方式取决于项目的设置方式。请参阅[每个控制标签的一致性](stats#Per-control-tag-agreement)。

![截图](/images/review/agreement-dm.png)

### 标注人员和模型

点击任何一致性列以选择要用于一致性计算的特定标注人员和模型。

![截图](/images/review/agreement-dm-modal.png)

默认情况下，所有标注人员（不包括模型）被选中用于一致性计算。

但是，您可以自定义选择标注人员的子集、模型，或同时选择模型和标注人员进行比较。

例如，如果您有 10 位标注人员而您选择了 3 位，则整体一致性分数和控制标签一致性分数将根据您选择的人员重新计算。

!!! note
    您必须至少选择两个项目进行比较。

    您的选择将应用于数据管理器中的所有一致性列。您不能为不同的一致性列选择不同的标注人员和模型。


### Ground Truth 匹配

如果您的项目包含 Ground Truth（基准真值）注释，您可以使用此选项将单个标注人员或单个模型与 Ground Truth 注释进行比较。

Label Studio 将对您的项目应用已配置的一致性指标和方法，但会将计算仅限于选定的标注人员或模型以及标记为 Ground Truth 的注释。

<img src="/images/review/agreement-dm-gt.png" class="gif-border" style="max-width:679px">

### 一致性弹出窗口

点击任何一致性列可看到一个弹出窗口，其中包含所使用的指标和方法的信息。

如果您使用**成对比较（Pairwise）**方法，您将看到所选标注人员和模型的一致性分数明细。

<img src="/images/review/agreement-dm-popover.png" class="gif-border" style="max-width:600px">


</div>
