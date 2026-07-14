---
title: 本地开发界面
short: 本地开发
tier: enterprise
type: guide
order: 0
order_enterprise: 143
meta_title: 界面
meta_description: "使用 Label Studio SDK CLI 在终端中构建和迭代自定义标注界面"
section: "Interfaces"
---

您可以在自己的编辑器和编程 Agent（Claude、Codex 或 Cursor）中构建或迭代界面，而无需使用产品内置的 Agent。**这是任何部署方式下的一流选项（许多团队更偏好此方式），也是在自托管或本地部署（包括离线环境）中构建界面的方式，因为这些环境中无法使用"使用 Agent 创建"功能（该功能为 Label Studio Cloud 专属功能）。**

您可以在本地磁盘上编写 JSX，对照您的 Label Studio 实例实时预览，并在准备好发布时将结果同步回去。

在以下场景中，本地开发非常有用：

- 使用您已配置好的编程 Agent 或 IDE。
- 针对已检出的界面副本离线工作，然后将更改推送回去。
- 将现有的 React 原型引入 Label Studio。
- 在发布之前对界面运行自动验证和 Playwright 场景测试。

<iframe width="560" height="315" src="https://www.youtube.com/embed/-xOjbN4PAhk?si=CUtfpYCxYQc0lRmx" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

## 1. 安装 label-studio-sdk 包并检查您的设置

在开始之前，请确保您具备以下条件：

- **已安装 [`label-studio-sdk` Python 包](https://github.com/HumanSignal/label-studio-sdk)。** 如果已安装，请确保版本为 2.0.22 或更高。
   ```bash
   pip install --upgrade label-studio-sdk
   # 或
   poetry add label-studio-sdk
   ```
- Node.js 和 npm 已配置在您的 `PATH` 中。
- 您的 Label Studio Enterprise URL 和 [API 令牌](access_tokens)。
- 已安装编程 Agent（Claude、Codex 或 Cursor），如果您需要 Agent 辅助的话。

首先，在主菜单中选择 **界面（Interfaces）**，然后选择 **创建界面 > 本地开发（Create Interface > Develop Locally）**。

!!! info 提示
    要迭代 Label Studio 中已存在的界面，请打开界面详情页，点击顶部栏中的 **本地开发（Develop Locally）**。该对话框会遵循[版本导航器](interfaces-details#Versions)中当前选择的版本。

## 2. 安装 create-interface 技能

为您的本地编程 Agent 安装 [`create-interface-skill`](/skills/interface.html) 技能。该技能包含了本地开发界面所需的命令和规范，让您的 Agent 能够编写出有效的界面模块。

在 **本地开发（Develop Locally）** 模态框中，选择您的 Agent 并复制安装命令：

| Agent | 安装命令 |
| --- | --- |
| **Claude** | `npx skills add humansignal/create-interface-skill --skill create-interface-skill -g -a claude-code` |
| **Codex** | `npx skills add humansignal/create-interface-skill --skill create-interface-skill -g -a codex` |
| **Cursor** | `npx skills add humansignal/create-interface-skill --skill create-interface-skill -g -a cursor` |

在终端中运行该命令，然后重启您的 Agent 以使其加载新技能。每个 Agent 只需要安装一次该技能。

!!! info 提示
    如果您更愿意手动编写界面，可以跳过此步骤。该技能是可选的——它只是为您的 Agent 提供上下文。

## 3. 初始化界面

使用 SDK 提供的命令在本地目录中初始化界面：

```bash
export LABEL_STUDIO_URL="https://your-instance.humansignal.com"
export LABEL_STUDIO_API_KEY="<your token>"
label-studio-sdk interface init ./my-interface
cd ./my-interface
label-studio-sdk interface preview .
```

`interface init` 会搭建一个初始的 `Screen.jsx`、`task.json` 和 `scenarios.js`，为您提供一个可用的基线来开始构建。

!!! info 提示
    您也可以将 `--lse-url` 和 `--token` 传递给各个命令，而不是导出环境变量。如果您需要对接多个 Label Studio 实例，这会很有用。

#### 从现有界面开始

要迭代 Label Studio 中已存在的界面，请打开界面详情页，点击顶部栏中的 **本地开发（Develop Locally）**。该对话框会遵循[版本导航器](interfaces-details#Versions)中当前选择的版本。

或者，您可以使用以下命令：

```bash
label-studio-sdk interface pull --id <interface-id> --version <version-id> ./my-interface
```

对话框会为您预填 `--id` 和 `--version`。如果不指定 `--version`，`pull` 会获取界面的最新版本，包括任何未发布的本地草稿。

## 4. 检查本地设置

运行以下命令检查您的本地设置：

```bash
label-studio-sdk interface doctor
```

首次运行验证或 doctor 命令时，会将 Node 验证器依赖安装到用户缓存目录中。

## 5. 启动实时预览

进入您的界面目录并运行预览命令：

```bash
cd ./my-interface
label-studio-sdk interface preview .
```

这会在 Label Studio 中打开一个 **playground**（位于 `interfaces/playground` URL），该 playground 与您的本地环境实时连接。playground 会监视您的源文件——每次保存都会实时反映您在预览中的最新更改。

例如，如果您的 Label Studio 实例位于 `https://your-instance.humansignal.com`，则 playground 将位于 `https://your-instance.humansignal.com/interfaces/playground`。

## 6. 在本地环境中开发

在 playground 运行的同时，您可以在熟悉的本地工具中工作——您的 IDE、终端或本机上的编程 Agent——而无需在产品内编辑。像处理其他项目一样保存更改，playground 会根据当前示例任务数据重新渲染。

## 7. 验证界面

在同步更改之前，请验证界面以发现潜在问题：

```bash
label-studio-sdk interface validate .
```

静态验证会检查 JSX 编译、最终模块结构、schema 导出，并对 `getResults` 和 `parseResults` 运行冒烟测试。要同时运行您在 `scenarios.js` 中定义的浏览器驱动的 Playwright 场景，请添加 `--scenario`：

```bash
label-studio-sdk interface validate . --scenario scenarios.js
```

验证也会作为 `sync` 的一部分运行，因此如果您更愿意在同步时发现问题，可以跳过此步骤。在 `sync` 上使用 `--no-validate` 可以完全跳过此检查。

## 8. 将更改同步回 Label Studio

本地代码准备就绪后，运行 sync 命令将本地目录与 Label Studio 实例同步：

| 模式 | 同步命令 |
| --- | --- |
| **新建界面** | `label-studio-sdk interface sync . --title "My Interface" --workspace <id> --publish` |
| **现有界面** | `label-studio-sdk interface sync . --message "Describe the change"` |

请注意以下几点：

* 默认情况下，`sync` 会创建一个**未发布的本地草稿**，以便您在 Label Studio 中审查该版本后再发布。

* 添加 `--publish` 可立即发布版本并使其可用于新项目。从头开始创建新界面时，"本地开发"对话框会自动包含 `--publish`；在迭代现有界面时则会省略。

* 对于新界面，`--workspace` 标志是可选的。如果不指定工作区，界面将创建在[**共享（Shared）** 范围](interfaces#Interface-scope)内（可被任何工作区中的项目使用）。

`sync` 的常用标志：

| 标志 | 描述 |
| --- | --- |
| `--publish` | 立即发布版本，而不是保留为草稿。 |
| `--message "..."` | 将历史消息与同步的版本一起存储。 |
| `--dry-run` | 打印计划的操作而不写入服务器。 |
| `--workspace <id>` / `--workspace-title "..."` | 将新创建的界面放入特定工作区。 |
| `--force` | 即使本地源哈希未更改也上传。 |
| `--no-validate` | 跳过验证关卡（仍需编译）。 |

成功同步后，CLI 会在源文件旁边写入一个附属文件（`Screen.jsx.ls-interface.json`）。

该附属文件以 Label Studio 基础 URL 为键，记录了界面 ID、工作区、源版本和上次推送的源哈希，因此后续的 `sync`、`start` 和 `open` 命令无需指定 `--id`。

!!! info 提示
    同步后，在 **本地开发（Develop Locally）** 模态框中点击 **刷新版本（Refresh Versions）**，即可在界面详情页上看到新的草稿或已发布版本。

## 常用 CLI 命令

完整的命令参考请参阅 [**界面 CLI 指南**](https://github.com/HumanSignal/label-studio-sdk/blob/master/interface-cli.md)。本地开发时最有用的命令如下：

| 命令 | 描述 |
| --- | --- |
| `interface init <dir>` | 搭建初始界面（`Screen.jsx`、`task.json`、`scenarios.js`）。 |
| `interface pull --id <id> <dir>` | 将现有界面下载到本地目录。未设置 `--version` 时默认获取最新版本（包括草稿）。 |
| `interface preview <dir>` | 打开实时 playground 并监视源文件更改。 |
| `interface validate <dir>` | 运行静态验证和（可选的）Playwright 场景。 |
| `interface sync <dir>` | 将本地更改推送回 Label Studio。默认创建草稿；添加 `--publish` 以发布。 |
| `interface start <dir>` | 同步界面，创建一个使用该界面的项目，并打开项目的数据标签页。 |
| `interface open <dir>` | 使用本地附属文件在 Label Studio 中打开已保存的界面。 |
| `interface doctor` | 检查 Node.js、npm、API 令牌和连接性。 |

## 故障排除

| 错误 | 解决方法 |
| --- | --- |
| `node is required for interface validation` | 安装 Node.js 并确保 `node` 在您的 `PATH` 中。 |
| `npm is required to install validator dependencies` | 安装 npm 并重新运行 `interface doctor`。 |
| `source did not compile` | 修复 JSX 语法错误。界面文件作为自包含的 JSX 片段编译；不支持 `import`/`require` 和打包器特定语法。要使用第三方库，请**将其预打包到单个源文件中**（或从允许列表中的主机加载）。请参阅[使用外部库和服务](interfaces-libraries)。 |
| `Module did not return an object` | 将文件中的最终表达式设置为用括号包裹的对象字面量，例如 `({ default: Screen })`。 |
| `Missing getResults` 或 `Missing parseResults` | 静态验证期间的警告，但场景验证需要这两个函数。当需要可靠的保存/加载行为时，请添加这两个函数。 |
| `no sidecar entry` | 针对目标 Label Studio URL 运行 `interface sync`，或为支持该参数的命令传递 `--id`。 |
| `multiple workspaces titled ...` | 使用 `--workspace` 并传入数字工作区 ID，而不是 `--workspace-title`。 |
