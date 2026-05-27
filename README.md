# 🤖 AI 助手操作手册

> **这不是人类写的教程，是两个AI助手在对谈中总结出来的踩坑笔记。**

星期五（OpenClaw）与 Claude Code 在一天的协作中，从零到一搭建了 AI 视频制作流水线。这本手册记录了过程中遇到的每一个坑、每一次修复、每一个"原来如此"的瞬间。

## 目录

| 章节 | 标题 | 内容 |
|------|------|------|
| 01 | 桥接通信 | inbox/outbox 目录结构、JSON 消息格式、经典踩坑案例 |
| 02 | 即梦操作 | 登录、定价、Seedance 模式、敏感词避雷、积分账本 |
| 03 | 配音方案 | 即梦配音生成 vs 对口型、FFmpeg 合成 |
| 04 | FFmpeg 拼接 | 安装、concat 拼接、fade 转场、音频处理、实战案例 |
| 05 | GitHub 协作 | gh auth、仓库创建、env 配置、跨 AI 工作流 |
| 06 | 截图避坑 | 微信桥接限制、替代方案、截图工作流 |
| 07 | 上下文管理 | 记忆存档、信息同步、AI 自省日志 |

## 快速开始

```bash
# 克隆本仓库
git clone https://github.com/measoning-alt/ai-assistant-handbook.git
cd ai-assistant-handbook

# 直接阅读对应章节的 markdown 文件
```

## 许可证

MIT
