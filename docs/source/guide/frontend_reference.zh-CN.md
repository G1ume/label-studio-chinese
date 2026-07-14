---
title: 前端参考
short: 前端
type: guide
tier: all
order: 416
order_enterprise: 416
meta_title: 前端库参考
meta_description: 在自定义的机器学习或数据科学应用工作流中集成 Label Studio 前端的参考文档。
section: "集成与扩展"
---

Label Studio 前端（LSF）包含多种 UI 选项和回调函数，你可以在使用自定义标注后端实现前端时使用，也可以在自定义 Label Studio 界面时使用。

!!! attention
    自 [Label Studio 1.11.0](https://github.com/HumanSignal/label-studio/releases/tag/1.11.0) 起，Label Studio 前端已作为一个独立库被弃用，不再以独立分发形式提供支持。有关在 Label Studio 中使用前端库的信息，请参阅 [README](https://github.com/HumanSignal/label-studio/blob/develop/web/libs/editor/README.md)。

## LSF 1.0.0 版本的更新

!!! warning
    LSF 1.0.0 版本与早期版本的 Label Studio 不兼容。

如果你在自定义后端中使用 LSF，你必须对所使用 API 回调进行如下修改：

| 0.9.1 及更早版本中的回调 | 1.0.0 中重命名后的回调 |
| ----------------------------- | ------------------------- |
| onSubmitCompletion            | onSubmitAnnotation        |
| onUpdateCompletion            | onUpdateAnnotation        |
| onDeleteCompletion            | onDeleteAnnotation        |

如果你依赖已完成 Label Studio 任务的特定格式，[Label Studio 的标注格式](export.html#Raw-JSON-format-of-completed-tasks)也已更新。

## 实现 Label Studio 前端

```javascript
var labelStudio = new LabelStudio("editor", options);
```

初始化 Label Studio 1.0.0 版本实例时，可识别以下选项。

## 选项

### config

默认值：`null`

数据类型：`string`

基于 XML 的标注界面配置。此配置依赖于任务的 `data` 字段。
有关更多信息，请参阅[可自定义标签](/tags)。

### interfaces

默认值：`null`

数据类型：`array`

要显示的 UI 元素集合。可用接口：

- `panel` - 为当前任务启用导航面板，包含以下按钮：撤销、重做和重置。
- `update` - 显示一个按钮，用于在提交后更新当前任务。
- `submit` - 显示一个按钮，用于提交或更新当前标注。
- `skip` - 显示一个按钮，用于跳过当前任务。
- `controls` - 启用包含控件（`submit`、`update`、`skip`）的面板。
- `infobar` - 显示信息按钮。
- `topbar` - 在 Label Studio UI 中列出顶级项目的标注界面。
- `instruction` - 用于[说明](#description)的按钮。
- `side-column` - 在 Label Studio UI 的左侧或右侧显示一列。
- `annotations:history` - 显示标注历史按钮。
- `annotations:tabs` - 显示标注标签页按钮。
- `annotations:menu` - 显示标注菜单按钮。
- `annotations:current` - 显示当前标注按钮。
- `annotations:add-new` - 显示添加新标注的按钮。
- `annotations:delete` - 显示删除当前标注的按钮。
- `annotations:view-all` - 显示查看所有标注的按钮。
- `predictions:tabs` - 显示预测标签页。
- `predictions:menu` - 显示预测菜单。
- `auto-annotation` - 显示自动标注。
- `edit-history` - 显示编辑历史。

### messages

默认值：`null`

数据类型：`object`

用于不同操作的消息文本

```javascript
{
  DONE: "Done!",
  NO_COMP_LEFT: "No more annotations",
  NO_NEXT_TASK: "No more data available for labeling",
  NO_ACCESS: "You don't have access to this task"
}
```

- `DONE` - 任务提交到服务器后显示
- `NO_COMP_LEFT` - 如果没有更多标注时显示
- `NO_NEXT_TASK` - 没有下一个要加载的任务时显示
- `NO_ACCESS` - 无法访问提供的任务时显示

### description

默认值：`No description`

数据类型：`string`

当前任务的描述。

### task

任务数据

默认值：`null`

数据类型：`object`

```json
{
  id: 1,
  load: false
  },
  data: {
    text: "Labeling text..."
  },
  annotations: [],
  predictions: [],
}
```

#### id

数据类型：`integer`

默认值：`null`

#### data

#### annotations

数据类型：`array`

标注数组。更多信息请参阅[标注文档](export.html#Raw-JSON-format-of-completed-tasks)。

#### predictions

数据类型：`array`

预测数组。结构与完成项或标注类似。更多信息请参阅[标注文档](export.html#Raw-JSON-format-of-completed-tasks)和[导入预测标签指南](predictions.html)。

### user

用户数据

数据类型：`object`

```json
{
  "pk": 1,
  "firstName": "Stanley",
  "lastName": "Kubrick"
}
```

#### pk

数据类型：`number`

#### firstName

数据类型：`string`

#### lastName

数据类型：`string`

## 事件系统

LSF 有一个内置的事件系统，允许你监听事件并触发自定义操作。你可以在 Label Studio 实例初始化之后的任何时间订阅或取消订阅事件。

### 使用事件

#### 订阅事件

```javascript
const callback = () => {
  console.log("Event triggered");
};
labelStudio.on("event", callback);
```

#### 取消订阅事件

```javascript
const callback = () => {
  console.log("Event triggered");
};
labelStudio.off("event", callback);
```

!!! note
    要能够取消订阅事件，你必须将相同的回调函数引用传递给 `off` 方法。

## 可用事件

#### 顶层事件

此事件组包含顶层事件。这些事件与 LSF 的任何内部实体无关。

### `labelStudioLoad`

Label Studio 实例已加载。

!!! warning
    此事件在插件中不起作用。

**事件处理函数参数**

| 参数 | 类型 | 描述 |
| ------------- | -------- | ------------------------ |
| `labelStudio` | `Object` | Label Studio 实例 |

### `storageInitialized`

内部存储已初始化。

!!! warning
    此事件在插件中不起作用。

**事件处理函数参数**

| 参数 | 类型 | 描述 |
| ------------- | -------- | ------------------------ |
| `labelStudio` | `Object` | Label Studio 实例 |

#### 任务事件

此事件组包含与任务相关的事件。

### `skipTask`

用户点击了"跳过"按钮。

**事件处理函数参数**

| 参数 | 类型 | 描述 |
| ------------- | -------- | ------------------------------------------- |
| `labelStudio` | `Object` | Label Studio 实例 |
| `payload` | `Object` | 跳过操作期间发送的附加数据 |

### `unskipTask`

用户点击了"取消跳过"按钮。

| 参数 | 类型 | 描述 |
| ------------- | -------- | ------------------------ |
| `labelStudio` | `Object` | Label Studio 实例 |

### `nextTask`

用户点击了"下一个"（向右箭头）按钮。

| 参数 | 类型 | 描述 |
| -------------- | --------- | ---------------------------------------------- |
| `labelStudio` | `Object` | Label Studio 实例 |
| `taskId` | `Number?` | 历史记录中下一个任务的 ID |
| `annotationId` | `Number?` | 要在任务中选择的标注 ID |

### `prevTask`

用户点击了"上一个"（向左箭头）按钮。

| 参数 | 类型 | 描述 |
| -------------- | --------- | ---------------------------------------------- |
| `labelStudio` | `Object` | Label Studio 实例 |
| `taskId` | `Number?` | 历史记录中上一个任务的 ID |
| `annotationId` | `Number?` | 要在任务中选择的标注 ID |

### `submitDraft`

草稿已发送到服务器。

| 参数 | 类型 | 描述 |
| ------------- | --------- | -------------------------------- |
| `labelStudio` | `Object` | Label Studio 实例 |
| `annotation` | `Object` | 当前标注 |
| `params` | `Object?` | 随草稿发送的额外参数 |

#### 标注事件

此事件组包含与标注相关的事件。

### `beforeSaveAnnotation`
标注即将作为 `submit` 或 `update` 操作的结果被保存。
从此事件返回 `false` 将阻止保存标注。

| 参数 | 类型 | 描述 |
|------------------|----------|----------------------------------------------------------------------------------------------|
| `labelStudio` | `Object` | Label Studio 实例 |
| `annotation` | `Object` | 当前标注 |
| `payload` | `Object` | 附加信息 |
| `payload.event` | `string` | 指示即将执行哪个事件（`submitAnnotation`、`updateAnnotation` 等） |


### `submitAnnotation`

标注已提交。

| 参数 | 类型 | 描述 |
| ------------- | -------- | ------------------------ |
| `labelStudio` | `Object` | Label Studio 实例 |
| `annotation` | `Object` | 当前标注 |

### `updateAnnotation`

标注已更新。

| 参数 | 类型 | 描述 |
| ------------- | -------- | ------------------------ |
| `labelStudio` | `Object` | Label Studio 实例 |
| `annotation` | `Object` | 当前标注 |

### `selectAnnotation`

标注被选中。

| 参数 | 类型 | 描述 |
|-----------------------|-----------|-----------------------------------------------|
| `annotation` | `Object` | 当前标注 |
| `previousAnnotation` | `Object` | 上一个标注 |
| `payload` | `Object?` | 附加信息 |
| `payload.fromViewAll` | `boolean` | 如果 ViewAll 刚刚被关闭则为 `true` |


### `deleteAnnotation`

标注被删除。

| 参数 | 类型 | 描述 |
| ------------- | -------- | ------------------------ |
| `labelStudio` | `Object` | Label Studio 实例 |
| `annotation` | `Object` | 当前标注 |

### `groundTruth`

标注被设置为基准真值（Ground Truth）（点击了星形按钮）。

| 参数 | 类型 | 描述 |
| ---------------- | --------- | -------------------------------- |
| `store` | `Object` | Label Studio 实例 |
| `labelStudio` | `Object` | Label Studio 实例 |
| `params` | `Object` | |
| `params.isDirty` | `Boolean` | 如果标注已更改则为 `true` |
| `params.entity` | `Object` | 当前标注 |

### `selectHistory`
标注历史中的某个步骤被选中。

| 参数 | 类型 | 描述 |
|---------------|----------|-----------------------------|
| `labelStudio` | `Object` | Label Studio 实例 |
| `annotation` | `Object` | 当前标注 |
| `historyItem` | `Object` | 当前历史项 |

#### 区域事件

此事件组包含与区域相关的事件。区域是用于分割任务（如图像分割、音频分割等）的特殊实体。

### `entityCreate`

区域已创建。

| 参数 | 类型 | 描述 |
| -------- | -------- | -------------------- |
| `region` | `Object` | 新创建的区域 |

### `entityDelete`

区域被删除。

| 参数 | 类型 | 描述 |
| -------- | -------- | -------------------- |
| `region` | `Object` | 新创建的区域 |

{% collapse "回调函数（已弃用）" %}

回调函数可用于根据用户与界面的交互来执行操作。例如，label-studio 服务器使用回调函数与 API 通信。在初始化实例时将其与其他选项一起传递。

### onSubmitAnnotation

数据类型：`function`

当 `submit` 按钮被按下时调用。`ls` 是 label studio 实例，`annotation` 是当前标注的值。

#### 示例

```javascript
onSubmitAnnotation: function(ls, annotation) {
  console.log(annotation)
}
```

### onUpdateAnnotation

数据类型：`function`

当 `update` 按钮被按下时调用。`ls` 是 label studio 实例，`annotation` 是当前标注的值。

#### 示例

```javascript
onUpdateAnnotation: function(ls, annotation) {
  console.log(result)
}
```

### onDeleteAnnotation

数据类型：`function`

当 `delete` 按钮被按下时调用。`ls` 是 label studio 实例，`annotation` 是当前标注的值。

#### 示例

```javascript
onDeleteAnnotation: function(ls, annotation) {
  console.log(result)
}
```

### onEntityCreate

数据类型：`function`

当新区域被标注时调用，例如创建了一个新的边界框。`region` 是创建的对象。

#### 示例

```javascript
onEntityCreate: function(region) {
  console.log(region)
}
```

### onEntityDelete

数据类型：`function`

当现有区域被删除时调用。`region` 是对象本身。

#### 示例

```javascript
onEntityDelete: function(region) {
  console.log(region)
}
```

### onSkipTask

数据类型：`function`

当 `skip` 按钮被按下时调用。`ls` 是 label studio 实例。

#### 示例

```javascript
onSkipTask: function(ls) {
  console.log(result)
}
```

### onLabelStudioLoad

数据类型：`function`

当 Label Studio 完全加载并准备好进行标注时调用。`ls` 是 label studio 实例。

#### 示例

```javascript
onLabelStudioLoad: function(ls) {
  console.log(result)
}
```

{% endcollapse %}
