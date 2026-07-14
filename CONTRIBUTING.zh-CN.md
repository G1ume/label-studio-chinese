# Label Studio 贡献者指南

您是否正在寻找开始为 Label Studio 做贡献的方法？本指南将帮助您了解如何为项目做出贡献，包括您可以做出的贡献类型、每种贡献类型的标准、Label Studio 项目的整体组织架构以及贡献流程。

## 贡献类型

虽然为开源软件贡献代码是最常见的方式之一，但参与 Label Studio 的社区开发和维护还有许多其他途径。除了代码 Pull Request 之外，您还可以通过错误报告、文档修复、功能请求、标注模板、存储后端和机器学习示例来做出贡献。您也可以通过加入 Label Studio 社区 Slack，与其他社区成员互动并回答问题来参与其中。再小的贡献都弥足珍贵！

### 文档更新

为 Label Studio 做贡献最简单的方式之一就是通过文档更新。文档是新用户接触 Label Studio 的首批途径之一，应该帮助引导用户完成使用 Label Studio 的整个过程。帮助撰写清晰、正确的文档可以对整个用户社区的体验产生持久的影响。

除了文档本身的更改之外，文档更新还应在 Pull Request 中描述所涉及的文档问题，以及该 Pull Request 如何解决该问题。

请为您的 Pull Request 使用文档更新模板（Docs Update），并将 Pull Request 标题前缀设置为 `docs:`。

### 错误报告

错误报告有助于识别开发团队在测试中可能遗漏的问题，或者影响用户体验的边缘情况。一个好的错误报告不仅能提醒开发团队注意问题，还能提供复现、验证和修复该错误所需的条件。

在填写错误报告时，请尽可能多地包含以下信息。如果开发团队无法复现您的错误，他们就无法采取必要的步骤来修复它。

错误报告可能进入几种不同的状态，包括：

- **verified（已验证）**：该错误报告已通过验证，已进入开发流程中等待修复
- **not a bug（非错误）**：该报告描述的并非错误，可能是预期行为或平台配置错误导致的
- **needs information（需要更多信息）**：开发团队无法验证该错误，需要更多信息才能采取行动
- **fixed（已修复）**：该错误报告描述的错误已在 Label Studio 的最新版本中修复

当错误报告进入"fixed"或"not a bug"状态时，对应的 issue 将被关闭。

请为您的 issue 使用错误报告模板（Bug Report）。

### 错误修复

错误修复建立在错误报告的基础上，提供解决该问题的代码。在提交错误修复之前，请先提交一份错误报告，为开发团队提供必要的上下文。错误修复应遵循 Label Studio 的编码标准并包含测试。单元测试对于证明错误已被修复以及提供防止未来回归的安全保障是必要的。除了单元测试之外，您还应提供验收标准，供 QA 团队验证应用程序的行为。错误修复必须引用原始的错误报告。

请为您的 Pull Request 使用错误修复模板（Bug Fix），并将 Pull Request 标题前缀设置为 `fix:`。

### 标注模板

Label Studio 最强大的功能之一是其灵活且可配置的标注界面。Label Studio 附带了许多涵盖多种用例的标注界面示例。如果您的工作流程未被默认模板覆盖，您可以提交一个新的界面模板以供收录。

请为您的 Pull Request 使用标注界面模板（Annotation Interface），并将 Pull Request 标题前缀设置为 `feat:`。

### ML 后端示例

[Label Studio ML Backend 仓库](https://github.com/HumanSignal/label-studio-ml-backend) 包含了许多不同的机器学习示例，用户可以基于这些示例构建自己的机器学习系统。请确保您的示例遵循仓库规范，并在 `README.md` 中提供完整的文档，包括硬件要求、安装说明和使用示例。将 Pull Request 标题前缀设置为 `feat:`。

### 导出格式新增或改进

[Label Studio Converter 仓库](https://github.com/HumanSignal/label-studio-converter) 帮助您将标注编码为您喜爱的机器学习库所支持的格式。它既可以通过命令行运行，也可以直接在 Label Studio 内部运行。在提交新的编解码器时，请将 Pull Request 标题前缀设置为 `feat:`。

### 功能请求

您可能会发现 Label Studio 缺少某个功能，而这个功能可以减少您标注工作流程中的摩擦。开发团队希望听到您的反馈，功能请求有助于确定 Label Studio 平台未来工作的优先级。

通过用户故事来具体说明功能如何运作，有助于开发团队和产品团队理解。用户故事遵循标准格式：作为 *[用户]*，我希望 *[一个结果]*，以便 *[一个好处/价值]*。

### 功能实现

功能实现是一项更为宏大的任务，需要与开发团队协调。功能实现包含两个主要部分：产品需求文档（PRD）和功能 Pull Request。

PRD 应作为一个 issue 提交，标题前缀为 `prd:`。它应包含以下部分：

- *问题（Problem）：* 描述您试图通过此功能解决的具体问题。
- *概念验证笔记（Proof of Concept Notes）：* 如果您有可运行的代码，请提供一个分支链接，并重点说明概念验证的工作方式。
- *用户故事（User Stories）：* 功能如何运作的具体示例，遵循以下格式：作为 *[用户]*，我希望 *[一个操作]*，以便 *[一个好处/价值]*。

核心开发和产品团队将审查该功能请求并提供反馈。如果该功能被接受纳入，接下来应提交一个 Pull Request。功能 Pull Request 应包括：

- 实现该功能所需的代码。
- 证明该功能正常工作的单元测试和集成测试。
- 描述该功能使用方式的文档。
- 提交消息中包含验收标准，描述 QA 应如何测试该功能以确保其按预期工作。
- 提交消息中包含指向 PRD issue 的链接。

将 Pull Request 标题前缀设置为 `feat:`。

### 审查预期

我们可能会要求某些更改（特别是较大的更改，或涉及后端基础设施性能敏感方面的更改）使用功能标志（feature flag）来保护，以确保它们可以安全地逐步推出。在这些情况下，我们将提供功能标志的名称供您在代码中使用 `flag_set` 进行检查，或者我们会直接将功能标志添加到 PR 中。

我们还可能会添加指向我们内部创建的 JIRA 工单的引用，用于跟踪哪些更改包含在我们的不同发布版本中。

## 代码组织

### [Label Studio](https://github.com/humansignal/label-studio)

Label Studio 的主仓库，包含了标注管理的大部分核心逻辑。您可能希望贡献的三个方面包括：

* `label_studio`——这是主应用程序，包含大部分后端代码。
* `web/apps/labelstudio`——这是所有前端元素的中央集成点。
* `web/libs/editor`——这是前端库。它使用 React 构建 UI，并使用 mobx-state-tree 进行状态管理。
* `web/libs/datamanager`——这是 Label Studio Data Manager（我们的数据探索工具）的前端界面。

### [Label Studio SDK](https://github.com/HumanSignal/label-studio-sdk)

用于基于 Label Studio API 构建高级自动化和集成的 Python SDK。

### [Label Studio Converter](https://github.com/HumanSignal/label-studio-converter)

用于在 Label Studio 格式与不同机器学习格式之间进行转换的库。可以独立运行，也可以作为 Label Studio 的扩展运行。

### [Label Studio ML Backend](https://github.com/HumanSignal/label-studio-ml-backend)

用于构建与 Label Studio 的机器学习集成的机器学习后端接口和服务器代码。包含多个示例后端。

## 编码标准

提交代码更改的 Pull Request 时，请遵循以下标准。

- 保持 Pull Request 小而精，每次只针对单个功能或单个错误。
- 字符串使用单引号。
- 使用注释描述代码块。
- 使用语义化的变量命名。
- 优先使用功能聚焦、职责单一的函数。
- 优先采用松耦合设计。
- 遵循 QA 执行的所有代码规范标准。我们使用 [ruff](https://beta.ruff.rs/docs/) 进行代码检查和风格格式化，并正在逐步引入 [mypy](https://github.com/python/mypy) 用于静态类型检查。所有这些最终都会在 CI 上强制执行。请为所有新代码添加类型提示！
- 优先提交较小的补丁。作为粗略的指导，将更改的行数限制在 400 行左右。对于较大的更改，请拆分为多个 PR。

### 测试

- 在贡献错误修复和新功能时，请包含单元测试。单元测试有助于证明您的代码能够正确工作，并能防止未来的破坏性更改。
- 尽可能使用 `tavern` 测试 API 端点。
- 请随您的错误修复和新功能一起提供验收标准。验收标准为 QA 团队提供了关于该更改应如何表现的清晰指导。验收标准的形式为："当用户执行 <操作> 时，应出现 <预期行为>。"
- 提交 Pull Request 后，请验证代码覆盖率测试和 PR 的自动化测试是否通过。
- 对于文档 Pull Request，请验证更改是否在自动生成的预览中正确渲染。
- Label Studio 后端代码应兼容 sqlite 和 postgresql 数据库。我们的自动化测试将在 sqlite 和 postgresql 环境下运行，但请谨慎使用特定数据库后端的功能。

### 其他问题

如果您有任何本指南未涵盖的问题，请在 [Label Studio Slack Community](https://slack.labelstud.io/?source=github-contrib) 的 #contributor 频道中找到我们。
