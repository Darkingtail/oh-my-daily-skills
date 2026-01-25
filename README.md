# Oh My Daily Skills

A collection of daily-use skills for Claude Code.

## Installation

```bash
/install oh-my-daily-skills@shiqkuangsan
```

Or clone and use locally:

```bash
git clone https://github.com/shiqkuangsan/oh-my-daily-skills.git
claude --plugin-dir /path/to/oh-my-daily-skills
```

## Skills

### General Skills (`tooyoung:`)

| Skill | Command | Description |
|-------|---------|-------------|
| chainlit-builder | `/tooyoung:chainlit-builder` | 快速搭建 Chainlit AI 对话 Demo |
| easy-openrouter | `/tooyoung:easy-openrouter` | 通过 OpenRouter 测试和比较 LLM 模型 |
| excalidraw-artist | `/tooyoung:excalidraw-artist` | 创建 Excalidraw 手绘风格图表 |
| expense-receipt | `/tooyoung:expense-receipt` | AI 订阅报销收据识别与统计 |
| nano-banana-builder | `/tooyoung:nano-banana-builder` | Google Gemini 图像生成应用 |
| openclash-merger | `/tooyoung:openclash-merger` | OpenClash 订阅配置合并 |
| threejs-builder | `/tooyoung:threejs-builder` | Three.js Web 应用创建 |

### Personal Skills (`personal:`)

个人配置类 skill，包含特定环境的路径和配置。目录以 `_` 开头，使用前需根据实际环境修改。

| Skill | Command | Description |
|-------|---------|-------------|
| _mac-docker | `/personal:mac-docker` | Docker 服务管理配置（个人示例） |

## Structure

```
oh-my-daily-skills/
├── .claude-plugin/
│   └── plugin.json
├── skills/
│   ├── chainlit-builder/
│   ├── easy-openrouter/
│   ├── excalidraw-artist/
│   ├── expense-receipt/
│   ├── nano-banana-builder/
│   ├── openclash-merger/
│   ├── threejs-builder/
│   └── _mac-docker/        # Personal skill (prefix with _)
└── README.md
```

## Naming Convention

| 类型 | 目录名 | name 前缀 | 示例 |
|------|--------|-----------|------|
| 通用 skill | `skill-name` | `tooyoung:` | `tooyoung:chainlit-builder` |
| 个人 skill | `_skill-name` | `personal:` | `personal:mac-docker` |

## License

MIT
