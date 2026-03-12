---
description:
globs:
alwaysApply: true
---

# MasterGo 组件系统规范 v1.0

## 核心内容

- 项目环境搭建
- 组件交互设计
- 组件开发工作流

---

## 项目环境搭建

### 环境检查

检查项目是否已初始化：

- `package.json`
- TypeScript 配置
- Vite 配置
- VitePress 配置 (`docs/.vitepress/`)
- Vue 和测试相关依赖

### 环境初始化

所需步骤：

```bash
npm init -y
npm install vue@latest typescript vite@latest vitepress@latest vitest@latest @vitejs/plugin-vue@latest
npm install -D @vue/test-utils jsdom @types/node
```

必需的配置文件：

- `tsconfig.json`
- `vite.config.ts`
- `docs/.vitepress/config.ts`
- `vitest.config.ts`

### 项目结构

```
project-root/
├── docs/                # 组件文档
│   ├── .vitepress/      # VitePress 配置
│   ├── components/      # 组件文档和示例
├── src/
│   ├── components/      # 组件源码
│   ├── styles/          # 样式文件
├── __tests__/           # 组件测试
```

### 必需脚本

```json
{
  "scripts": {
    "dev": "vitepress dev docs",
    "build": "vitepress build docs",
    "test": "vitest run",
    "test:ui": "vitest --ui"
  }
}
```

### 项目验证

**关键步骤**：项目初始化后，必须运行脚本以验证配置：

1. 运行开发服务器：

   ```bash
   npm run dev
   ```

2. 验证测试环境：

   ```bash
   npm run test
   ```

3. 确保每个脚本在控制台中不出现错误
4. 在进行组件开发之前解决所有错误
5. 只有当所有脚本都能无错误运行时，项目才算正确初始化

---

## 组件交互设计规范

### 核心原则

- **CSS 优先**：基本状态使用 CSS 伪类实现
- **状态扩展**：允许通过 props 覆盖默认状态
- **一致性**：保持一致的状态管理模式
- **性能优先**：最小化 JavaScript 状态管理

### 状态优先级

CSS 伪类 > Props 指定状态 > JavaScript 状态管理

### 组件复用原则

复用决策优先级：

1. 直接使用（功能完全匹配时）
2. 组件组合（通过组合现有组件实现）
3. 组件扩展（在现有组件基础上添加新功能）
4. 重新开发（仅当上述方法不可行时）

---

## 组件开发工作流

### 完整流程

```
[环境检查] → [项目验证] → [组件分析] → [用户确认] → [测试生成] → [组件开发] → [验证] → [文档与预览]
```

### 1. 组件分析

**输入**：组件 JSON 规范  
**输出**：架构文档 (`.mastergo/${componentName}-arch.md`)

#### 插槽分析

AI 必须分析组件设计并推断：

- 可能需要的插槽
- 每个插槽的用途
- 默认内容建议
- 可选/必选状态

#### 检查清单

- [ ] 属性分析
- [ ] 状态和变体识别
- [ ] 公共样式提取
- [ ] 接口定义
- [ ] 插槽定义

#### 架构文档验证

**关键断点**：生成架构文档后，必须暂停执行。

1. 将架构文档呈现给用户审阅
2. 请用户验证文档的各个方面：
   - 组件属性和类型
   - 状态定义
   - 插槽规范
   - 组件结构
   - 图片资源及其路径
3. 如果用户发现问题：
   - 收集所有反馈
   - 对架构文档进行必要修改
   - 呈现更新后的文档供审阅
4. 重复审阅循环，直到用户明确批准文档
5. 只有在用户确认后才能进入测试生成阶段

#### 图片资源处理

**关键步骤**：用户确认架构文档后，进入测试生成阶段之前：

1.  **资源清单和路径记录**：

    - 架构文档必须包含"图片资源"部分，以表格形式清晰列出所有必要资源：

    ```markdown
    ## 图片资源

    ### 资源列表和路径

    | 图标描述   | 原始路径                  | 目标路径                                                | 图标颜色控制                     |
    | ---------- | ------------------------- | ------------------------------------------------------- | -------------------------------- |
    | 关闭图标   | `/original/path/icon.svg` | `src/components/${componentName}/images/icon-close.svg` | 动态控制，默认与文字颜色一致     |
    | 其他图标   | ...                       | ...                                                     | ...                              |
    ```

2.  **复制图片**：

    - 将架构文档中列出的所有必要图片资源复制到组件专用目录。
    - 使用语义化文件名，如 `icon-close.svg`、`icon-success.svg`、`bg-header.png` 等，确保名称清晰表示每个图片的用途。
    - 目标路径必须是 `src/components/${componentName}/images/`。如果目录不存在则创建。
    - 示例：
      ```bash
      mkdir -p src/components/${componentName}/images
      cp /original/path/close-icon.svg src/components/${componentName}/images/icon-close.svg
      ```

3.  **SVG 图片导入和颜色规范**：

    - 架构文档必须明确指定 SVG 的导入方式和颜色控制方法。
    - SVG 必须使用以下方法导入以确保动态颜色控制：

      ```typescript
      import CloseIcon from "./images/icon-close.svg?raw"; // ?raw 确保以字符串形式导入
      ```

    - 架构文档必须包含 SVG 使用和颜色控制的代码示例：

      ````markdown
      ### 图标导入和使用方法

      ```typescript
      // 在 ${componentName}.vue 中，导入图标
      import CloseIcon from "./images/icon-close.svg?raw";
      import SuccessIcon from "./images/icon-success.svg?raw";
      ```
      ````

      在模板中使用 SVG 并控制其颜色：

      ```html
      <template>
        <div class="icon-container" v-html="CloseIcon"></div>
      </template>

      <style scoped>
        .icon-container svg {
          fill: v-bind("dynamicColorVariable"); /* 动态绑定颜色 */
        }
        /* 或使用 CSS 变量控制颜色 */
        .icon-container svg {
          fill: var(--icon-color, currentColor);
        }
      </style>
      ```

    - 对于每个 SVG 图标，架构文档必须明确说明：
      1. 默认颜色
      2. 颜色是固定的还是需要动态控制
      3. 不同状态下的颜色变化

### 2. 测试生成

**输入**：已批准的架构文档  
**输出**：组件单元测试

#### 测试覆盖

- 所有组件属性
- 所有组件状态和行为
- 边界情况
- 所有推断的插槽
- 状态管理（hover、focus、active、disabled 等）

### 3. 组件开发

**输入**：架构文档和测试用例  
**输出**：功能组件

#### 必需文件

- `src/components/${componentName}/index.ts`
- `src/components/${componentName}/types.ts`
- `src/components/${componentName}/${componentName}.vue`

#### 开发方法

- 测试驱动开发
- 必须遵循 UI 交互设计规范
- 迭代实现：最小代码 → 运行测试 → 重构 → 下一个测试

### 4. 验证

- 所有测试通过
- 组件视觉效果与设计一致
- 组件可访问性
- 响应式行为正确

### 5. 文档与预览

**输出**：VitePress 文档和交互式预览

#### 文档内容

- 组件概述
- API 参考
- 交互式示例
- 完整的插槽文档
- 各种状态和用例演示

#### 交互式预览

````md
## 基本用法

:::demo

```vue
<template>
  <ComponentName prop="value" />
</template>
```

:::
````

### 检查点

- **环境**：配置正确、依赖已安装、文档预览系统正常工作
- **结构**：文件已创建、导出正常、接口已定义、插槽定义完整
- **测试**：覆盖所有功能、边界情况、插槽和状态
- **实现**：渲染正确、属性有效、状态管理符合规范、样式正确应用、插槽功能正常
- **文档**：功能文档完整、示例可用、API 参考完整、插槽使用文档完整
