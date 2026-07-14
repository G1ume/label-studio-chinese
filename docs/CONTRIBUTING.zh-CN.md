# 参与 Label Studio 文档贡献

感谢您投入时间为我们的项目做贡献！您的任何贡献都将展示在 Label Studio 文档网站上，地址为 https://labelstud.io/guide/，如果您[贡献模板](#贡献模板)，则展示在 https://labelstud.io/templates/。

## 开始之前

我们重视社区每位成员的贡献，并请您遵守我们的[行为准则](https://github.com/HumanSignal/label-studio/blob/develop/CODE_OF_CONDUCT.md)。我们是一个小团队，但会尽力在 2 个工作日内回复 issue 和 Pull Request。

对于您为任何 Label Studio 仓库所做的贡献，请执行以下操作：
- 阅读通用的[贡献者指南](https://github.com/HumanSignal/label-studio/blob/develop/CONTRIBUTING.md)。
- 对于您想要进行的任何重大更改和增强功能，请创建 issue。
- 保持 Pull Request 专注于一个 issue。较短的 Pull Request 更受欢迎，也更容易审查。

## 贡献文档更新

您可能希望为 Label Studio 贡献以下几种类型的文档：
- 小修复：修复错别字或损坏的链接。
- 添加章节：在现有主题中添加新章节。例如，根据您遇到的问题改进 [ML 后端的故障排除指南](https://labelstud.io/guide/ml_troubleshooting.html)。
- 重构：重写某个主题以使其更清晰。例如，改进[创建自定义 Webhook 事件](https://labelstud.io/guide/webhook_create.html)的指南。
- 新增内容：添加新主题，例如[新模板](#贡献模板)或新的 ML 教程。如果您想添加其他内容，请先开启一个 issue 与团队讨论。

## 贡献模板

所有 Label Studio 模板都针对特定的机器学习用例，例如自然语言处理、计算机视觉、对话式 AI 等。

我们欢迎贡献新模板和对现有模板的增强。在做出贡献之前，您可能需要就您提议的模板提交一个功能请求 issue，以便在投入 Pull Request 工作之前进行讨论。

如果您想添加新模板，请确保它适用于某个 ML 用例。如果您的模板是对现有模板的定制，主要影响标注界面的布局，请考虑通过在`增强此模板`部分添加新章节来为现有模板做贡献。

### 贡献新模板

如果您想贡献新模板，请执行以下操作。

将模板添加到文档中：

1. 在 `label-studio/docs/source/templates` 目录中创建一个新文件，文件名使用下划线分隔的模板名称，例如 `new_template.md`。
2. 为模板添加 Front Matter，包括标题、类别、类型、排序和元信息。`type` 必须为 templates，`order` 必须比模板所属类别中最后一个相关模板大 1。例如：
```markdown
---
title: Document Retrieval
type: templates
category: Ranking and Scoring
cat: ranking-and-scoring
order: 502
meta_title: Document Retrieval Data Labeling Template
meta_description: Template for annotating documents for document retrieval tasks with Label Studio for your machine learning and data science projects.
---
```
3. 添加有关模板的信息。包括 1-2 句话介绍模板的机器学习用例以及模板的实际工作原理。例如：
```markdown
如果您想要开始训练文档检索或推荐模型，可能需要开发一个能够识别相似文档的数据集。使用此模板来识别和选择与特定查询或现有文档相关的文档。
```
4. 添加样板信息以允许渲染模板预览：
```markdown
## 交互式模板预览

<div id="main-preview"></div>
```
5. 添加标题 `## 标注配置`，并将完整的模板 XML 放在 ````html```` 标签中。
6. 添加 `## 关于标注配置` 部分，描述模板中的标签。
7. （可选）添加包含替代配置的模板增强部分、相关模板部分或相关标签部分。

添加模板文件后，通过以下操作将其添加到相关的模板库中：
1. 找到您的模板用例对应的模板库文件。所有模板库文件命名为 `gallery_*.html`，文件名的最后部分是用例的缩写。例如，`gallery_cv.html` 是计算机视觉模板库。
2. 在文件末尾为模板创建一个新条目。例如，复制以下内容并更新 `a href` 元素以指向您的新模板文件，添加类别并更新标题：
```html
    <div class="column">
      <a href="/templates/image_ellipse.html">
        <div class="card">
          <div class="image-wrap">
            <div class="image" style="background-image: url(/images/templates-misc/object-detection-ellipses.png)"></div>
          </div>
          <div class="category">object detection, semantic segmentation</div>
          <div class="title">Object Detection with Ellipses</div>
        </div>
      </a>
    </div>
```
如果您想添加图片，请将其放在 `label-studio/docs/themes/htx/source/images/templates-misc/` 目录中，并更新此部分中的链接。

就是这样！您也可以随时提交仅包含部分模板文档的 Pull Request 来获取反馈。

### 贡献对现有模板的增强
如果您修改了现有模板并希望与社区分享您的更改，请为现有模板贡献增强功能。

1. 找到您要更新的模板文档。例如 `label-studio/docs/source/templates/text_summarization.md`。
2. 如果不存在，请在模板底部、`## 相关标签` 部分之前添加标题 `## 增强此模板`。
3. 添加描述您增强功能的新标题。例如，```### 在待摘要文本旁边显示文本框```。
4. 描述对标注配置的增强。例如：
```markdown
如果您想在待摘要文本旁边显示文本框，请执行以下操作：

1. 为标注配置中的 [View](/tags/view.html) 标签添加 flex 显示样式：`<View style="display: flex;">`
2. 添加新的 [View](/tags/view.html) 标签包裹 [Header](/tags/header.html) 和 [Text](/tags/text.html) 样本，使它们显示在左侧。
3. 将 [TextArea](/tags/textarea.html) 和 [Header](/tags/header.html) 标签包裹在具有以下 CSS 样式的 [View](/tags/view.html) 标签中，使它们在右侧整齐显示：
    ```xml
    <View style="width: 50%; padding-right: 2em; margin-left: 2em;">
    ```
您完整的增强标注配置如下所示：
    ```xml
    <View style="display: flex;">
      <View>
        <Header value="Please read the text" />
        <Text name="text" value="$text" />
      </View>
      <View style="width: 50%; padding-right: 2em; margin-left: 2em;">
        <Header value="Provide one sentence summary" />
        <TextArea name="answer" toName="text"
                  showSubmitButton="true" maxSubmissions="1" editable="true"
                  required="true" />
      </View>
    </View>
    ```
```
