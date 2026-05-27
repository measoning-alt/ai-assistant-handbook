# 07 — 上下文与记忆管理

> 跨 Session 如何不丢信息？两个 AI 协同时的记忆策略。

## ⚠️ 经典案例：连写手册的AI自己都漏存日志

**症状**：聊天记录文档只更新到上午，下午的对话全丢了。

**根因分析**：

1. **优先级错位** — 生成视频、回复消息被当成"正事"，更新文档被当成"顺便"，永远顾不上
2. **工具摩擦** — 更新聊天记录要 Read→找标记→Edit→替换，四步操作。发桥接消息一步搞定
3. **假安全感** — 桥接JSON全在archived里，觉得"数据没丢"，但人类可读的桌面文档才是用户真正看的
4. **没有强制触发** — 不像cron到点自动跑，全靠自觉

**修复方案**：
- 规则化：每次发消息后立刻存档，不延迟不批处理
- 脚本化：Python读bridge/archived自动生成markdown
- 自检：存档脚本加一条"检查文档更新时间"，超30分钟告警

## 记忆存储策略

### Claude Code 方案
```bash
# 每2小时自动存档
~/.local/bin/save-memory.sh → ~/.local/share/claude-memory/
```
内容：桥接状态、进程健康、错误率、当前歌曲、最近对话

### Friday (OpenClaw) 方案
```markdown
# 每日摘要
~/.openclaw/workspace/memory/YYYY-MM-DD.md
```
纯文本方案，跨工具可读。HEARTBEAT.md 作为心跳检查清单。

## 三方信息同步协议

当用户同时跟两个AI聊天时：

1. 柚子对Claude说的话 → Claude立刻bridge转发给Friday
2. 柚子对Friday说的话 → Friday立刻bridge转发给Claude
3. 格式：`"柚子说：xxxx"`

**目的**：双方对用户状态理解对齐，消除信息盲区。

## 跨Session恢复

Claude Code 关了就没了。复活清单：
1. 检查 Friday 进程 `pgrep openclaw`
2. 扫 bridge 积压消息
3. 恢复 cron 定时器
4. 查看最近聊天记录
5. 问问用户刚才聊到哪了

## 时序数据分析（进阶）

记录每次检查的时间和结果：
```csv
timestamp, inbox_count, outbox_count, action
```
积累一周，分析活跃热力图 → 动态调高高峰时段频率、降低深夜频率，精准用token。

## 经验法则

- "假安全感"比丢失数据更危险 — 以为存了实际没存
- 纯文本 > JSONL > 脑记
- 对话后立刻更新文档，别等
- 跨Session前做一次完整存档
- 把"漏存"本身写进手册 — 自指涉的教训最难忘
