---
title: 活动日志
short: 活动日志
tier: enterprise
type: guide
order: 0
order_enterprise: 354
meta_title: Label Studio 中的用户活动日志
meta_description: 如何在 Label Studio 中查看用户活动日志
section: "管理您的组织"
date: 2024-02-05 16:40:16
---

您可以在**组织 > 活动日志**页面查看用户日志。只有具有管理员或所有者角色的用户才能访问此页面。

!!! note
    在 Label Studio Enterprise Cloud（`app.humansignal.com`）中，活动日志保留 180 天。

    在 Label Studio Enterprise 本地部署版中，活动日志的保留期限没有限制。

日志包括用户 ID、IP 地址、时间戳以及通过记录 API 请求来记录的操作类型。您可以点击 API 请求来了解有关每个操作的更多信息。

!!! note
    在某些情况下，点击 API 请求不会返回额外信息。这通常是因为该请求包含敏感信息，例如密码。

您可以按以下条件筛选日志：

* 用户
* 项目
* 请求类型（GET、POST、PATCH、DELETE）
* 日期范围
* 请求关键词
