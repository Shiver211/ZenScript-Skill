# ZenScript Skill for AI Agent

为 AI Agent 提供 CraftTweaker 1.12.2 ZenScript 的正确 API 参考，防止 AI agent 自造方法。

## 功能

- **反幻觉**：agent 写 ZenScript 代码时只使用文档中记录的真实 API
- **自动激活**：编辑 `.zs` 文件或提到 CraftTweaker 时自动加载
- **双层参考**：常用 API 内嵌在 SKILL.md 中，完整 API 按需查阅参考文件
- **中文为主**：说明和注释用中文，API 名保持英文

## 安装

将 `skills/claude/zenscript/`和 `references` 目录复制到你的整合包项目的 `.claude/skills/` 下。

```
你的整合包/
└── .claude/
    └── skills/
        └── zenscript/
            ├── SKILL.md
            └── references
```

## 其他 AI Agent 安装

### 通用步骤

1. 根据你的 agent，将 `skills/<agent>/` 内的文件复制到对应位置
2. 将`references` 复制到 `.md` 同级目录

### Codex

```
.codex/skills/zenscript/
├── SKILL.md         
└── references      
```

### GitHub Copilot

```
.github/skills/zenscript/
├── SKILL.md
└── references                              
```

### Trae

```
.trae/skills/zenscript
├── SKILL.md    
└── references                 
```

## 使用

无需手动调用。当你在项目中编辑 `.zs` 文件或与 Agent 讨论 CraftTweaker 相关内容时，skill 会自动激活。

你也可以按照不同 Agent 的方式手动调用。

## 包含的 API 参考

- 原版（references/vanilla/）
- 模组（references/mods/）
- 工具（references/utils/）

## 贡献模组扩展

本项目欢迎社区贡献！你可以为任何有 CraftTweaker 集成的模组添加 API 参考。详细说明见 [CONTRIBUTING.md](CONTRIBUTING.md)。