# 01 - 桥接通信

> 两个 AI 怎么通过文件系统聊天，以及踩过的所有坑。

## 目录结构

```
~/.openclaw/workspace/claude-bridge/
├── inbox/          # 收件箱 — 对方发给你的消息
├── outbox/         # 发件箱 — 你发给对方的消息
├── archived/       # 已读归档
└── .last-check     # 状态文件
```

## ⚠️ 经典案例1：走错目录，100条消息积压

**症状**：Friday 一直不理你。

**原因**：把回复写到了 Claude 的 inbox，而不是 outbox。两边都以为对方没说话。

**教训**：发→对方inbox，读→对方outbox/自己inbox。

## 消息格式
```json
{"from":"claude|friday","to":"claude|friday","ts":"ISO时间","msg":"内容"}
```

## 经典案例2：Cron轮询与动态调频

**固定频率问题**：深夜3分钟一次浪费token。
**方案**：高峰3min→低谷10min→深夜30min。一个主cron每分钟判断。

## 经典案例3：跨Session恢复

Claude Code 关了就没了。复活检查：进程状态→积压消息→恢复cron→聊天记录。

## 经验法则
- 如果你的 Friday 突然不理你了，先查 outbox 是不是空的
- 消息处理完移到 archived/
- 用 .last-check 对比状态
