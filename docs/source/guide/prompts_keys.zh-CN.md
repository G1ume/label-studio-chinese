---
title: 提示功能的模型提供商 API 密钥
short: API 密钥
tier: enterprise
type: guide
order: 0
order_enterprise: 229
meta_title: 模型提供商 API 密钥
meta_description: 添加用于提示功能的 API 密钥
section: Prompts
date: 2024-06-11 16:53:16
---

添加模型提供商 API 密钥有两种方式。

* 一种情况是，每个组织获得一个提供商连接，该连接提供对一组白名单模型的访问。示例包括：

    * OpenAI
    * Vertex AI
    * Gemini
    * Anthropic

* 第二种情况是，每个模型单独添加一个 API 密钥。示例包括：

    * Azure OpenAI
    * Azure AI Foundry
    * 自定义

!!! note
        如果您限制了对资源的网络访问，则在配置网络安全时可能需要将 HumanSignal 的 IP 地址（[SaaS 上的 IP 范围](saas#Outbound-Connections-IP-Addresses)）加入白名单。


一旦通过 API 密钥添加了模型，组织中能够访问提示功能的任何人都可以在执行提示时选择关联的模型。

您可以点击提示页面右上角的 **API 密钥**来查看已有的 API 密钥并添加新的密钥，这将打开**模型提供商 API 密钥**窗口：

![API 密钥按钮截图](/images/prompts/model_keys.png)

{% insertmd includes/base_models.md %}

添加密钥后，当您[起草提示](prompts_draft)时，所有支持的模型将出现在基础模型下拉菜单中。

## 添加模型提供商

!!! note
    此处配置的模型也将在您的[组织级别提供商](model_providers)中可用。

{% insertmd includes/model_keys.md %}
