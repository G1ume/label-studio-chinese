---
title: 比较 Label Studio 各版本
short: 版本比较
type: guide
tier: all
order: 28
order_enterprise: 28
section: "了解与学习"
meta_title: Label Studio 社区版与企业版功能对比
meta_description: 比较 Label Studio 社区版与付费版本的功能，以便您选择最适合数据标注项目需求的方案。
---

Label Studio 以开源软件形式（Label Studio 社区版）向所有人开放。此外还有两个付费版本：Starter Cloud 和 Enterprise。

<a class="Button" href="#功能对比" target="_blank" style="margin-bottom: 2em;">查看完整功能对比</a>

## 概览

![ls compare](../images/ls_compare.png)

<a class="Button" href="https://humansignal.com/" target="_blank" style="margin-bottom: 2em;">了解 Enterprise 版</a>

## 功能对比

<table>
<thead>
  <tr>
    <th>功能</th>
    <th>社区版</th>
    <th>Starter Cloud</th>
    <th>企业版</th>
  </tr>
  </thead>
  <tr>
    <td colspan="4" style="text-align:center"><b>用户管理</b></td>
  </tr>

  <tr>
    <td><b>基于角色的工作流</b><br/><a href="https://docs.humansignal.com/guide/project_settings_lse#Annotation">面向标注员和审核员的基于角色的自动化工作流。</a></td>
    <td style="text-align:center">❌</td>
    <td style="text-align:center">有限</td>
    <td style="text-align:center">✅</td>
  </tr>
  <tr>
    <td><b>基于角色的访问控制</b><br/><a href="https://docs.humansignal.com/guide/admin_roles">对工作区和项目基于角色的访问控制：Admin、Manager、Reviewer 和 Annotator。</a></td>
    <td style="text-align:center">❌</td>
    <td style="text-align:center">✅</td>
    <td style="text-align:center">✅</td>
  </tr>
  <tr>
    <td><b>可自定义的权限</b><br/><a href="https://docs.humansignal.com/guide/admin_permissions">精细控制哪些角色可以执行特定操作，例如邀请成员、管理云存储、删除任务等。</a></td>
    <td style="text-align:center">❌</td>
    <td style="text-align:center">❌</td>
    <td style="text-align:center">✅</td>
  </tr>
  <tr>
    <td colspan="4" style="text-align:center"><b>数据管理</b></td>
  </tr>
  <tr>
    <td><b>数据管理视图</b><br/><a href="manage_data.html">通过数据管理器视图查看和管理项目中的数据集与任务。</a></td>
    <td style="text-align:center">✅</td>
    <td style="text-align:center">✅</td>
    <td style="text-align:center">✅</td>
  </tr>
  <tr>
    <td><b>多种数据格式</b><br/><a href="https://labelstud.io/playground/">标注任何数据类型：文本、图像、音频、视频、时间序列数据以及多模态数据。</a></td>
    <td style="text-align:center">✅</td>
    <td style="text-align:center">✅</td>
    <td style="text-align:center">✅</td>
  </tr>
  <tr>
    <td><b>导入数据</b><br/><a href="tasks.html">引用存储在数据库、云存储桶或本地存储中的数据，并在浏览器中进行标注。</a></td>
    <td style="text-align:center">✅</td>
    <td style="text-align:center">✅</td>
    <td style="text-align:center">✅</td>
  </tr>
    <tr>
    <td><b>高级云存储集成</b><br/><a href="storage.html">Databricks Unity Catalog、适用于 AWS S3 的 IAM、使用 Service Principal 的 Azure Blob Storage 以及适用于 Google Cloud Storage 的 WIF 连接。</a></td>
    <td style="text-align:center">❌</td>
    <td style="text-align:center">❌</td>
    <td style="text-align:center">✅</td>
  </tr>
  <tr>
    <td><b>导入预标注数据</b><br/><a href="predictions.html">将预标注数据（预测）导入 Label Studio 以便进一步修改和评估。</a></td>
    <td style="text-align:center">✅</td>
    <td style="text-align:center">✅</td>
    <td style="text-align:center">✅</td>
  </tr>
  <tr>
    <td><b>导出数据</b><br/><a href="export.html">将标注导出为常用格式，如 JSON、COCO、Pascal VOC 等。</a></td>
    <td style="text-align:center">✅</td>
    <td style="text-align:center">✅</td>
    <td style="text-align:center">✅</td>
  </tr>
  <tr>
    <td><b>同步数据</b><br/><a href="storage.html">在项目与外部数据存储之间同步新增和已标注的数据。</a></td>
    <td style="text-align:center">✅</td>
    <td style="text-align:center">✅</td>
    <td style="text-align:center">✅</td>
  </tr>
  <tr>
    <td><b>Chat</b><br/><a href="/tags/chat.html">生成并标注对话数据。与您选择的 LLM 进行实时交互。</a></td>
    <td style="text-align:center">❌</td>
    <td style="text-align:center">有限</td>
    <td style="text-align:center">✅</td>
  </tr>
  <tr>
    <td colspan="4" style="text-align:center"><b>项目管理</b></td>
  </tr>
  <tr>
    <td><b>在项目中组织数据</b><br/><a href="setup_project.html">通过项目管理数据标注活动。</a></td>
    <td style="text-align:center">✅</td>
    <td style="text-align:center">✅</td>
    <td style="text-align:center">✅</td>
  </tr>
  <tr>
    <td><b>在工作区中组织项目</b><br/><a href="https://docs.humansignal.com/guide/workspaces">按团队、部门或产品组织相关项目。用户只能访问与自己关联的工作区。</a></td>
    <td style="text-align:center">❌</td>
    <td style="text-align:center">❌</td>
    <td style="text-align:center">✅</td>
  </tr>
  <tr>
    <td><b>个人沙盒工作区</b><br/><a href="https://docs.humansignal.com/guide/workspaces">用于项目测试和实验的个人沙盒工作区。</a></td>
    <td style="text-align:center">❌</td>
    <td style="text-align:center">✅</td>
    <td style="text-align:center">✅</td>
  </tr>
  <tr>
    <td><b>模板</b><br/><a href="/templates">加速搭建数据标注项目的模板。</a></td>
    <td style="text-align:center">✅</td>
    <td style="text-align:center">✅</td>
    <td style="text-align:center">✅</td>
  </tr>
  <tr>
    <td><b>AI 助手</b><br/><a href="https://docs.humansignal.com/guide/ask_ai">使用由 HumanSignal 训练的 LLM 帮助您创建和优化模板。</a></td>
    <td style="text-align:center">❌</td>
    <td style="text-align:center">✅</td>
    <td style="text-align:center">✅</td>
  </tr>
  <tr>
    <td><b>项目成员</b><br/><a href="https://docs.humansignal.com/guide/project_settings_lse#Members">只有被添加为项目成员的用户才能查看该项目。</a></td>
    <td style="text-align:center">❌</td>
    <td style="text-align:center">✅</td>
    <td style="text-align:center">✅</td>
  </tr>
  <tr>
    <td><b>项目级角色</b><br/><a href="https://docs.humansignal.com/guide/project_settings_lse#Members">Annotator 和 Reviewer 可以在每个项目级别被分配为标注员/审核员角色。</a></td>
    <td style="text-align:center">❌</td>
    <td style="text-align:center">✅</td>
    <td style="text-align:center">✅</td>
  </tr>
  <tr>
    <td><b>项目级用户设置</b><br/><a href="https://docs.humansignal.com/guide/project_settings_lse#Annotation">标注员和审核员如何与任务交互以及他们能看到哪些信息的多种配置选项。</a></td>
    <td style="text-align:center">❌</td>
    <td style="text-align:center">有限</td>
    <td style="text-align:center">✅</td>
  </tr>

<tr>
    <td colspan="4" style="text-align:center"><b>数据标注工作流</b></td>
  </tr>
  <tr>
    <td><b>分配任务</b><br/><a href="https://docs.humansignal.com/guide/manage_data#Assign-annotators-to-tasks">将任务分配给特定的标注员或审核员。</a></td>
    <td style="text-align:center">❌</td>
    <td style="text-align:center">✅</td>
    <td style="text-align:center">✅</td>
  </tr>
  <tr>
    <td><b>自动分配任务</b><br/><a href="https://docs.humansignal.com/guide/setup_project#Configure-high-impact-settings">设置规则并自动化任务分配给标注员的方式。</a></td>
    <td style="text-align:center">❌</td>
    <td style="text-align:center">✅</td>
    <td style="text-align:center">✅</td>
  </tr>
  <tr>
    <td><b>标注员简化界面</b><br/>仅显示已分配任务的标注员专属标注视图。</td>
    <td style="text-align:center">❌</td>
    <td style="text-align:center">✅</td>
    <td style="text-align:center">✅</td>
  </tr>
  <tr>
    <td><b>批量标注</b><br/><a href="https://docs.humansignal.com/guide/labeling_bulk">批量分类数据。</a></td>
    <td style="text-align:center">❌</td>
    <td style="text-align:center">❌</td>
    <td style="text-align:center">✅</td>
  </tr>
   <tr>
    <td colspan="4" style="text-align:center"><b>自定义与开发</b></td>
  </tr>
 <tr>
    <td><b>可编程和嵌入式界面</b><br/><a href="https://docs.humansignal.com/tags/reactcode">使用 React 构建完全自定义的标注和评估界面。可嵌入到您自己的应用中。</a></td>
    <td style="text-align:center">❌</td>
    <td style="text-align:center">❌</td>
    <td style="text-align:center">✅</td>
  </tr>
  <tr>
    <td><b>标签库</b><br/><a href="/tags">使用我们的标签库通过修改预构建模板或构建自己的模板来自定义标注界面。</a></td>
    <td style="text-align:center">✅</td>
    <td style="text-align:center">✅</td>
    <td style="text-align:center">✅</td>
  </tr>
  <tr>
    <td><b>白标</b><br/>使用您公司的颜色和徽标，为团队提供一致的体验。（额外收费）</td>
    <td style="text-align:center">❌</td>
    <td style="text-align:center">❌</td>
    <td style="text-align:center">✅</td>
  </tr>
  <tr>
    <td><b>插件</b><br/><a href="https://docs.humansignal.com/guide/plugins">使用 JavaScript 进一步增强和自定义您的标注界面。</a></td>
    <td style="text-align:center">❌</td>
    <td style="text-align:center">❌</td>
    <td style="text-align:center">✅</td>
  </tr>
  <tr>
    <td><b>API/SDK 和 Webhooks</b><br/><a href="api.html">用于以编程方式访问和管理 Label Studio 的 API、SDK 和 webhooks。</a></td>
    <td style="text-align:center">✅</td>
    <td style="text-align:center">✅</td>
    <td style="text-align:center">✅</td>
  </tr>
  <tr>
    <td colspan="4" style="text-align:center"><b>Prompts</b></td>
  </tr>
   <tr>
    <td><b>自动预标注</b><br/><a href="https://docs.humansignal.com/guide/prompts_overview">使用 LLM 快速预标注任务。</a></td>
    <td style="text-align:center">❌</td>
    <td style="text-align:center">有限试用</td>
    <td style="text-align:center">✅</td>
  </tr>
  <tr>
    <td><b>LLM 微调与评估</b><br/><a href="https://docs.humansignal.com/guide/prompts_draft">针对基准真相数据集评估和微调 LLM 提示词。</a></td>
    <td style="text-align:center">❌</td>
    <td style="text-align:center">有限试用</td>
    <td style="text-align:center">✅</td>
  </tr>
  <tr>
    <td><b>运行基准测试</b><br/><a href="https://docs.humansignal.com/guide/prompts_draft#Evaluation-results">将模型输出与基准真相或评分标准进行比较。</a></td>
    <td style="text-align:center">❌</td>
    <td style="text-align:center">有限试用</td>
    <td style="text-align:center">✅</td>
  </tr>
  <tr>
    <td colspan="4" style="text-align:center"><b>机器学习</b></td>
  </tr>
  <tr>
    <td><b>自定义 ML 后端</b><br/><a href="ml.html">将机器学习模型连接到项目后端。</a></td>
    <td style="text-align:center">✅</td>
    <td style="text-align:center">✅</td>
    <td style="text-align:center">✅</td>
  </tr>
  <tr>
    <td><b>主动学习循环</b><br/><a href="https://docs.humansignal.com/guide/active_learning.html">使用主动学习循环加速标注。</a></td>
    <td style="text-align:center">❌</td>
    <td style="text-align:center">❌</td>
    <td style="text-align:center">✅</td>
  </tr>
  <tr>
    <td><b>从连接模型获取预测</b><br/><a href="ml.html#Pre-annotations-predictions">通过 ML 模型后端自动标注任务并按预测分数排序。</a></td>
    <td style="text-align:center">✅</td>
    <td style="text-align:center">✅</td>
    <td style="text-align:center">✅</td>
  </tr>
  <tr>
    <td colspan="4" style="text-align:center"><b>分析与报告</b></td>
  </tr>
  <tr>
    <td><b>项目仪表盘</b><br/><a href="https://docs.humansignal.com/guide/dashboards">用于监控项目进度的仪表盘。</a></td>
    <td style="text-align:center">❌</td>
    <td style="text-align:center">❌</td>
    <td style="text-align:center">✅</td>
  </tr>
  <tr>
    <td><b>标注员绩效仪表盘</b><br/><a href="https://docs.humansignal.com/guide/dashboard_annotator">用于审核和监控标注员个人绩效的仪表盘。</a></td>
    <td style="text-align:center">❌</td>
    <td style="text-align:center">❌</td>
    <td style="text-align:center">✅</td>
  </tr>
  <tr>
    <td><b>活动日志</b><br/><a href="https://docs.humansignal.com/guide/admin_logs">按项目审计标注活动的活动日志。</a></td>
    <td style="text-align:center">❌</td>
    <td style="text-align:center">❌</td>
    <td style="text-align:center">✅</td>
  </tr>
  <tr>
    <td><b>标注历史</b><br/>从标注界面查看标注历史。</td>
    <td style="text-align:center">❌</td>
    <td style="text-align:center">✅</td>
    <td style="text-align:center">✅</td>
  </tr>
  <tr>
    <td colspan="4" style="text-align:center"><b>质量工作流</b></td>
  </tr>
  <tr>
    <td><b>分配审核员</b><br/><a href="https://docs.humansignal.com/guide/quality.html">分配审核员来审核、修正和更新标注。</a></td>
    <td style="text-align:center">❌</td>
    <td style="text-align:center">✅</td>
    <td style="text-align:center">✅</td>
  </tr>
  <tr>
    <td><b>自动任务重新分配</b><br/><a href="https://docs.humansignal.com/guide/quality.html">将一致性分数较低的任务重新分配给新的标注员。</a></td>
    <td style="text-align:center">❌</td>
    <td style="text-align:center">❌</td>
    <td style="text-align:center">✅</td>
  </tr>
  <tr>
    <td><b>一致性指标</b><br/><a href="https://docs.humansignal.com/guide/stats.html">使用预定义的一致性指标定义标注员共识的计算方式。</a></td>
    <td style="text-align:center">❌</td>
    <td style="text-align:center">有限</td>
    <td style="text-align:center">✅</td>
  </tr>
  <tr>
    <td><b>自定义一致性指标</b><br/><a href="https://docs.humansignal.com/guide/custom_metric.html">编写您自己的自定义一致性指标。</a></td>
    <td style="text-align:center">❌</td>
    <td style="text-align:center">❌</td>
    <td style="text-align:center">✅</td>
  </tr>
  <tr>
    <td><b>评论与通知</b><br/><a href="https://docs.humansignal.com/guide/comments_notifications.html">团队协作功能，如标注任务上的评论和通知。</a></td>
    <td style="text-align:center">❌</td>
    <td style="text-align:center">✅</td>
    <td style="text-align:center">✅</td>
  </tr>
  <tr>
    <td><b>标识基准真相</b><br/><a href="https://docs.humansignal.com/guide/quality.html">标记哪些标注应包含在基准真相数据集中。</a></td>
    <td style="text-align:center">❌</td>
    <td style="text-align:center">✅</td>
    <td style="text-align:center">✅</td>
  </tr>
  <tr>
    <td><b>重叠配置</b><br/><a href="https://docs.humansignal.com/guide/project_settings_lse#Quality">设置每个样本必须有多少位标注员标注。</a></td>
    <td style="text-align:center">❌</td>
    <td style="text-align:center">✅</td>
    <td style="text-align:center">✅</td>
  </tr>
  <tr>
    <td><b>暂停标注员</b><br/><a href="https://docs.humansignal.com/guide/quality#Pause-an-annotator">手动或根据预定义行为暂停单个标注员的工作进度。</a></td>
    <td style="text-align:center">❌</td>
    <td style="text-align:center">❌</td>
    <td style="text-align:center">✅</td>
  </tr>
  <tr>
    <td><b>标注限制</b><br/><a href="https://docs.humansignal.com/guide/project_settings_lse#annotation-limit">设置在用户被暂停工作前其在项目中可以提交的标注数量上限。</a></td>
    <td style="text-align:center">❌</td>
    <td style="text-align:center">❌</td>
    <td style="text-align:center">✅</td>
  </tr>
  <tr>
    <td><b>标注员一致性矩阵</b><br/><a href="https://docs.humansignal.com/guide/quality#Review-annotator-agreement-matrix">用于比较不同标注员标注结果的矩阵。</a></td>
    <td style="text-align:center">❌</td>
    <td style="text-align:center">❌</td>
    <td style="text-align:center">✅</td>
  </tr>
  <tr>
    <td><b>标签分布图</b><br/><a href="https://docs.humansignal.com/guide/dashboard_distribution">识别数据集中可能存在的问题，如数据集不平衡。</a></td>
    <td style="text-align:center">❌</td>
    <td style="text-align:center">❌</td>
    <td style="text-align:center">✅</td>
  </tr>
  <tr>
    <td colspan="4" style="text-align:center"><b>安全与支持</b></td>
  </tr>
  <tr>
    <td><b>SSO</b><br/><a href="https://docs.humansignal.com/guide/auth_setup.html">通过 SAML SSO 或 LDAP 实现用户安全访问与认证。</a></td>
    <td style="text-align:center">❌</td>
    <td style="text-align:center">❌</td>
    <td style="text-align:center">✅</td>
   </tr>
  <tr>
    <td><b>SOC2</b><br/><a href ="https://heartex.com/security">符合 SOC2 的托管云服务或本地部署可用性</a></td>
    <td style="text-align:center">❌</td>
    <td style="text-align:center">❌</td>
    <td style="text-align:center">✅</td>
  </tr>
  <tr>
    <td><b>支持门户</b><br/>专属支持门户的访问权限。</td>
    <td style="text-align:center">❌</td>
    <td style="text-align:center">✅</td>
    <td style="text-align:center">✅</td>
  </tr>
  <tr>
    <td><b>正常运行时间 SLA</b><br/>99.9% 正常运行时间 SLA</td>
    <td style="text-align:center">❌</td>
    <td style="text-align:center">❌</td>
    <td style="text-align:center">✅</td>
  </tr>
  <tr>
    <td><b>客户成功经理</b><br/>专属客户成功经理，支持入职、培训与问题升级处理。</td>
    <td style="text-align:center">❌</td>
    <td style="text-align:center">❌</td>
    <td style="text-align:center">✅</td>
  </tr>
</table>
