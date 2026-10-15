# Claude Code 使用指南

> 终端里的 AI 编程 Agent：能读写项目文件、执行命令、跑测试、改代码。输入 `/` 可随时查看全部命令。

---

## 1. 安装与启动

```bash
# 安装（需要 Node.js 18+）
npm install -g @anthropic-ai/claude-code

# 进入项目目录后启动
cd your-project
claude
```

常用启动参数：

| 命令 | 作用 |
|---|---|
| `claude` | 交互式会话（最常用） |
| `claude -p "帮我写个登录接口"` | 一次性执行，不进入交互界面 |
| `claude --continue` | 继续上一次会话 |
| `claude --dangerously-skip-permissions` | 跳过每次工具确认（慎用） |

---

## 2. 输入框快捷键

| 操作 | 作用 |
|---|---|
| `@文件名` | 引用文件/目录（自动补全） |
| `!命令` | 直接执行 shell 命令，输出带入对话 |
| `#内容` | 快速把这句话写入项目 `CLAUDE.md` |
| `Ctrl + C` | 中断当前回答 |
| `Esc` | 中断/回退 |
| `Ctrl + D`（两次） | 退出 Claude Code |
| 命令中夹带斜杠命令时 | Claude 回答中发命令会排队，等当前回合结束再执行 |

---

## 3. 斜杠命令速查（按场景分类）

### 3.1 项目初始化 / 记忆

| 命令 | 作用 |
|---|---|
| `/init` | 扫描代码库，自动生成 `CLAUDE.md` 入门文件 |
| `/memory` | 编辑 `CLAUDE.md`、管理自动记忆 |
| `/add-dir <路径>` | 为当前会话添加可访问的工作目录 |
| `/permissions` | 管理工具允许/询问/拒绝规则 |
| `/mcp` | 管理 MCP 服务器连接（重连/启用/禁用） |

### 3.2 任务进行中

| 命令 | 作用 |
|---|---|
| `/plan` | 进入计划模式：先出方案，确认后再动手改代码 |
| `/plan 修复登录态失效` | 带任务描述直接进计划模式 |
| `/model` | 切换模型（左右方向键可调推理强度） |
| `/context` | 可视化查看上下文窗口被什么占满了 |
| `/compact [保留重点]` | 压缩当前对话历史以释放上下文，**不重开对话** |
| `/clear` | 清空上下文开始新对话（换不相关任务时用） |
| `/rewind` | 回滚代码和对话到历史检查点（误操作后悔药） |
| `/btw 顺便问个问题` | 旁问一句，不污染主任务上下文 |

### 3.3 提交前 / 代码审查

| 命令 | 作用 |
|---|---|
| `/code-review high --fix` | 审查当前 diff 的正确性 Bug，`--fix` 直接修复 |
| `/code-review 1234` | 审查指定 PR 号 |
| `/code-review ultra` | 云端多 Agent 深度审查（需官方账号） |
| `/security-review` | 检查当前改动的安全漏洞 |
| `/simplify` | 并行 4 个 Agent 做代码简化（不查 Bug） |
| `/diff` | 查看当前所有改动 |

> 审查级别：`low` → `medium` → `high` → `xhigh` → `max` → `ultra`，越严越慢越费 token。
> `/review` 是 `/code-review` 的别名。

### 3.4 会话管理

| 命令 | 作用 |
|---|---|
| `/resume` | 打开历史会话选择器，恢复之前的对话 |
| `/rename 登录功能修复` | 给当前会话命名，方便以后找回 |
| `/tasks` | 查看当前会话的后台子 Agent 任务 |
| `/background` | 把整个会话挂到后台，释放终端继续跑 |
| `/branch` | 从当前对话分叉一条新方向 |

### 3.5 排障 / 状态

| 命令 | 作用 |
|---|---|
| `/status` | 查看版本、当前模型、账号状态 |
| `/usage`（别名 `/cost`、`/stats`） | 会话花费与额度用量 |
| `/doctor` | 安装/配置体检，自动诊断问题 |
| `/config theme=dark` | 不进面板直接改设置，如 `/config thinking=false` |

### 3.6 其他

| 命令 | 作用 |
|---|---|
| `/help` | 列出所有命令 |
| `/login` / `/logout` | 登录/退出 Anthropic 账号 |
| `/keybindings` | 打开快捷键配置文件 |
| `/copy [N]` | 复制最近一次（或倒数第 N 次）回答到剪贴板 |

---

## 4. CLAUDE.md（项目记忆）

Claude 每次启动自动读取的"项目说明书"，放这两个位置：

| 位置 | 作用域 |
|---|---|
| `~/.claude/CLAUDE.md` | 全局，所有项目生效 |
| `项目根目录/CLAUDE.md` | 仅当前项目 |

建议写上：
- 技术栈和目录结构
- 常用构建/测试命令（如 `npm test`、`pytest`）
- 代码风格约定、禁忌事项（如"不要改配置文件"）

---

## 5. 典型工作流

```
1. 新项目：  cd 项目 → claude → /init → /memory 补充约定
2. 改需求：  直接描述目标 → Claude 先调查 → 动手改 → 自己跑测试验证
3. 上下文满：/context 看占用 → /compact 压缩（继续聊）或 /clear（换任务）
4. 改坏了：  /rewind 回滚到检查点
5. 提交前：  /code-review high --fix → /security-review → git commit
```

经验法则：
- 不相关任务之间一定 `/clear`，混杂上下文会明显降低回答质量；
- 大改动前先 `/plan`，让它出方案你确认后再执行；
- 觉得回答变笨了，往往是上下文脏了，`/clear` 重开比继续聊更省 token。

---

## 6. 用 CC Switch 接第三方 API 的注意事项

当前你的环境（Kimi / DeepSeek 接入）：

- CC Switch 切换供应商本质 = 修改两个环境变量：`ANTHROPIC_BASE_URL` + `ANTHROPIC_AUTH_TOKEN`；
- Kimi 国内站 Anthropic 兼容地址：`https://api.moonshot.cn/anthropic`；
- 第三方平台走的是兼容接口，**不支持**官方账号的云端功能：`/code-review ultra`、cloud session、`/autofix-pr` 等可能不可用；
- `/usage`、`/cost` 只统计官方账号额度，第三方额度在 CC Switch 面板里看；
- 如果遇到模型行为异常，先用 `/status` 确认当前生效的是哪个供应商。
