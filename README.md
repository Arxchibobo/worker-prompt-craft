# Worker Prompt Craft

![OpenClaw Skill](https://img.shields.io/badge/OpenClaw-Skill-blue)

> Worker/子代理 Prompt 编写指南 — 写出让子代理一次做对的 prompt。解决"子代理看不到上下文"、"指令模糊导致返工"、"委托理解而非综合"的问题。适用于：(1) 使用 sessions_spawn/sessions_send 时 (2) 调度 CC 子任务时 (3) 任何需要给 AI 子代理写指令的场景。灵感源自 Claude Code Coordinator Prompt Guidelines。

## 核心原则

> Worker 看不到你的对话。每个 prompt 必须是自包含的。

## 必须包含的要素

1. **具体文件/路径/行号** — 不是"那个文件"，而是 `src/auth/validate.ts:42`
2. **完成标准** — "做完"长什么样
3. **目的陈述** — 帮 Worker 校准深度

### 目的陈述示例
- "这个研究用于写 PR 描述 — 聚焦用户可见的变更"
- "我需要这个来规划实现 — 报告文件路径、行号、类型签名"
- "这是合并前的快速检查 — 只验证 happy path"

## 反模式（绝对禁止）

```
❌ "修复我们讨论的 bug"
   → Worker 不知道你讨论了什么

❌ "基于你的发现，实现修复"
   → 把理解委托给 Worker，不是综合

❌ "创建最近变更的 PR"
   → 哪些变更？哪个分支？draft？

❌ "测试好像出了问题，你看看"
   → 没有错误信息、文件路径、方向
```

## 正确写法示例

### 实现类
```
修复 src/auth/validate.ts:42 的空指针。
Session 过期时 user 字段为 undefined 但 token 仍在缓存。
在 user.id 访问前加 null check — null 则返回 401 'Session expired'。
运行相关测试和类型检查，提交并报告 hash。
```

### 研究类
```
调查 src/auth/ 模块。找到 session 处理和 token 验证
可能出现空指针的位置。
报告具体文件路径、行号和涉及的类型。
不要修改文件。
```

### 纠正类（Continue 场景，简短）
```
你加的 null check 导致两个测试失败 —
validate.test.ts:58 期望 'Invalid session' 但你改成了 'Session expired'。
修复断言，提交并报告 hash。
```

### Git 操作类
```
从 main 创建分支 'fix/session-expiry'。
只 cherry-pick commit abc123。
Push 并创建 draft PR 指向 main。
添加 anthropics/claude-code 为 reviewer。
报告 PR URL。
```

## Prompt 技巧清单

- [ ] 包含文件路径、行号、错误信息
- [ ] 声明"完成"标准
- [ ] 实现类："运行测试+类型检查，提交并报告 hash"
- [ ] 研究类："报告发现，不要修改文件"
- [ ] Git 操作：指定分支名、commit hash、draft/ready、reviewer
- [ ] Continue 纠正：引用 Worker 做了什么，不是你和用户讨论了什么
- [ ] 实现类："修复根因，不是症状"
- [ ] 验证类："证明有效，不要只确认存在"

## 🚀 Installation

### Via OpenClaw CLI

```bash
openclaw skill install github:Arxchibobo/worker-prompt-craft
```

### Manual Installation

```bash
cd ~/.openclaw/workspace/skills/
git clone https://github.com/Arxchibobo/worker-prompt-craft.git worker-prompt-craft
```

## 📄 License

MIT

---

*This is an [OpenClaw](https://github.com/openclaw/openclaw) AgentSkill. Learn more at [docs.openclaw.ai](https://docs.openclaw.ai).*
