# Excalidraw 元素属性速查

## 基础结构

每个 `.excalidraw` 文件的顶层结构：

```json
{
  "type": "excalidraw",
  "version": 2,
  "source": "claude-code",
  "elements": [...],
  "appState": {
    "viewBackgroundColor": "#ffffff",
    "gridSize": null
  },
  "files": {}
}
```

## 元素类型

| type | 用途 | 特有属性 |
|------|------|----------|
| `rectangle` | 矩形/圆角矩形 | roundness |
| `ellipse` | 椭圆/圆 | - |
| `diamond` | 菱形（决策） | - |
| `line` | 直线/折线 | points |
| `arrow` | 箭头连线 | points, startArrowhead, endArrowhead, startBinding, endBinding |
| `text` | 文字 | text, fontSize, fontFamily, textAlign, containerId |

## 通用属性

每个元素都有的属性：

```json
{
  "id": "unique-id",
  "type": "rectangle",
  "x": 100,
  "y": 100,
  "width": 150,
  "height": 80,
  "angle": 0,
  "strokeColor": "#1e3a5f",
  "backgroundColor": "#dbeafe",
  "fillStyle": "solid",
  "strokeWidth": 2,
  "strokeStyle": "solid",
  "roughness": 1,
  "opacity": 100,
  "groupIds": [],
  "roundness": { "type": 3 },
  "seed": 1,
  "version": 1,
  "versionNonce": 1,
  "isDeleted": false,
  "boundElements": []
}
```

## 属性说明

### 样式属性

| 属性 | 值 | 说明 |
|------|-----|------|
| `fillStyle` | `"solid"`, `"hachure"`, `"cross-hatch"` | 填充样式 |
| `strokeStyle` | `"solid"`, `"dashed"`, `"dotted"` | 线条样式 |
| `strokeWidth` | 1, 2, 4 | 线宽 |
| `roughness` | 0, 1, 2 | 手绘感（0=平滑，2=粗糙） |
| `opacity` | 0-100 | 透明度 |

### 圆角 (roundness)

```json
// 圆角矩形
"roundness": { "type": 3 }

// 椭圆/菱形
"roundness": { "type": 2 }

// 无圆角
"roundness": null
```

### 文字属性

```json
{
  "type": "text",
  "text": "Hello World",
  "fontSize": 20,
  "fontFamily": 1,
  "textAlign": "center",
  "verticalAlign": "middle",
  "containerId": "parent-element-id"  // 绑定到容器
}
```

| fontFamily | 字体 |
|------------|------|
| 1 | Virgil（手写） |
| 2 | Helvetica |
| 3 | Cascadia（等宽） |

| textAlign | 水平对齐 |
|-----------|----------|
| `"left"` | 左对齐 |
| `"center"` | 居中 |
| `"right"` | 右对齐 |

### 箭头属性

```json
{
  "type": "arrow",
  "points": [[0, 0], [100, 0]],
  "startArrowhead": null,
  "endArrowhead": "arrow",
  "startBinding": {
    "elementId": "source-id",
    "focus": 0,
    "gap": 1
  },
  "endBinding": {
    "elementId": "target-id",
    "focus": 0,
    "gap": 1
  }
}
```

| arrowhead | 样式 |
|-----------|------|
| `null` | 无箭头 |
| `"arrow"` | 普通箭头 |
| `"bar"` | 竖线 |
| `"dot"` | 圆点 |
| `"triangle"` | 三角形 |

### 分组

```json
// 将元素分组
"groupIds": ["group-1"]

// 嵌套分组
"groupIds": ["group-1", "subgroup-a"]
```

### 元素绑定

容器与文字绑定：

```json
// 容器（矩形）
{
  "id": "box",
  "type": "rectangle",
  "boundElements": [
    { "id": "box-text", "type": "text" }
  ]
}

// 文字
{
  "id": "box-text",
  "type": "text",
  "containerId": "box"
}
```

## 常用配色

### 商务风格

| 用途 | 颜色 |
|------|------|
| 主色/文字 | `#1e3a5f` |
| 次色 | `#4a90d9` |
| 背景蓝 | `#dbeafe` |
| 背景绿 | `#dcfce7` |
| 背景黄 | `#fef3c7` |
| 强调绿 | `#10b981` |
| 边框灰 | `#6b7b8c` |
| 浅灰 | `#9ca3af` |

### 状态颜色

| 状态 | 背景色 | 边框色 |
|------|--------|--------|
| 成功 | `#dcfce7` | `#10b981` |
| 警告 | `#fef3c7` | `#f59e0b` |
| 错误 | `#fee2e2` | `#ef4444` |
| 信息 | `#dbeafe` | `#3b82f6` |

## 坐标与布局

- **x, y**: 左上角坐标
- **width, height**: 尺寸
- **建议间距**: 元素间 50-100px
- **建议宽度**: 整体 1200-1600px

## 最小化示例

一个矩形 + 文字：

```json
{
  "type": "excalidraw",
  "version": 2,
  "source": "claude-code",
  "elements": [
    {
      "id": "box",
      "type": "rectangle",
      "x": 100,
      "y": 100,
      "width": 120,
      "height": 60,
      "strokeColor": "#1e3a5f",
      "backgroundColor": "#dbeafe",
      "fillStyle": "solid",
      "strokeWidth": 2,
      "roughness": 1,
      "roundness": { "type": 3 },
      "seed": 1,
      "boundElements": [{ "id": "box-text", "type": "text" }]
    },
    {
      "id": "box-text",
      "type": "text",
      "x": 130,
      "y": 118,
      "width": 60,
      "height": 24,
      "text": "Hello",
      "fontSize": 20,
      "fontFamily": 1,
      "textAlign": "center",
      "strokeColor": "#1e3a5f",
      "containerId": "box"
    }
  ],
  "appState": { "viewBackgroundColor": "#ffffff" },
  "files": {}
}
```
