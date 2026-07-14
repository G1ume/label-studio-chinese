---
title: SCIM 如何与 Label Studio Enterprise 配合使用
short: SCIM 工作流程与 API
tier: enterprise
type: guide
order: 0
order_enterprise: 390
meta_title: SCIM 如何与 Label Studio Enterprise 配合使用
meta_description: SCIM 工作流程和实体说明
section: "Manage Your Organization"
parent: "scim_setup"
parent_enterprise: "scim_setup"
date: 2024-01-25 10:07:22
---

跨域身份管理系统（SCIM）是一种开放标准，用于自动在身份域或 IT 系统之间交换用户身份信息。SCIM 旨在使基于云的应用和服务中的用户管理更加简单高效，减少用户管理所需的时间和资源。

对于使用 Label Studio Enterprise 的组织，SCIM 提供了一种简化的方法来管理用户身份和访问权限。通过集成 SCIM，管理员可以自动配置和取消配置用户、跨系统同步用户数据，并确保合适的人员能够访问 Label Studio Enterprise 中的必要资源。

## SCIM 工作流程

您可以使用 SCIM 执行以下操作：

* 添加用户
* 移除用户（将其用户角色设置为"已停用（Deactivated）"）
* 将用户分配到组
* 从组中取消分配用户
* 将组映射到用户角色

    请注意，组是在 IdP 中定义的，而非 Label Studio。但是，组到角色的映射是在 Label Studio 中定义的。

## SCIM API 端点

您可以使用 SCIM 与 Label Studio 来控制两个实体并与其交互：用户和组。

!!! note 注意
    我们的 API 使用 [django-scim2 库](https://django-scim2.readthedocs.io/en/latest/)。

#### 用户
* 搜索用户：`GET /scim/v2/Users?filter=userName =<user@email.com>&startIndex=1&count=100`
    * 如果用户存在，返回 `200` 响应
    * 如果不存在，返回 `404`
* 获取用户：`GET /scim/v2/Users/user@email.com`
* 创建用户：`POST /scim/v2/Users/`

    这还需要一个包含用户信息（如 email 和密码）的负载。

#### 组

* 修改组成员：`PUT /scim/v2/Groups/<group-name>`

    示例：
    ```json
{
  "BODY": {
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
    "id": "<group-name>",
    "displayName": "<group-name>",
    "members": [
      {
        "value": "<user@email.com>",
        "display": "<user@email.com>"
      }
    ]
  }
}
    ```

* 创建组：`POST /scim/v2/Groups/<group-name>`
* 获取组：`GET /scim/v2/Groups/<group-name>`

#### SCIM 设置 API

这些设置也可以在 Label Studio 应用中的 **Organization > SCIM** 页面进行配置。在此页面中，您可以将组映射到用户角色以及将组映射到工作区（[见下文](#SCIM-settings)）。

* [获取 SCIM 设置](https://app.heartex.com/docs/api/#tag/SSO/operation/api_scim_settings_list)：`GET /api/scim/settings`
* [更新 SCIM 设置](https://app.heartex.com/docs/api/#tag/SSO/operation/api_scim_settings_list)：`POST /api/scim/settings`


## SCIM 设置

### 角色

您可以通过组映射为用户分配角色。使用[更新 SCIM 设置 API](https://api.labelstud.io/api-reference/api-reference/sso/scim/update)，或登录 Label Studio 并前往 **Organization** 页面。点击右上角的 **SCIM**。

#### 组织级角色

这些角色可以是 **Annotator**、**Reviewer**、**Manager** 或 **Administrator**。

每个组在组织级别只能映射到一个角色。有关每个角色拥有哪些权限的更多信息，请参阅 [Label Studio Enterprise 中的角色](manage_users#Roles-in-Label-Studio-Enterprise)。

您还可以将组分配到**已停用（Deactivated）** 角色，这将撤销其对 Label Studio 的访问权限。


#### 项目级角色

如需更精细的控制，您可以为组分配项目级角色。

这些角色可以是 **Annotator**、**Reviewer** 或 **Inherit**（即继承其组织级角色）。

与组织级角色不同，一个组可以在多个项目中分配多个角色。例如，组 A 在项目 1 中可以是 Annotator，在项目 2 中可以是 Reviewer。


### 工作区

您还可以使用 SCIM 将用户组分配到工作区，或在工作区尚不存在时创建新的工作区。

当您将组分配到工作区时，他们将被添加为工作区成员。这意味着默认情况下，他们将有权访问该工作区内的任何项目。

他们在这些项目中的权限将取决于其组织级角色。但是，您可以通过使用 SCIM 分配项目级角色来覆盖此设置（见上文）。
