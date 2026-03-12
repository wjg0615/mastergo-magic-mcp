`results` 返回一个 XML 文件，其中包含两种类型的信息：

- **meta**：描述站点、环境、需求等上下文信息。
- **action**：对应入口页面名称和 `targetLayerId`，即目标页面的 ID。

我需要你按照以下步骤在当前目录下创建一个 task.md 文件，并严格按照这些步骤生成最终的项目代码。

## 步骤
1. 获取 `results` 信息，提取 `meta` 和 `action` 数据，创建一个新的 `task.md` 文件。
2. 分析 `meta` 字段，总结需求描述，并将其写入 task.md。
3. 分析 `action` 字段，将页面信息写入 task.md。
4. 使用 `action` 字段中的 `targetLayerId` 调用 `mcp__getDsl` 方法获取数据。
5. 分析页面数据，检查是否存在 `interactive` 字段。如果存在，该字段包含当前节点导航到另一个页面的信息。你必须根据 `interactive` 字段继续调用 `mcp__getDsl` 方法。
6. 重复步骤 5，直到所有页面数据都已解析并写入 task.md。
7. 根据 task.md 中的内容，依次解析 task.md 中列出的页面并生成代码。完成项目构建。

## 示例
**注意**：请务必按照示例中描述的顺序执行。确保获取所有页面信息！


假设获取的 results 为：
```xml
<info>
  <meta title="Name" content="外卖配送 APP" />
  <meta title="Description" content="这是一个外卖配送应用，用户可以登录、下单订餐，并管理配送订单、地址信息等。" />
  <meta title="Requirements" content="使用 React 实现，绑定 Ant Design 组件库" />
  <action title="登录页面" layerId="0:1" />
  <action title="外卖配送页面" layerId="0:2" />
</info>
```
将以下内容写入 task.md：




```markdown
需求描述：这是一个外卖配送应用，包含登录、订餐和地址管理功能。应使用 React 构建，并使用 Ant Design 作为组件库。

## 页面列表：
登录页面 (layerId: 0:1)
外卖配送页面 (layerId: 0:2)

## 导航信息


```

使用 `mcp__getDsl` 解析 0:1 页面并分析数据。

数据可能如下：
```json
{
    nodes: [{
        id: "0:1",
        // ...其他字段
        children: [{
            id: "1:12",
            interactive: [
                type: "navigation",
                targetLayerId: "0:3"
            ]
        }]
    }]
}
```

如果你发现页面 0:1 的节点数据包含 id 为 0:3 的 `interactive` 字段，则写入 0:3 页面并添加导航信息：
```markdown

## 页面列表：
登录页面 (layerId: 0:1)
外卖配送页面 (layerId: 0:2)
登录页面导航页面 (layerId: 0:3)

## 导航信息
0:1 => 0:3

```
继续解析 0:3。如果数据包含 `interactive` 字段，继续写入。
重复此过程，直到 `mcp__getDsl` 解析的数据不再包含 `interactive` 字段。
然后，使用 `mcp__getDsl` 解析 0:2 页面，并重复 0:1 页面的步骤。

完成后，按照 task.md 中的页面列表依次生成项目代码。
