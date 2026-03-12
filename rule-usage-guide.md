# MasterGo Magic MCP 规则使用指南

本文档总结了 `--rule` 参数的使用方法和常见场景配置。

---

## 一、`--rule` 参数基础

### 1.1 参数格式

```bash
# 等号格式（推荐）
npx @mastergo/magic-mcp --token=YOUR_TOKEN --rule=rule1 --rule=rule2

# 空格分隔格式
npx @mastergo/magic-mcp --token YOUR_TOKEN --rule rule1 --rule rule2
```

### 1.2 参数作用

`--rule` 参数用于**向 AI 模型传递自定义的代码生成规则**。这些规则会作为系统提示词的一部分，告诉 AI 在将 MasterGo 设计转换为代码时应该遵循哪些约束和规范。

### 1.3 工作原理

```
设计稿图层命名（原始数据）→ DSL 数据 → AI 解析 → 生成代码
                                    ↑
                              --rule 在这里生效
```

### 1.4 环境变量方式

```bash
export RULES='["规则1", "规则2", "规则3"]'
```

或通过 MCP 配置：

```json
{
  "mcpServers": {
    "mastergo-magic-mcp": {
      "command": "npx",
      "args": ["-y", "@mastergo/magic-mcp", "--token=YOUR_TOKEN"],
      "env": {
        "RULES": "[\"规则1\", \"规则2\"]"
      }
    }
  }
}
```

---

## 二、默认规则

如果不传 `--rule` 参数，系统会使用以下默认规则：

1. **Token 必须生成变量** - 颜色、阴影、字体等必须作为变量生成，并在注释中显示 token 字段
2. **组件文档链接处理** - 如果 DSL 中包含 `componentDocumentLinks`，必须使用 `mcp__getComponentLink` 获取组件文档

---

## 三、常见规则配置场景

### 3.1 提高还原度的规则

#### 布局规则
```bash
--rule="必须使用 Flexbox/Grid 布局，禁止使用绝对定位"
--rule="间距必须使用 8px 基准网格系统（4px、8px、16px、24px、32px、48px）"
--rule="使用相对单位（rem、em、%）代替固定像素值"
```

#### Design Token 规则
```bash
--rule="所有颜色必须使用 CSS 变量，禁止硬编码颜色值"
--rule="字体大小必须使用预定义的字体层级（如 text-xs、text-sm、text-base）"
--rule="圆角、阴影必须使用 Design Token 变量"
```

#### 组件状态规则
```bash
--rule="按钮必须实现 default、hover、active、disabled、loading 五种状态"
--rule="所有交互元素必须有 transition 过渡动画（duration: 0.2s, easing: ease-in-out）"
--rule="表单组件必须包含 focus、error、disabled 状态"
```

#### 完整示例：企业级项目
```bash
npx @mastergo/magic-mcp --token=YOUR_TOKEN \
  --rule="使用 Tailwind CSS，间距必须使用 Tailwind 间距系统" \
  --rule="颜色必须使用 Tailwind 颜色变量或 CSS 变量" \
  --rule="组件必须支持 hover、focus、active、disabled 状态" \
  --rule="所有过渡动画统一使用 duration-200 ease-in-out"
```

---

### 3.2 命名规则

#### 命名规则的有效性分析

| 场景 | 效果 |
|------|------|
| 设计稿命名规范 + 命名规则 | ✅ 完美匹配，AI 能准确理解语义 |
| 设计稿命名随意 + 命名规则 | ⚠️ AI 会按规则命名代码，但可能误解图层含义 |

#### 有效的规则（约束代码输出）
```bash
--rule="生成的 CSS 类名使用 BEM 命名规范"
--rule="组件文件名使用 PascalCase，如 Button.tsx"
--rule="变量名使用 camelCase，禁止使用拼音"
```

#### 无效的规则（期望改变设计稿理解）
```bash
# 这些规则对随意命名的设计稿帮助有限
--rule="容器层命名使用 container- 前缀"  # 设计稿不是这个名，AI 无法对应
--rule="图标层命名使用 icon- 前缀"       # 同上
```

#### 推荐做法：语义识别规则
如果设计稿命名随意，应该使用**语义识别规则**：

```bash
npx @mastergo/magic-mcp --token=YOUR_TOKEN \
  --rule="根据图层的外观和位置推断其语义用途" \
  --rule="按钮类元素统一命名为 Button 组件" \
  --rule="图片类元素使用 img 标签，添加有意义的 alt 属性" \
  --rule="容器元素根据内容推断用途，如 header、main、footer"
```

---

### 3.3 SVG 图标转 iconfont 规则

#### 基础规则
```bash
npx @mastergo/magic-mcp --token=YOUR_TOKEN \
  --rule="设计稿中的 SVG 图标必须转换为 iconfont 使用，禁止使用 img 标签或内联 SVG" \
  --rule="iconfont 类名使用图层 name 字段，转换为 kebab-case 格式，如 name='icon-close' 则类名为 'iconfont icon-close'" \
  --rule="如果图层命名不规范（如'矩形123'），根据图标外观推断语义并命名，如关闭图标用 icon-close" \
  --rule="iconfont 使用方式：<i class='iconfont icon-xxx'></i>"
```

#### 详细规则模板
```bash
--rule="【图标处理规范】设计稿中的 SVG/矢量图标必须转换为 iconfont 字体图标，使用方式：<i class='iconfont icon-{name}'></i>。禁止使用 img 标签、内联 SVG 或 background-image。图标类名从 DSL 节点的 name 字段提取，去除 icon- 前缀后重新拼接，如 name='icon-close' 生成 class='iconfont icon-close'，name='close' 同样生成 class='iconfont icon-close'"
```

#### 语义映射规则（处理不规范命名）
```bash
--rule="图标语义映射：关闭/×/close→icon-close，搜索/放大镜/search→icon-search，菜单/三条杠/menu→icon-menu，箭头/右箭头/arrow→icon-arrow-right，用户/头像/user→icon-user"
```

#### 效果对比

**设计稿 DSL 数据：**
```json
{
  "name": "icon-close",
  "type": "VECTOR",
  "svgPath": "M6 6L18 18M6 18L18 6"
}
```

**不加规则时：**
```jsx
<img src="data:image/svg+xml;base64,..." alt="icon-close" />
```

**加规则后：**
```jsx
<i className="iconfont icon-close"></i>
```

---

### 3.4 图片上传去重规则

#### 问题背景
使用 OSS 上传 MCP 时，相同图片可能被重复上传多次。

#### 去重规则
```bash
--rule="【图片去重规范】处理图片时必须遵循以下步骤：
1. 提取图片的原始 URL 或生成唯一标识（基于文件名+尺寸+内容的 hash）
2. 检查全局图片映射表是否已存在该标识
3. 如果存在，直接使用映射表中的 OSS URL
4. 如果不存在，调用上传 MCP 上传，将结果存入映射表
5. 同一设计稿中相同图片只上传一次"
```

#### 详细规则模板
```bash
--rule="【图片处理去重规范】
1. 创建全局图片缓存对象 imageCache = {}
2. 遍历 DSL 中所有图片节点时：
   - 以图片原始 URL 或 src 字段作为 key
   - 如果 imageCache[key] 存在，直接使用缓存的 OSS URL
   - 如果不存在，调用上传 MCP 上传，将返回的 OSS URL 存入 imageCache[key]
3. 相同 src 的图片节点共享同一个 OSS URL
4. 在代码生成阶段，统一使用缓存中的 OSS URL"
```

#### 效果对比

**DSL 数据中有重复图片：**
```json
{
  "nodes": [
    { "name": "avatar1", "type": "IMAGE", "src": "https://cdn.example.com/avatar.png" },
    { "name": "avatar2", "type": "IMAGE", "src": "https://cdn.example.com/avatar.png" },
    { "name": "avatar3", "type": "IMAGE", "src": "https://cdn.example.com/avatar.png" }
  ]
}
```

**不加规则时（可能重复上传）：**
```jsx
<img src="https://oss.example.com/abc123.png" />  // 第1次上传
<img src="https://oss.example.com/def456.png" />  // 第2次上传（重复）
<img src="https://oss.example.com/ghi789.png" />  // 第3次上传（重复）
```

**加规则后（只上传一次）：**
```jsx
<img src="https://oss.example.com/abc123.png" />
<img src="https://oss.example.com/abc123.png" />  // 复用
<img src="https://oss.example.com/abc123.png" />  // 复用
```

---

## 四、规则优先级

| 规则类型 | 还原度影响 | 推荐程度 |
|---------|-----------|---------|
| 布局规则 | ⭐⭐⭐⭐⭐ | 必须添加 |
| Token 规则 | ⭐⭐⭐⭐⭐ | 必须添加 |
| 状态规则 | ⭐⭐⭐⭐ | 推荐添加 |
| 图标规则 | ⭐⭐⭐⭐ | 推荐添加 |
| 命名规则 | ⭐⭐⭐ | 可选添加 |
| 图片去重规则 | ⭐⭐⭐ | 可选添加 |

---

## 五、综合配置示例

### 5.1 高保真还原项目
```bash
npx @mastergo/magic-mcp --token=YOUR_TOKEN \
  --rule="严格还原设计稿的每个像素值，不做任何近似" \
  --rule="必须实现设计稿中的所有交互状态" \
  --rule="阴影效果必须完全还原，包括 blur、spread、offset" \
  --rule="渐变效果必须使用 CSS gradient 精确还原" \
  --rule="文字行高、字间距必须与设计稿一致" \
  --rule="SVG 图标转换为 iconfont：<i class='iconfont icon-xxx'></i>" \
  --rule="相同图片只上传一次，复用 OSS URL"
```

### 5.2 响应式项目
```bash
npx @mastergo/magic-mcp --token=YOUR_TOKEN \
  --rule="布局必须使用 Flexbox，支持响应式" \
  --rule="宽度优先使用百分比或 flex-grow" \
  --rule="字体大小使用 clamp() 实现流式排版" \
  --rule="图片使用 object-fit: cover 保持比例"
```

### 5.3 完整 MCP 配置示例
```json
{
  "mcpServers": {
    "mastergo-magic-mcp": {
      "command": "npx",
      "args": [
        "-y",
        "@mastergo/magic-mcp",
        "--token=<MG_MCP_TOKEN>",
        "--url=https://mastergo.com"
      ],
      "env": {
        "RULES": "[\"使用 Flexbox 布局\",\"颜色使用 CSS 变量\",\"实现所有交互状态\",\"间距使用 8px 网格系统\",\"SVG 图标转换为 iconfont\",\"相同图片只上传一次\"]"
      }
    }
  }
}
```

---

## 六、注意事项

1. **规则是指导 AI 生成代码**，而不是改变设计稿数据的解析方式
2. **命名规则对随意命名的设计稿作用有限**，应使用语义识别规则
3. **规则越具体越好**，避免模糊表述
4. **相同类型的规则可以合并**，减少参数数量
5. **可以通过环境变量管理规则**，便于维护和复用
