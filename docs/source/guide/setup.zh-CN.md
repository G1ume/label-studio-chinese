---
title: 配置标注界面
type: guide
tier: all
order: 107
order_enterprise: 107
meta_title: 设置标注配置界面
meta_description: 使用模板或自定义标签组合，为您的机器学习和数据科学项目定制数据标注和注释界面。
section: "创建和管理项目"
---

Label Studio 中的所有标注活动都在项目的上下文中进行。在[创建项目](setup_project.html#Create-a-project)并[导入数据](tasks.html)之后，您需要为项目设置标注界面和标注配置。这一设置过程对您的标注项目至关重要。

!!! error Enterprise
    您的项目需要完全自定义的界面吗？
    Label Studio Enterprise 的 UI 引擎可以创建可编程且可嵌入的 AI 和智能体界面。[了解更多](https://humansignal.com/programmable-ui/)。

## 为您的项目设置标注界面

您可以使用 Label Studio 提供的模板，或通过定义自己的标签组合来配置标注界面，为标注人员设定标签和任务类型。

1. 从[可用模板](/templates)中选择一个模板，或自定义一个。
2. Label Studio 会根据您的数据自动选择需要标注的字段。如有需要，可以修改所选字段。
3. 逐行添加标签名称。
4. （可选）点击标签名称，使用颜色选择器为标签选择新颜色。
5. 配置与标注界面功能相关的其他设置。例如，标注文本时您可以选择**按词选择文本**。
6. 点击**保存**。

<div class="opensource-only">

!!! error enterprise
    Label Studio Enterprise 支持将插件作为标注界面的一部分。插件允许您使用 JavaScript 扩展界面并自定义标注工作流。有关更多信息，请参阅[插件](https://docs.humansignal.com/guide/plugins)。

</div>

<div class="enterprise-only">

!!! info 提示
    您也可以将插件作为标注界面的一部分来实现。插件允许您使用 JavaScript 扩展界面并自定义标注工作流。有关更多信息，请参阅[插件](plugins)。

</div>

### 修改标注界面

您可以在项目设置中修改标注界面和配置。

!!! note
    如果您修改的项目有正在进行中的工作，请注意以下事项：

    * 除非删除使用这些标签的现有注释，否则无法删除标签或更改标注类型。
    * 如果您修改了标注配置，数据管理器中创建的所有标签页都将被移除。

1. 在 Label Studio 中，打开您要修改的项目。
2. 点击**设置**。
3. 点击**标注界面**。
4. 浏览模板、更新可用标签，或使用 `Code` 选项通过[标签](/tags)进一步自定义界面。


#### 代码视图自动补全

代码视图会在您输入时显示自动补全助手。

自动补全包含标签提示以及所选标签的可用参数提示：

![代码自动补全动图演示](/images/label/autocomplete.gif)

输入左尖括号 `<` 后会显示标签建议。在标签内添加空格后会显示参数建议。

要接受建议，您可以点击选择，或在建议高亮时按 Tab 键。

## 自定义模板

您可以自定义[标注配置模板](/templates)，也可以使用[标签](/tags)从头创建自定义配置。如果您创建了一个可能对其他 Label Studio 用户有用的自定义配置，请考虑[将其贡献为模板](https://github.com/HumanSignal/label-studio/tree/develop/label_studio/annotation_templates)。

项目的标注配置是一个 XML 文件，包含三种 Label Studio 特有的标签类型。

| 标签类型 | 适用场景 |
| -------- | -------------------------------------------------------------------------------------- |
| Object   | 指定数据集中的数据类型和输入数据源。 |
| Control  | 配置要执行的标注类型以及标注结果的显示方式。 |
| Visual   | 定义标注的用户界面外观。 |

您可以组合这些标签来为数据集创建自定义标注配置。

<a class="button" href="/tags">查看所有可用标签</a>

### 标注配置示例

例如，要将数据中作为 URL 引用的图像（`$image_url`）分类为 Cat 或 Dog 两个类别之一，请使用以下标注配置示例：

```xml
<View>
  <Image name="image_object" value="$image_url"/>
  <Choices name="image_classes" toName="image_object">
    <Choice value="Cat"/>
    <Choice value="Dog"/>
  </Choices>
</View>
```

此标注配置在 [Image](/tags/image.html) 对象标签中引用图像资源，并在 [Choices](/tags/choices.html) 控制标签中指定可选标签。

如果您想自定义此示例，比如允许标注人员为单张图像同时选择 Cat 和 Dog 标签，可以修改 [Choices](/tags/choices.html) 控制标签的参数：

```xml
<View>
  <Image name="image_object" value="$image_url"/>
  <Choices name="image_classes" toName="image_object" choice="multiple">
    <Choice value="Cat"/>
    <Choice value="Dog"/>
  </Choices>
</View>
```

<div class="opensource-only">

### 从命令行添加标注配置

您可以在 `config.xml` 文件中定义标注配置，并使用该文件初始化 Label Studio 中的特定项目。

```bash
label-studio my_new_project start --label-config config.xml
```

</div>

### 通过 API 添加标注配置

您可以使用服务器 API 配置标注配置。有关更多详细信息，请参阅[后端 API](api.html) 文档。
