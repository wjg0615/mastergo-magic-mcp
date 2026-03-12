---
description: 复杂模块组件化工作流程
globs:
alwaysApply: true
---

# 复杂模块组件化工作流程

本文档定义了复杂设计模块的组件化拆分策略和操作流程，确保设计稿能够高效转换为可维护的前端代码。

---

## 一、组件化拆分原则

### 1.1 分层架构

```
复杂模块拆分层级：

页面 (Page)
  └── 区块 (Section)
        └── 模块 (Module)
              └── 组件 (Component)
                    └── 原子元素 (Atom)
```

### 1.2 拆分原则

| 原则 | 说明 | 示例 |
|------|------|------|
| 单一职责 | 每个组件只负责一个功能 | Tag 只负责标签展示 |
| 可复用性 | 组件应在多处复用 | Button 可用于任何场景 |
| 独立性 | 组件可独立测试和使用 | Price 组件不依赖外部状态 |
| 层级清晰 | 父子关系明确 | Card 包含 Image、Info、Action |

---

## 二、实战案例：商品卡片模块

### 2.1 原始设计稿结构

```
┌─────────────────────────────────────────┐
│ ┌─────────────────────────────────────┐ │
│ │         [商品图片]                   │ │
│ │    ┌──────────────────────────┐     │ │
│ │    │                          │     │ │
│ │    │       商品主图            │     │ │
│ │    │                          │     │ │
│ │    └──────────────────────────┘     │ │
│ │    ┌────┐                           │ │
│ │    │标签│  ┌────┐ ┌────┐           │ │
│ │    └────┘  │收藏│ │分享│           │ │
│ └─────────────────────────────────────┘ │
│ ┌─────────────────────────────────────┐ │
│ │ 商品标题                             │ │
│ │ 商品描述                             │ │
│ │ ┌────┐ ┌────┐ ┌────┐               │ │
│ │ │标签│ │标签│ │标签│               │ │
│ └─────────────────────────────────────┘ │
│ ┌─────────────────────────────────────┐ │
│ │ ¥199  ──  ¥299      [立即购买]      │ │
│ └─────────────────────────────────────┘ │
└─────────────────────────────────────────┘
```

### 2.2 拆分步骤总览

```
步骤1：识别原子组件
      ↓
步骤2：创建原子组件 + 变体
      ↓
步骤3：识别子模块
      ↓
步骤4：创建子模块组件（使用原子组件实例）
      ↓
步骤5：创建完整模块（使用子模块组件实例）
      ↓
步骤6：定义模块级变体 + 属性绑定
      ↓
步骤7：测试所有变体组合
```

---

## 三、详细拆分流程

### 3.1 步骤一：识别原子组件

从设计稿中提取最小可复用单元：

```
原子组件列表：
├── Image          图片组件
├── Tag            标签组件  
├── Icon           图标组件
├── Price          价格组件
├── Button         按钮组件
└── Title          标题组件
```

**识别标准**：

| 标准 | 说明 | 判断方法 |
|------|------|----------|
| 可复用 | 在多处使用 | 设计稿中出现 2 次以上 |
| 独立性 | 可单独使用 | 不依赖父级上下文 |
| 语义明确 | 功能单一 | 用一个词描述其功能 |

### 3.2 步骤二：创建原子组件

#### 3.2.1 Tag 组件

```
MasterGo 操作：
1. 选中标签元素
2. 右键 → 创建组件（Ctrl/Cmd + K）
3. 命名：Tag
4. 添加变体

变体定义：
├── Type
│   ├── default     默认样式
│   ├── primary     主要标签
│   ├── success     成功状态
│   ├── warning     警告状态
│   └── error       错误状态
├── Size
│   ├── small       高度 20px
│   ├── medium      高度 24px
│   └── large       高度 28px
└── Closable
    ├── true        显示关闭按钮
    └── false       不显示关闭按钮
```

#### 3.2.2 Button 组件

```
变体定义：
├── Type
│   ├── primary     主要按钮
│   ├── secondary   次要按钮
│   ├── dashed      虚线按钮
│   ├── text        文字按钮
│   └── link        链接按钮
├── Size
│   ├── small       高度 24px
│   ├── medium      高度 32px
│   └── large       高度 40px
└── State
    ├── default     默认状态
    ├── hover       悬停状态
    ├── active      激活状态
    ├── disabled    禁用状态
    └── loading     加载状态
```

#### 3.2.3 Price 组件

```
变体定义：
├── Type
│   ├── current     当前价格（大号、强调）
│   └── original    原价（小号、删除线）
├── Size
│   ├── small       字号 14px
│   ├── medium      字号 18px
│   └── large       字号 24px
└── Currency
    ├── cny         ¥ 符号
    ├── usd         $ 符号
    └── none        无符号
```

### 3.3 步骤三：识别子模块

将复杂模块按功能区域划分：

```
子模块划分：

┌─────────────────────────────────────────┐
│ ProductCard                             │
│ ┌─────────────────────────────────────┐ │
│ │ ProductImage (子模块1)              │ │
│ │   ├── Image                         │ │
│ │   ├── Tag                           │ │
│ │   └── IconButton × 2                │ │
│ ├─────────────────────────────────────┤ │
│ │ ProductInfo (子模块2)               │ │
│ │   ├── Title                         │ │
│ │   ├── Description                   │ │
│ │   └── Tag × n                       │ │
│ ├─────────────────────────────────────┤ │
│ │ ProductAction (子模块3)             │ │
│ │   ├── Price                         │ │
│ │   └── Button                        │ │
│ └─────────────────────────────────────┘ │
└─────────────────────────────────────────┘
```

### 3.4 步骤四：创建子模块组件

#### 3.4.1 ProductImage 组件

```
MasterGo 操作：
1. 选中商品图片区域所有元素
2. 创建组件 → 命名 ProductImage
3. 添加变体

变体定义：
├── Ratio
│   ├── 1:1        正方形
│   ├── 4:3        标准比例
│   └── 16:9       宽屏比例
├── ShowTag
│   ├── true       显示左上角标签
│   └── false      不显示
└── ShowActions
    ├── true       显示收藏/分享按钮
    └── false      不显示

内部结构：
┌─────────────────────────────────┐
│ ProductImage                     │
│ ┌─────────────────────────────┐ │
│ │ <Image /> (组件实例)         │ │
│ │ ┌────┐                      │ │
│ │ │Tag │ (组件实例)            │ │
│ │ └────┘                      │ │
│ │ ┌────┐ ┌────┐               │ │
│ │ │Icon│ │Icon│ (组件实例)     │ │
│ │ └────┘ └────┘               │ │
│ └─────────────────────────────┘ │
└─────────────────────────────────┘
```

#### 3.4.2 ProductInfo 组件

```
变体定义：
├── TitleLines
│   ├── 1          单行标题
│   ├── 2          两行标题
│   └── 3          三行标题
├── ShowDesc
│   ├── true       显示描述
│   └── false      不显示描述
└── ShowTags
    ├── true       显示标签列表
    └── false      不显示标签

内部结构：
┌─────────────────────────────────┐
│ ProductInfo                      │
│ ┌─────────────────────────────┐ │
│ │ <Title /> (文本)             │ │
│ │ <Description /> (文本)       │ │
│ │ ┌────┐ ┌────┐ ┌────┐        │ │
│ │ │Tag │ │Tag │ │Tag │ (实例)  │ │
│ │ └────┘ └────┘ └────┘        │ │
│ └─────────────────────────────┘ │
└─────────────────────────────────┘
```

#### 3.4.3 ProductAction 组件

```
变体定义：
├── ShowOriginalPrice
│   ├── true       显示原价
│   └── false      不显示原价
├── ButtonType
│   ├── primary    主要按钮
│   └── secondary  次要按钮
└── ButtonSize
    ├── small      小按钮
    ├── medium     中按钮
    └── large      大按钮

内部结构：
┌─────────────────────────────────┐
│ ProductAction                    │
│ ┌─────────────────────────────┐ │
│ │ <Price type="current" />    │ │
│ │ <Price type="original" />   │ │
│ │ <Button /> (组件实例)        │ │
│ └─────────────────────────────┘ │
└─────────────────────────────────┘
```

### 3.5 步骤五：创建完整模块

#### 3.5.1 ProductCard 主组件

```
MasterGo 操作：
1. 选中整个卡片区域
2. 创建组件 → 命名 ProductCard
3. 添加变体

变体定义：
├── Layout
│   ├── vertical      垂直布局（上下排列）
│   └── horizontal    水平布局（左右排列）
├── Size
│   ├── small         紧凑卡片
│   ├── medium        标准卡片
│   └── large         大卡片
└── State
    ├── default       默认状态
    ├── hover         悬停状态（阴影、边框变化）
    └── selected      选中状态（边框高亮）
```

#### 3.5.2 组件嵌套结构

```
ProductCard (主组件)
│
├── 变体属性
│   ├── Layout: vertical / horizontal
│   ├── Size: small / medium / large  
│   └── State: default / hover / selected
│
├── ProductImage (子组件实例)
│   ├── 变体绑定: ratio = Card.size → Image.ratio
│   │
│   ├── Image (原子组件实例)
│   ├── Tag (原子组件实例)
│   └── IconButton (原子组件实例) × 2
│
├── ProductInfo (子组件实例)
│   ├── Title (文本)
│   ├── Description (文本)
│   └── Tag (原子组件实例) × n
│
└── ProductAction (子组件实例)
    ├── Price (原子组件实例)
    └── Button (原子组件实例)
```

### 3.6 步骤六：属性绑定

#### 3.6.1 MasterGo 属性绑定操作

```
操作步骤：
1. 选中 ProductCard 组件
2. 进入组件编辑模式
3. 选中子组件实例（如 ProductImage）
4. 在右侧面板找到属性绑定区域
5. 将子组件属性绑定到父组件属性

绑定示例：
ProductCard.size = "large"
    ↓ 自动传递
    ├── ProductImage.ratio = "16:9"
    ├── ProductInfo.titleLines = "3"
    └── ProductAction.buttonSize = "large"

ProductCard.state = "hover"
    ↓ 自动传递
    ├── ProductImage.showActions = true
    └── 整体阴影变化
```

#### 3.6.2 属性映射表

| 父组件属性 | 子组件属性 | 映射关系 |
|------------|------------|----------|
| size=small | image.ratio | 1:1 |
| size=small | info.titleLines | 1 |
| size=small | action.buttonSize | small |
| size=medium | image.ratio | 4:3 |
| size=medium | info.titleLines | 2 |
| size=medium | action.buttonSize | medium |
| size=large | image.ratio | 16:9 |
| size=large | info.titleLines | 3 |
| size=large | action.buttonSize | large |
| state=hover | image.showActions | true |
| state=hover | 整体阴影 | shadow-lg |

---

## 四、DSL 数据结构

### 4.1 完整 DSL 示例

```json
{
  "nodes": [{
    "id": "1:0",
    "name": "ProductCard",
    "type": "COMPONENT_SET",
    "componentPropertyDefinitions": {
      "Layout": {
        "type": "TEXT",
        "defaultValue": "vertical",
        "variantOptions": ["vertical", "horizontal"]
      },
      "Size": {
        "type": "TEXT",
        "defaultValue": "medium",
        "variantOptions": ["small", "medium", "large"]
      },
      "State": {
        "type": "TEXT",
        "defaultValue": "default",
        "variantOptions": ["default", "hover", "selected"]
      }
    },
    "children": [{
      "id": "1:1",
      "name": "vertical, medium, default",
      "type": "COMPONENT",
      "children": [{
        "id": "1:2",
        "name": "ProductImage",
        "type": "INSTANCE",
        "componentId": "2:0",
        "overrides": {
          "ratio": "4:3",
          "showTag": true,
          "showActions": false
        }
      }, {
        "id": "1:3",
        "name": "ProductInfo",
        "type": "INSTANCE",
        "componentId": "3:0",
        "overrides": {
          "titleLines": 2,
          "showDesc": true,
          "showTags": true
        }
      }, {
        "id": "1:4",
        "name": "ProductAction",
        "type": "INSTANCE",
        "componentId": "4:0",
        "overrides": {
          "showOriginalPrice": true,
          "buttonType": "primary",
          "buttonSize": "medium"
        }
      }]
    }]
  }]
}
```

### 4.2 子组件 DSL 示例

```json
{
  "nodes": [{
    "id": "2:0",
    "name": "ProductImage",
    "type": "COMPONENT_SET",
    "componentPropertyDefinitions": {
      "Ratio": {
        "type": "TEXT",
        "defaultValue": "4:3",
        "variantOptions": ["1:1", "4:3", "16:9"]
      },
      "ShowTag": {
        "type": "BOOLEAN",
        "defaultValue": true
      },
      "ShowActions": {
        "type": "BOOLEAN",
        "defaultValue": false
      }
    },
    "children": [{
      "id": "2:1",
      "name": "4:3, true, false",
      "children": [{
        "id": "2:2",
        "name": "Image",
        "type": "INSTANCE",
        "componentId": "10:0"
      }, {
        "id": "2:3",
        "name": "Tag",
        "type": "INSTANCE",
        "componentId": "11:0"
      }]
    }]
  }]
}
```

---

## 五、检查清单

### 5.1 设计阶段检查

| 检查项 | 说明 | 状态 |
|--------|------|------|
| ☐ 原子识别 | 是否识别出所有可复用的原子组件？ | |
| ☐ 层级清晰 | 子模块之间边界是否清晰？ | |
| ☐ 变体合理 | 变体是否覆盖所有使用场景？ | |
| ☐ 属性绑定 | 子组件属性是否与父组件关联？ | |
| ☐ 命名规范 | 所有层级命名是否语义化？ | |
| ☐ 实例复用 | 是否使用组件实例而非复制？ | |
| ☐ Auto Layout | 是否正确使用自动布局？ | |

---

## 六、常见问题与解决方案

### 7.1 拆分粒度问题

| 问题 | 原因 | 解决方案 |
|------|------|----------|
| 组件过于碎片化 | 拆分过细 | 合并功能相近的原子组件 |
| 组件过于庞大 | 拆分不足 | 按功能区域继续拆分 |
| 层级过深 | 过度嵌套 | 扁平化结构，减少中间层 |

### 7.2 变体组合爆炸

```
问题：变体组合过多，难以维护

示例：
- Type × Size × State = 5 × 3 × 5 = 75 种组合

解决方案：
1. 使用属性绑定，减少手动创建
2. 使用布尔属性替代枚举
3. 分离独立变体为独立属性
```

### 7.3 属性传递问题

| 问题 | 原因 | 解决方案 |
|------|------|----------|
| 属性未传递 | 未建立绑定关系 | 在 MasterGo 中设置属性绑定 |
| 传递错误 | 映射关系错误 | 检查属性映射表 |
| 类型不匹配 | 类型定义不一致 | 统一类型定义 |

---

## 七、附录

### 7.1 组件命名规范

| 层级 | 命名格式 | 示例 |
|------|----------|------|
| 原子组件 | 功能名 | Button, Tag, Icon |
| 子模块 | 模块名+区域名 | ProductImage, ProductInfo |
| 主模块 | 业务名 | ProductCard, UserProfile |
| 页面 | Page后缀 | HomePage, ProductPage |

### 7.2 变体属性命名规范

| 属性类型 | 命名示例 | 可选值 |
|----------|----------|--------|
| 类型 | Type | primary, secondary, default |
| 尺寸 | Size | small, medium, large |
| 状态 | State | default, hover, active, disabled |
| 布局 | Layout | vertical, horizontal, grid |
| 显示 | Show* | true, false |

### 7.3 快捷键参考

| 操作 | Mac | Windows |
|------|-----|---------|
| 创建组件 | Cmd + K | Ctrl + K |
| 添加变体 | 右键菜单 | 右键菜单 |
| 创建实例 | Alt + 拖拽 | Alt + 拖拽 |
| 编辑组件 | Enter | Enter |

---
