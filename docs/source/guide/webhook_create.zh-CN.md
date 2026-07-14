---
title: 在 Label Studio 中为 Webhook 创建自定义事件
short: Webhook 开发
type: guide
tier: all
order: 410
order_enterprise: 410
meta_title: 在 Label Studio 中创建自定义 Webhook
meta_description: Label Studio 文档：创建自定义 webhook 事件触发器，以在 Label Studio 和您的机器学习流水线之间构建自定义集成
section: "集成与扩展"
parent: "webhooks"
parent_enterprise: "webhooks"

---

如果您想在 Label Studio 中为 webhook 触发自定义事件，可以扩展 webhook 事件模型。


## 创建自定义 webhook 事件

要创建自定义 webhook 事件，请在 `WebhookActions` 模型中添加您自己的操作。

例如：

```python
class WebhookAction(models.Model):
    ...
    SOMETHING_HAPPENED = 'SOMETHING_HAPPENED'
    ...
    ACTIONS = {
        SOMETHING_HAPPENED: {
            'name': _('Something happened'),
            'description': _("A thing happened. We wanted to let you know."),
            'key': 'something',
        },
        ...
    ...
```

在 `WebhookAction` 类中声明操作及其关联属性和负载详情后，在代码中相应位置调用事件操作。例如：

```
...python
result = do_something()
emit_webhooks(organization, WebhookAction.SOMETHING_HAPPENED, {'something': [result]})
...
```

您可以使用 `Organization.objects.first()` 来获取组织详情。

### 使用 Python 函数调用事件操作
您可以使用以下多个函数来调用事件操作。请参考下表：

| Python 函数 | 使用场景 | 补充说明 |
| --- | --- | --- |
| `get_active_webhooks()` | 获取所有活跃的 webhook。 | |
| `run_webhook()` | 运行一个 webhook 并传递负载数据。 | |
| `emit_webhooks()` | 为某个操作的所有 webhook 发送请求。 | |
| `emit_webhooks_for_instances()` | 发送带 webhook 请求的序列化实例。 | 您必须在 `WebhookAction.ACTIONS` 模型中声明 `serializer`。|


### 在 API 源代码中使用装饰器调用事件操作

您可以在 CRUD REST 方法中使用装饰器向 webhook 发送事件操作。可以使用以下装饰器：

| 装饰器语法 | 使用场景 | 说明 |
| --- | --- | --- |
| `@api_webhook()` | `POST`/`PUT`/`PATCH` 请求 | 期望返回带有 `id` 的响应，并在请求后使用 `.get_object()` 函数发送该信息。 |
| `@api_webhook_for_delete()` | `DELETE` | 在成功删除操作后仅发送 `id` 字段。 |
