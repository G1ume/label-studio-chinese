---
title: 设置 Redis 数据库项目存储
short: Redis
type: guide
tier: all
order: 156
order_enterprise: 156
meta_title: 设置 Redis 存储
meta_description: "如何为 Redis 数据库设置源存储和目标存储。"
section: "导入与导出"
parent: "storage"
parent_enterprise: "storage"
---

您也可以将任务和标注存储在 [Redis 数据库](https://redis.io/)中。您必须将任务和标注存储在不同的数据库中。如果您发现依赖基于文件的云存储连接对您的数据集来说速度较慢，那么您可能希望使用 Redis 数据库。

目前，此配置仅支持以默认模式和默认 IP 地址托管 Redis 数据库。

Label Studio 不会为您管理 Redis 数据库。有关托管和管理您自己的 Redis 数据库的详细信息，请参阅 [Redis Quick Start](https://redis.io/topics/quickstart)。由于 Redis 是内存数据库，保存在 Redis 中的数据不会持久化。为确保不丢失数据，请设置 [Redis 持久化](https://redis.io/topics/persistence)或使用其他方法持久化数据，例如通过 [Microsoft Azure](https://azure.microsoft.com/en-us/services/cache/) 或 [Amazon AWS](https://aws.amazon.com/redis/) 使用云中的 Redis。

## 源 Redis 存储的任务格式

Label Studio 仅支持 Redis 数据库的字符串值，这些值应以 JSON 格式表示 Label Studio 任务。

例如：

```
'ls-task-1': '{"image": "http://example.com/1.jpg"}'
'ls-task-2': '{"image": "http://example.com/2.jpg"}'
...
```

```
> redis-cli -n 1
127.0.0.1:6379[1]> SET ls-task-1 '{"image": "http://example.com/1.jpg"}'
OK
127.0.0.1:6379[1]> GET ls-task-1
"{\"image\": \"http://example.com/1.jpg\"}"
127.0.0.1:6379[1]> TYPE ls-task-1
string
```


## 创建源存储连接

在 Label Studio 中，打开您的项目，选择 **Settings > Cloud Storage > Add Source Storage**。

选择 **Redis Storage**，点击 **Next**。

#### 配置连接

填写以下字段，然后点击 **Test connection**：

<div class="noheader rowheader">

<table style="width: 100%; border-collapse: collapse;">
  <tr>
    <th style="width: 25%;">字段</th>
    <th>描述</th>
  </tr>

  <tr>
    <td>Storage Title</td>
    <td>输入一个名称以标识存储连接。</td>
  </tr>

  <tr>
    <td>Host</td>
    <td>
      输入托管数据库的服务器的 IP 地址，或输入 <code>localhost</code>。
    </td>
  </tr>

  <tr>
    <td>Port</td>
    <td>
      输入可用于访问数据库的端口。默认为 <code>6379</code>。
    </td>
  </tr>

  <tr>
    <td>Database Number (db)</td>
    <td>
      输入您要使用的数据库编号。默认为 <code>1</code>。
    </td>
  </tr>

  <tr>
    <td>Password</td>
    <td>
      可选，如果您的 Redis 实例需要身份验证，请输入服务器密码。
    </td>
  </tr>

</table>
</div>

#### 导入设置与预览

填写以下字段，然后点击 **Load preview** 以确保您正在同步正确的数据：

<div class="noheader rowheader">

| | |
| --- | --- |
| Path to files | 可选，指定数据库的路径。用作键前缀，此路径下的值将被扫描以获取任务。 |
| Import Method | 选择是为数据库中的每个文件创建一个任务，还是使用 JSON/JSONL/Parquet 文件定义每个任务的数据。 |
| File Name Filter | 指定一个正则表达式来过滤数据库对象。使用 `.*` 收集所有对象。 |
| Scan all sub-folders | 启用此选项可对数据库内的子文件夹执行递归扫描。 |

</div>

#### 审核与确认

如果一切看起来正确，点击 **Save & Sync** 立即同步，或点击 **Save** 保存设置稍后同步。

!!! info Tip
    您也可以使用 API [同步导入存储](https://api.labelstud.io/api-reference/api-reference/import-storage/redis/sync)。

## 创建目标存储连接

在 Label Studio 中，打开您的项目，选择 **Settings > Cloud Storage > Add Target Storage**。

选择 **Redis Storage**，点击 **Next**。

填写以下字段：

<div class="noheader rowheader">

<table style="width: 100%; border-collapse: collapse;">

  <tr>
    <td style="width: 25%;">Storage Title</td>
    <td>输入一个名称以标识存储连接。</td>
  </tr>

  <tr>
    <td>Path</td>
    <td>
      可选，指定数据库的路径。用作导出标注的键前缀。
    </td>
  </tr>

  <tr>
    <td>Host</td>
    <td>
      输入托管数据库的服务器的 IP 地址，或输入 <code>localhost</code>。
    </td>
  </tr>

  <tr>
    <td>Port</td>
    <td>
      输入可用于访问数据库的端口。默认为 <code>6379</code>。
    </td>
  </tr>

  <tr>
    <td>Database Number (db)</td>
    <td>
      输入您要使用的数据库编号。默认为 <code>2</code>。您必须使用与源存储不同的数据库编号。
    </td>
  </tr>

  <tr>
    <td>Password</td>
    <td>
      可选，如果您的 Redis 实例需要身份验证，请输入服务器密码。
    </td>
  </tr>

</table>
</div>

添加存储后，点击 **Sync**。

!!! info Tip
    您也可以使用 API [同步导出存储](https://api.labelstud.io/api-reference/api-reference/export-storage/redis/sync)。

## 使用 Label Studio API 添加存储

您也可以使用 API 以编程方式创建连接。[请参阅我们的 API 文档。](https://api.labelstud.io/api-reference/introduction/getting-started)
