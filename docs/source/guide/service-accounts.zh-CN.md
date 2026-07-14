---
title: 服务账号
short: 服务账号
tier: enterprise
type: guide
hide_menu: false
order: 0
order_enterprise: 363
meta_title: 服务账号
meta_description: Label Studio Enterprise 服务账号概述
section: "Manage Your Organization"
parent_enterprise: "admin_settings"
date: 2025-02-18 12:03:59
---

服务账号被允许通过 API 访问 Label Studio，但不能通过 UI 登录或与平台交互。它们仅用于通过 [Label Studio API](https://api.labelstud.io/api-reference/introduction/getting-started) 进行程序化访问。


!!! note 注意
    默认情况下，每个组织允许创建 1 个服务账号。此初始服务账号不计入您的许可证席位数量。

    如果需要添加更多服务账号，可以申请额外的服务账号。

## 创建服务账号

要添加服务账号，请前往 **Organization > Settings > Service Accounts**。

点击 **Create Service Account** 并填写以下字段：

| 字段 | 描述 |
|---|---|
| **Name** | 服务账号的名称。创建后不可更改。 |
| **Organization role** | 角色决定了服务账号在组织内拥有的程序化访问权限级别。有关更多信息，请参阅[用户角色和权限](admin_roles)。<br><br>**注意：** 您以后可以根据需要升级或降级服务账号的角色。 |
| **Workspaces and projects** | 对于具有 Annotator、Reviewer 或 Manager 角色的服务账号，请将其分配到一个或多个工作区和/或项目。<br><br>**注意：** 您以后可以通过与标准用户相同的方式来分配或移除服务账号来更改工作区/项目。在管理项目和 workspace 成员时，服务账号会出现在用户列表中。 |

创建服务账号时，系统会为您提供一个用于该账号的 API 令牌。您只有一次复制令牌的机会，因此请务必将令牌复制到安全的位置。

创建完成后，服务账号将拥有其角色和项目范围内的完整 API 访问权限和功能。

## 更新和管理服务账号

点击服务账号旁边的溢出菜单，可查看以下操作：

| 操作 | 描述 |
|---|---|
| **View Member Performance** | 查看服务账号的[成员绩效仪表板](dashboard_annotator)。 |
| **View Activity Log** | 查看服务账号的[活动日志](admin_logs)。 |
| **Reset Token** | 撤销并重置服务账号的 API 令牌。 |
| **Delete** | 删除服务账号。 |
