---
title: 管理用户账户
short: 管理用户账户
tier: enterprise
type: guide
order: 0
order_enterprise: 372
meta_title: 管理用户账户
meta_description: 如何为用户分配角色、删除用户、标记用户以及停用用户
section: "管理您的组织"
parent: "manage_users"
parent_enterprise: "manage_users"
date: 2024-02-06 12:00:25
---

!!! note
    如果您使用 SSO/SAML 或 SCIM，可以在组织级别和项目级别将组映射到角色。有关更多信息，请参阅[为 Label Studio 设置 SSO 认证](auth_setup)和[为 Label Studio 设置 SCIM2](scim_setup)。

    如果您使用带有 SCIM 或 SAML/SSO 的 IdP，还应该从 IdP 处理用户停用和删除操作。

## 为用户分配角色

如果您使用[邀请链接选项](admin_user#Invite-users-to-Label-Studio-Enterprise)邀请用户，其账户会被创建，但必须通过分配角色来激活。

!!! info 提示
    您可以使用表格上方的角色筛选器查看所有待处理用户。选择**未激活**角色。

1. 打开 Label Studio，点击左上角的菜单。
2. 选择**组织**。

    如果您没有看到**组织**选项，说明您没有访问组织页面的权限。只有处于 Owner 或 Administrator 角色的用户才能访问此页面。

3. 找到您要分配角色的用户账户。

4. 使用用户旁边的下拉菜单选择一个角色。

![角色下拉菜单截图](/images/admin/assign_role.png)

## 以编程方式分配角色

要以编程方式激活用户并分配角色，您可以使用 API：

https://api.labelstud.io/api-reference/api-reference/organizations/members/update

您可以在**组织**页面找到用户 ID 和组织 ID：

![角色下拉菜单截图](/images/admin/user-ids.png)

您也可以使用以下 API：

* [列出组织](https://api.labelstud.io/api-reference/api-reference/organizations/list)
* [列出成员](https://api.labelstud.io/api-reference/api-reference/organizations/members/list)

## 标记成员

您可以使用元数据标记组织成员。

### 通过 CSV 批量应用成员标签

要批量应用成员标签，请转到组织页面并点击**标记成员**。

您可以在此处上传以下格式的 CSV：

```csv
email,tags
heidi@humansignal.com,"Data Science, Seattle"
sally@humansignal.com,"Finance, New York City"
```

* 确保包含 `email,tags` 作为表头。
* 标签必须用引号括起来。
* 如果有多个标签，用逗号分隔。

!!! info 提示
    如果您是从电子表格编辑器导出 CSV，请确保在上传前在文本编辑器中检查格式。电子表格编辑器可能会添加不必要的格式。

### 添加和删除标签

要删除或手动添加新标签，请转到**组织 > 设置 > 成员标签**。

### 手动分配标签

如果您想手动为单个用户分配标签，可以使用组织成员表格中的**标签**列。

![截图](/images/admin/tags-apply.png)

## 停用用户

您可以通过将用户分配到**已停用**角色来停用用户账户。停用用户将撤销其访问权限，并释放其占用的许可证名额。

您可以通过将用户重新分配到活跃用户角色来重新激活用户。

## 删除用户

1. 在组织页面，选择您要删除的用户。

2. 点击**删除**。

3. 出现确认消息。点击**删除成员**。

一旦删除，用户已完成的任何工作或所做的任何更改都将归属于"已删除用户"。

如果您以后想再次添加该用户，需要重新邀请他们加入组织。
