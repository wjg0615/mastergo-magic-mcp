---
description: 设计稿转代码还原度提升规范
---

# 设计稿转代码还原度提升规范

本文档定义了提高 MasterGo 设计稿转代码还原度的设计规范。

---

## 一、设计稿规范

### 1.1 命名规范

#### 1.1.1 图层命名

| 类别 | 规范要求 | 示例 | 说明 |
|------|----------|------|------|
| 容器层 | 语义化前缀 | `container-main`、`section-hero` | 表示布局容器 |
| 组件层 | 组件名+变体 | `btn-primary`、`btn-secondary` | 便于识别组件类型 |
| 状态层 | 组件名+状态 | `btn-hover`、`btn-active`、`btn-disabled` | 明确交互状态 |
| 图标层 | icon-前缀 | `icon-close`、`icon-arrow-right` | 统一图标命名 |
| 图片层 | img-前缀 | `img-avatar`、`img-banner` | 区分图片资源 |

#### 1.1.2 组件命名对照表

| 设计组件名 | 说明 |
|------------|------|
| btn / button | 按钮组件 |
| input / textField | 输入框组件 |
| card / panel | 卡片组件 |
| modal / dialog | 弹窗组件 |
| dropdown / select | 下拉选择组件 |
| tab / tabPanel | 标签页组件 |
| nav / navbar | 导航栏组件 |
| footer | 页脚组件 |

#### 1.1.3 状态命名后缀

```
组件名-state形式：
- Button-default    默认状态
- Button-hover      悬停状态
- Button-active     激活状态
- Button-disabled   禁用状态
- Button-focus      聚焦状态
- Button-loading    加载状态
```

---

### 1.2 布局规范

#### 1.2.1 必须使用 Auto Layout

**原因**：Auto Layout 能生成准确的布局代码，大幅提升还原度。

| 布局场景 | Auto Layout 设置 |
|----------|------------------|
| 水平排列 | Direction: Horizontal |
| 垂直排列 | Direction: Vertical |
| 两端对齐 | Primary: Space Between |
| 居中对齐 | Primary: Center |
| 等间距 | Primary: Space Around |
| 自动换行 | Wrap: Wrap |

#### 1.2.2 禁止事项

| 禁止行为 | 原因 | 替代方案 |
|----------|------|----------|
| 滥用绝对定位 | 生成代码难以维护 | 使用 Auto Layout |
| 随意拖拽位置 | 布局结构混乱 | 使用对齐工具 |
| 忽略层级关系 | 组件边界不清 | 明确父子层级 |
| 固定宽度过多 | 响应式适配困难 | 使用相对宽度 |

#### 1.2.3 间距系统

采用 **8px 基准网格系统**：

```
间距值 = 8 × n (n 为整数)

推荐间距值：
- xs:  4px   (n=0.5)  紧凑元素间距
- sm:  8px   (n=1)    同组元素间距
- md:  16px  (n=2)    模块内间距
- lg:  24px  (n=3)    模块间间距
- xl:  32px  (n=4)    区块间间距
- 2xl: 48px  (n=6)    大区块间距
```

---

### 1.3 组件化规范

#### 1.3.1 组件变体定义

为同一组件的不同状态创建变体：

```
Button 组件变体示例：
├── Button (主组件)
│   ├── Variants
│   │   ├── Primary (主要按钮)
│   │   ├── Secondary (次要按钮)
│   │   ├── Dashed (虚线按钮)
│   │   ├── Text (文字按钮)
│   │   └── Link (链接按钮)
│   ├── Sizes
│   │   ├── Small (小尺寸)
│   │   ├── Medium (中等尺寸)
│   │   └── Large (大尺寸)
│   └── States
│       ├── Default (默认)
│       ├── Hover (悬停)
│       ├── Active (激活)
│       ├── Disabled (禁用)
│       └── Loading (加载)
```

#### 1.3.2 组件属性标注

每个组件需标注以下属性：

| 属性类别 | 标注内容 | 示例 |
|----------|----------|------|
| 尺寸 | width, height, padding, margin | `padding: 12px 24px` |
| 边框 | border-width, border-style, border-color, border-radius | `border: 1px solid #d9d9d9` |
| 背景 | background-color, background-image, gradient | `background: #ffffff` |
| 文字 | font-size, font-weight, line-height, color | `font: 14px/1.5 sans-serif` |
| 效果 | box-shadow, opacity, transform | `shadow: 0 2px 8px rgba(0,0,0,0.08)` |
| 交互 | cursor, transition, animation | `transition: all 0.3s` |

#### 1.3.3 组件复用原则

| 优先级 | 策略 | 适用场景 |
|--------|------|----------|
| 1 | 直接使用 | 功能完全匹配 |
| 2 | 组件组合 | 通过组合现有组件实现 |
| 3 | 组件扩展 | 基于现有组件添加功能 |
| 4 | 重新开发 | 以上方法均不可行 |

---

### 1.4 交互标注规范

#### 1.4.1 页面跳转标注

使用 MasterGo 的交互原型功能标注：

```
交互类型：
- navigation: 页面跳转
- overlay: 弹出层
- scroll: 滚动到指定位置
- back: 返回上一页
```

#### 1.4.2 动画效果标注

| 动画类型 | 标注内容 |
|----------|----------|
| 淡入淡出 | duration, easing |
| 滑动 | direction, distance |
| 缩放 | scale-from, scale-to |
| 展开/收起 | height-change |

#### 1.4.3 交互状态说明

每个可交互元素需说明：

```
按钮交互说明示例：

Primary Button
- 默认状态：背景 primary，文字白色
- 悬停状态：背景 primary-hover，光标 pointer
- 激活状态：背景 primary-active，轻微下沉
- 禁用状态：背景 gray，光标 not-allowed，透明度 0.5
- 加载状态：显示 loading 图标，禁用点击

过渡动画
- 所有状态切换：duration 0.2s，easing ease-in-out
```

## 二、常见问题与解决方案

### 2.1 布局问题

| 问题 | 原因 | 解决方案 |
|------|------|----------|
| 元素位置偏移 | 未使用 Auto Layout | 重新设置自动布局 |
| 间距不一致 | 随意拖拽 | 使用间距系统对齐 |
| 响应式失效 | 固定宽度过多 | 使用相对单位和百分比 |

### 2.2 样式问题

| 问题 | 原因 | 解决方案 |
|------|------|----------|
| 颜色不统一 | 未使用 Token | 定义并使用 Design Token |
| 字体层级混乱 | 未定义字体系统 | 建立字体层级规范 |
| 圆角不一致 | 无圆角规范 | 统一圆角 Token |

### 2.3 组件问题

| 问题 | 原因 | 解决方案 |
|------|------|----------|
| 状态缺失 | 变体不完整 | 补充所有状态变体 |
| 样式冲突 | 命名不规范 | 规范命名体系 |
| 复用困难 | 组件拆分不合理 | 重新设计组件结构 |