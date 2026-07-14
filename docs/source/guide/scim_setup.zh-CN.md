---
title: 为 Label Studio 设置 SCIM2
short: SCIM2
tier: enterprise
type: guide
order: 0
order_enterprise: 389
meta_title: Label Studio Enterprise 的跨域身份管理系统（SCIM）
meta_description: Label Studio Enterprise 设置 SCIM2 的文档
section: "Manage Your Organization"
parent: "admin_auth"
parent_enterprise: "admin_auth"
---

跨域身份管理系统（SCIM）是一种流行的协议，用于管理整个组织内服务和应用程序的访问权限。

使用 SCIM 提供商，您可以管理对 Label Studio Enterprise 工作区的访问，并为个人用户和组授予角色。

## 要求

Label Studio Enterprise 使用 SCIM 2.0 版标准。

Label Studio Enterprise 遵循 [SCIM RFC 5741](https://datatracker.ietf.org/doc/html/rfc7644#section-3.2)，可与任何支持该标准的访问管理服务集成。

有关 SCIM 工作流程的更多信息，请参阅 [SCIM 如何与 Label Studio Enterprise 配合使用](scim_workflow)。

## 前提条件

* SCIM 与您的 SSO 集成配合使用。在开始之前，您必须已配置好 SSO。如果尚未设置 SSO，请按照[设置 SSO](auth_setup.html) 进行操作。

!!! note 注意
    Okta 或类似的 SSO 提供商具有基于 SSO 的 SCIM 集成。

* 您需要提供一个 [Legacy 令牌](access_tokens#Legacy-tokens)，并且该令牌必须与您组织的 Owner 角色关联。

## 设置与 Okta 的 SCIM 集成

!!! attention "重要"
    此视频演示了在"用户唯一标识字段"中使用 `userName`。必须使用 `email` 作为唯一标识符，而不是 `userName`；否则，对于在 SCIM 集成之前创建的用户，SCIM 将无法正常工作。

<iframe width="560" height="315" src="https://www.youtube.com/embed/MA3de3gu18A" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

要管理对 Label Studio Enterprise 的访问，请将应用程序添加到您的 SCIM 提供商（Okta）。

Okta 使用 Bearer 令牌（请求头应为 `Authorization: Bearer <token>`）与应用程序的 REST API 端点交互，以配置和取消配置访问权限。

### 将 Label Studio Enterprise 添加为应用程序（如尚未完成）

1. 在 Okta 中导航到 **Applications > Applications**。点击 **Create App Integration**。
2. 选择 **SAML 2.0**。输入应用名称（例如 _Label Studio Enterprise_）。
3. 在 **Configure SAML** 下，按照[设置 SSO 指南](auth_setup.html)中概述的步骤设置 SAML 集成。
4. 确保 Label Studio Enterprise 出现在活动应用程序列表中。

### 启用 SCIM 配置

1. 在 Okta 中导航到 **Applications > Applications**。
2. 选择 **Label Studio Enterprise**。
3. 选择 **General** 选项卡，然后选择 **Enable SCIM provisioning**。
4. 选择 **Provisioning** 选项卡。
5. 在左侧菜单中选择 **Integration**。
6. 点击右侧的 **Edit**。

填写以下字段：

| 字段 | 值/描述 |
|-------|-------------------|
| **SCIM connector base URL** | `https://<LABEL_STUDIO_BASE_URL>/scim/v2/`，其中 `<LABEL_STUDIO_BASE_URL>` 是您的 Label Studio Enterprise 实例的基础 URL。 |
| **Unique identifier field for users** | 使用 `email`。Label Studio Enterprise 在此字段中使用 email 作为用户标识符。 |
| **Supported provisioning actions** | 选择以下项目：<br>- Import New Users and Profile Updates<br>- Push New Users<br>- Push Profile Updates<br>- Push Groups |
| **HTTP Header → `Authorization: Bearer <token>`** | 输入与 Label Studio 中 Owner 账户关联的 [Legacy 令牌](access_tokens#Legacy-tokens)。<br />对于 Label Studio，`Token` 和 `Bearer` 是相同的令牌。但是，在请求头中使用 `Bearer` 而不是 `Token` 非常重要。 |

### SCIM 设置和应用程序触发器

1. 在应用程序页面中，导航到 **Provisioning** 选项卡，在左侧菜单中选择 **To App**。点击右侧的 **Edit**。
2. 启用以下项目：
   - Create Users
   - Update User Attributes
   - Deactivate Users

### 将应用程序分配给单个用户

您可以在**用户**页面和**应用程序**页面上分配应用程序。

1. 在**应用程序**页面上，导航到 **Assignments** 选项卡。
2. 点击 **Assign** 并选择 **Assign to People**。
3. 选择您想要添加到 Label Studio Enterprise 的人员。
4. 点击 **Done**。

点击 **Done** 后，Okta 将发送请求，在 Label Studio Enterprise 中相应地创建用户。

### 取消用户的应用程序分配

1. 在应用程序页面上，导航到 **Assignments** 选项卡。
2. 在左侧菜单中选择 **People**。
3. 点击要取消分配的用户的删除叉号。
4. 确认取消分配。

### 将应用程序分配给组

管理应用程序访问权限最方便的方式是通过组。您可以将 Label Studio 分配给组，并在 Okta 中管理这些组。更改将传播到应用程序。

### 在 Label Studio 中设置组映射

1. 在 Label Studio 中，点击左上角的菜单，选择 **Organization**。

2. 在右上角选择 **SCIM**。
3. 更新角色和工作区映射。确保您输入的组名称与 SCIM 提供商发送的组名称相同。

    * **组织角色到组的映射（Organization Roles to Groups Mapping）** --- 将组映射到组织级别的角色。在组织级别设置的角色是用户的默认角色，并会自动分配给工作区和项目。有关角色的更多信息，请参阅 [Label Studio Enterprise 中的角色](admin_roles)。

        您可以将多个组映射到同一角色。请注意，**未激活（Not Activated）** 或**已停用（Deactivated）** 的用户不计入您账户的席位限制。
    * **工作区到组的映射（Workspaces to Groups Mapping）** --- 将组作为成员添加到工作区。具有 Manager、Reviewer 或 Annotator 角色的用户只有在被添加为该工作区的成员后才能看到工作区。

        选择现有工作区或创建新工作区。您可以将多个组映射到同一工作区。
    * **项目到组的映射（Projects to Groups Mapping）** --- 将组映射到项目级别的角色。项目级角色可以是 **Annotator**、**Reviewer** 或 **Inherit**。

        您可以将一个组映射到多个项目中的不同角色。您也可以将多个组映射到相同的角色和相同的项目。有关角色的更多信息，请参阅 [Label Studio Enterprise 中的角色](admin_roles)。

        如果选择 **Inherit**，该组将继承上面**组织角色到组的映射**中设置的角色。如果组继承的是"未激活（Not Activated）"角色，则用户会被映射到项目，但在组同步之前（即用户先进行身份验证），他们实际上并未被分配到该项目。

### 将组分配给应用程序

1. 在 Okta 中，导航到**应用程序**页面，打开 **Assignments** 选项卡。
2. 选择 **Assign → Assign to Groups** 并选择组。
3. 将属性 **Active** 设置为 **true**。

保存组分配后，更新将排队并发送到 Label Studio。

!!! note 注意
    或者，您可以立即将更改推送到 Label Studio。

### 将组同步到应用程序

1. 在 Okta 中，导航到**应用程序**页面，打开 **Push Groups** 选项卡。
2. 点击 **Push Groups** 并选择 **Find groups by name**。
3. 找到要同步到 Label Studio 的组。
4. 选择 **Create Group** 或 **Link Group**（如果您已经有一个与 **SCIM >> Settings** 页面上指定的名称相同的工作区）。

### 取消组的应用程序分配

要取消组的应用程序分配，请按照[取消用户的应用程序分配](#Unassigning-the-application-for-users)的步骤操作。

1. 在**应用程序**页面上，导航到 **Assignments** 选项卡。
2. 在左侧菜单中选择 **Group**。
3. 点击要取消分配的组的删除叉号。
4. 确认取消分配。


<i>请查看此视频教程以了解如何移除用户和组。</i>
<iframe width="560" height="315" src="https://www.youtube.com/embed/vMA0TLhHGYE" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

## 设置与 Microsoft Entra ID（Azure AD）的 SCIM 集成

Label Studio Enterprise 支持与 Microsoft Entra ID（原 Azure AD）的 SCIM 配置。设置方式与 Okta 类似，但需要特定的属性映射配置。

### 支持的用户属性

Label Studio Enterprise 支持一组有限的 SCIM 用户属性用于配置。在 Microsoft Entra ID 中配置属性映射时，请仅包含以下列出的属性。

| SCIM 属性 | 描述 | 是否必需 |
|---|---|---|
| `emails[type eq "work"].value` | 用户的电子邮件地址（主要标识符） | 是 |
| `userName` | 用户名（在 Label Studio 中映射到 email） | 是 |
| `active` | 用户是否处于活动状态 | 是 |
| `name.givenName` | 用户的名 | 否 |
| `name.familyName` | 用户的姓 | 否 |

!!! warning "不支持的属性会导致配置错误"
    映射 Label Studio 不支持的属性将导致 SCIM 配置期间出现 **HTTP 501（未实现）** 错误。您必须移除所有多余的 Microsoft Entra ID 属性映射，例如：

    * `displayName`
    * `preferredLanguage`
    * `name.formatted`
    * `externalId`

### 配置 Microsoft Entra ID 配置

1. 在 [Microsoft Entra 管理中心](https://entra.microsoft.com)中，在左侧菜单中选择 **Enterprise apps**。
2. 选择您的企业应用程序。
3. 在左侧菜单中选择 **Provisioning**。
4. 将 **Tenant URL** 设置为 `https://<LABEL_STUDIO_BASE_URL>/scim/v2/`。
5. 将 **Secret Token** 设置为与 Label Studio 中 Owner 账户关联的 [Legacy 令牌](access_tokens#Legacy-tokens)。

    必须是 Legacy 令牌，而不是 Personal Access Token。且必须与具有 Owner 角色的用户关联。
6. 在 **Mappings** 下，打开 **Provision Microsoft Entra ID Users**。
7. 移除除上面列出的受支持属性之外的所有属性映射。

    保留：
    * `emails[type eq "work"].value` → `userPrincipalName`
    * `userName` → `userPrincipalName`
    * `active` → `Switch([IsSoftDeleted], , "False", "True", "True", "False")`
    * `name.givenName` → `givenName`
    * `name.familyName` → `surname`
8. 在 **Mappings** 下，打开 **Provision Microsoft Entra ID Groups**，如果要使用基于组的角色分配，请确保其已启用。
9. 对于组配置，请在 Label Studio 中配置 SCIM 组设置（请参阅上面的[设置组映射](#set-up-group-mapping)）。
