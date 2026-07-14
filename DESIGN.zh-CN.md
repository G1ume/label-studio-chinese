# Label Studio 设计系统

## 目录

- [品牌与设计原则](#品牌与设计原则)
- [内容指南](#内容指南)
- [无障碍标准](#无障碍标准)
- [设计令牌](#设计令牌)
- [组件库](#组件库)
- [样式指南](#样式指南)
- [组件开发](#组件开发)
- [常用模式](#常用模式)
- [组件复用与最佳实践](#组件复用与最佳实践)
- [应避免的反模式](#应避免的反模式)
- [快速参考](#快速参考)

---

## 品牌与设计原则

**个性**: 人性化、乐观、轻松、可靠、适应性强、精益求精。

**设计原则**: 解决人类问题、增强人类能力、值得信赖、透明、整体性、可预测。

---

## 内容指南

### 语气与语调

- 对话式的、开放的、清晰的、实用的
- 非正式的、乐观的、积极的、友好的
- 用户应感到被鼓励和激励

### 写作风格

- 使用简洁的句子，目标为初中阅读水平或更低
- 在应用中为元素使用一致的名称（例如，如果您在标注中使用"类别"，请始终使用"类别"，而非"类型"或"组"）
- 尽可能使用主动语态以确保清晰（例如，"审核员审核并更新标注"，而非"标注由审核员审核"）
- 标题、输入标签和控件使用句子大小写（例如，"选择详情"、"邮箱地址"、"启用通知"）
- 按钮和导航项使用标题大小写（例如，"保存更改"、"上传数据集"、"查看文档"）
- 使用缩写使语言更具对话感（例如，使用"can't"、"don't"、"it's"，而非"cannot"、"do not"、"it is"）

### UI 文本指南

**错误消息**:
- 清晰、具体、有帮助
- 告诉用户出了什么问题以及如何修复

**反馈消息**:
- 积极、鼓励、信息丰富

**标签和按钮**:
- 按钮使用动作动词（保存、取消、上传）
- 标签保持简洁但描述性
- 按钮使用标题大小写
- 标签使用句子大小写

**链接**:
- 使用描述性的链接文本，指明目标位置

### 词汇

关于领域术语（项目、任务、标注等），请参见 `terminology.mdc` 光标规则。

---

## 无障碍标准

需要符合 **WCAG 2.1 AA 级**标准。

### 关键要求

**键盘导航**:
- ✅ 所有交互元素必须可通过键盘访问
- ✅ 界面中正确的 Tab 顺序
- ✅ 可见的焦点指示器（最小 3:1 对比度）
- ✅ 支持 ESC、Enter、Space、方向键
- ✅ 无焦点陷阱（模态框除外）
- ✅ 模态框/对话框关闭后焦点返回到适当位置

**视觉**:
- 最小 4.5:1 的文本对比度
- 绝不仅用颜色作为传达信息的唯一手段
- 界面可缩放至 200% 而不丢失内容
- 所有图片都有替代文本

**结构**:
- 全程使用语义化 HTML
- 需要时使用 ARIA 属性
- 正确的标题层次结构（H1 → H2 → H3）
- 表单标签与输入框关联
- 通过 `aria-describedby` 将错误消息链接到字段
- 动态内容使用 ARIA 实时区域

---

## 设计令牌

**位置**: `web/libs/ui/src/tokens/tokens.prefix.css`

**始终使用语义令牌**，而非数值：

| 类别 | ✅ 使用语义 | ❌ 不使用数值 |
|------|----------------|---------------------|
| **间距** | `p-tight`、`m-base`、`gap-wide` | `p-200`、`m-400`、`gap-600` |
| **排版** | `text-body-medium`、`text-label-small` | `text-16`、`text-14` |
| **颜色** | `bg-primary-surface`、`text-neutral-content` | `bg-grape-600`、`text-sand-800` |

### 颜色令牌

**语义颜色类别**:
- **Primary**（主色）: 品牌颜色（grape/blue）
- **Neutral**（中性色）: 灰度颜色（sand）
- **Positive**（正向）: 成功状态（kale/green）
- **Negative**（负向）: 错误状态（persimmon/red）
- **Warning**（警告）: 警告状态（canteloupe/orange）
- **Accent**（强调色）: 装饰颜色（grape、blueberry、kale、kiwi、mango、canteloupe、persimmon、plum、fig、sand）

**颜色令牌结构**:
```css
/* 表面颜色（用于交互元素的背景） */
--color-primary-surface
--color-primary-surface-hover
--color-primary-surface-active

/* 内容颜色（用于文本） */
--color-neutral-content
--color-neutral-content-subtle
--color-neutral-content-subtler
--color-neutral-content-subtlest  /* 用于禁用文本 */

/* 背景颜色（用于页面/容器背景） */
--color-neutral-background
--color-primary-background

/* 边框颜色 */
--color-neutral-border
--color-primary-border-subtle

/* 图标颜色 */
--color-primary-icon
--color-negative-icon
```

**强调色**（标签、图表、分类）:
- 默认: `-bold` 文本, `-subtlest` 背景
- 悬停: `-bold` 文本, `-subtle` 背景
- 激活: `-subtlest` 文本, `-base` 背景
- 图表: `-base` 背景

### 间距令牌

**语义尺度**:
- `--spacing-tightest` / `tightest`: 2px
- `--spacing-tighter` / `tighter`: 4px
- `--spacing-tight` / `tight`: 8px
- `--spacing-base` / `base`: 16px
- `--spacing-wide` / `wide`: 24px
- `--spacing-wider` / `wider`: 32px
- `--spacing-widest` / `widest`: 40px

### 排版令牌

**语义尺度**:
- `text-body-smallest` / `--font-size-body-smallest`: 10px
- `text-body-smaller` / `--font-size-body-smaller`: 12px
- `text-body-small` / `--font-size-body-small`: 14px
- `text-body-medium` / `--font-size-body-medium`: 16px
- `text-label-small` / `--font-size-label-small`: 14px
- `text-label-medium` / `--font-size-label-medium`: 16px
- `text-title-small` / `--font-size-title-small`: 18px
- `text-title-medium` / `--font-size-title-medium`: 20px
- `text-title-large` / `--font-size-title-large`: 24px

### 深色模式

使用语义令牌时自动支持深色模式。切勿使用硬编码颜色、数值令牌（grape-600）或内联颜色样式。


---

## 组件库

### 位置

所有共享 UI 组件位于 `@humansignal/ui` 包中：
- 源码: `web/libs/ui/src/lib/`
- 导入: `import { Button, Badge } from '@humansignal/ui';`

### 组件发现

浏览 `@humansignal/ui` 中的可用组件：
- **源码**: `web/libs/ui/src/lib/`
- **Storybook**: 运行 `yarn nx storybook storybook`（端口 4400）

**关键**: 创建新组件前务必先检查 `@humansignal/ui`。使用 `Message` 显示信息框，`EmptyState` 显示空状态，`Button` 而非 `<button>`。

### 导入模式

```tsx
// UI 组件
import { Button, Badge, Message } from '@humansignal/ui';

// 图标
import { IconCheck, IconCross } from '@humansignal/icons';

// 核心工具
import { cn } from '@humansignal/core';
```

### shadcn/ui 集成

部分组件基于 shadcn/ui 构建。始终通过 `@humansignal/ui` 导入，切勿从 `/src/shad/` 导入。

---

## 样式指南

### Tailwind CSS

完整指南请参见 `tailwind.mdc`。

**使用语义工具类**: `p-tight`、`bg-primary-surface`、`text-body-medium`（而非 `p-200`、`bg-grape-600`、`text-16`）

**响应式**: `sm:`、`md:`、`lg:` 工具类

### CSS 模块

将 `.module.css` 与组件放在同一目录。

**组件令牌模式**:
```css
.base {
  --background-color: var(--color-primary-surface);
  --text-color: var(--color-primary-surface-content);
  background-color: var(--background-color);
  color: var(--text-color);
}

.variant-neutral {
  --background-color: var(--color-neutral-surface);
  --text-color: var(--color-neutral-content);
}
```

**CSS 中使用 Tailwind**: `@apply flex items-center gap-tight;`

**Canvas 元素**:

对于无法使用 CSS 变量的 Canvas/JS 渲染，使用 `getTokenColor`：

```tsx
import { getTokenColor } from '@humansignal/ui';
ctx.fillStyle = getTokenColor('--color-primary-surface');
```

---

## 组件开发

完整的 React 模式请参见 `react.mdc`。

### 文件结构

- `@humansignal/ui` 组件: kebab-case（`button.tsx`、`empty-state.tsx`）
- 应用组件: 允许 PascalCase（`DataManager.tsx`）
- 同目录放置: `.tsx`、`.module.css`、`.stories.tsx`、`.test.tsx`
- 每个组件必须有 Storybook 故事

---

## 常用模式

### 组件变体

**状态**: `primary`、`neutral`、`positive`、`negative`、`warning`、`gradient`
**尺寸**: `smaller`（24px）、`small`（32px）、`medium`（40px）、`large`（48px+）
**外观**: `filled`（实心）、`outlined`（边框）、`string`（仅文本）

### 禁用状态

禁用文本使用 `neutral-content-subtlest`：`<button disabled className="text-neutral-content-subtlest">`

### 加载状态

使用 `waiting` 属性：`<Button waiting={isLoading}>保存</Button>`

### 空状态

始终使用 `EmptyState`，包含图标、标题、描述、操作：
```tsx
<EmptyState icon={<IconInbox />} title="暂无任务" description="..." actions={<Button>创建</Button>} />
```

### 模态框模式

**底部操作**: 所有 CTA 和导航按钮放在底部（默认：右对齐；"上一步"按钮：左对齐）

**按钮视觉层次**: 参见"按钮层次"章节。

**破坏性操作**: 需要确认。高风险操作需要输入验证（"DELETE" 或实体名称）。

**模态框堆叠**: 避免模态框套模态框。改用多步骤模态框或抽屉。

```tsx
// 右对齐底部（默认）
<Modal.Footer align="right">
  <Button variant="neutral" look="outlined">取消</Button>
  <Button variant="primary" look="filled">保存更改</Button>
</Modal.Footer>

```

---

## 组件复用与最佳实践

### 组件选择

创建新组件前，检查 `@humansignal/ui` 和 Storybook。

**始终使用现有组件**:
- `<Button>` 而非 `<button>`
- `<Message>` 用于信息框
- `<Tooltip>` 用于工具提示
- `<Modal>` 用于模态框
- `<EmptyState>` 用于空状态

### 命名约定

- `@humansignal/ui`: kebab-case（`button.tsx`）
- 应用组件: 允许 PascalCase（`DataManager.tsx`）
- Props: `ComponentNameProps`

### 值与令牌

绝不硬编码值。使用语义令牌：`text-primary-content p-tight text-body-medium`

创建组件令牌：
```css
.component {
  --component-bg: var(--color-neutral-surface);
  background: var(--component-bg);
}
```

尺寸优先使用 `rem`，必要时可使用 `px`。

### 按钮层次

**每个屏幕一个 primary/filled 按钮**（单个 CTA）。

**按对齐方式的视觉层次**:
- 右对齐: 从右到左（主按钮在最右）: `[取消] [保存]`
- 左对齐: 从左到右（主按钮在最左）: `[下一步] [跳过]`

### 响应式设计

确保布局适配: `flex-col md:flex-row`、`p-tight md:p-base`、`text-title-medium md:text-headline-small`


### 保存设置

设置/配置使用显式的"保存"按钮（而非自动保存）。

例外: 草稿内容、偏好设置、即时切换。


---

## 应避免的反模式

- ❌ 数值令牌（`p-200`、`bg-grape-600`）
- ❌ 硬编码值（`color: #4C5FA9`）
- ❌ 内联样式（破坏深色模式）
- ❌ 存在可比较组件时重复创建
- ❌ 使用 `<button>` 而非 `<Button>`
- ❌ 直接从 `/src/shad/` 导入
- ❌ 每个屏幕多个 primary/filled 按钮
- ❌ 缺少键盘导航
- ❌ 缺少焦点指示器
- ❌ 仅用颜色传达信息
- ❌ 对比度不足（< 4.5:1）
- ❌ 非语义化 HTML
- ❌ 非响应式布局

---

## 快速参考

### 关键文件

- 组件: `web/libs/ui/src/lib/`
- 令牌: `web/libs/ui/src/tokens/tokens.prefix.css`
- 图标: `@humansignal/icons`
- Storybook: `yarn nx storybook storybook`

### 相关文档

- `react.mdc` - 组件结构、Hooks、状态管理
- `tailwind.mdc` - 工具类、响应式设计
- `typescript.mdc` - 类型约定
- `frontend-unit-tests.mdc` - 测试模式
- `terminology.mdc` - 领域术语

### 常用导入

```tsx
import { Button, Message, EmptyState } from '@humansignal/ui';
import { IconCheck } from '@humansignal/icons';
import { cn } from '@humansignal/core';
```

### 令牌示例

`p-tight`、`m-base`、`gap-wide`、`text-body-medium`、`bg-primary-surface`、`text-neutral-content`、`text-neutral-content-subtlest`（禁用状态）

---
