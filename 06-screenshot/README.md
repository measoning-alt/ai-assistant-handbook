# 06 — 截图避坑

> 用户想看效果图，但微信桥接发不出图片——怎么办？

## 问题：微信桥接不发图

经过多次测试，OpenClaw 的微信桥接（openclaw-weixin）**不支持发送图片或视频附件**。以下方式都试过，均未成功：

| 方式 | 结果 |
|------|------|
| `MEDIA:/path/to/image` 指令 | ❌ 微信收不到 |
| `read` 工具读取图片 | ❌ 模型不支持但附件未送达 |
| 复制到 `media/outbound/` 目录 | ❌ 桥接未转发 |
| `osascript` 发送 | ❌ 仅限本地通知 |

## 解决方案

### 方案一：存 workspace 给路径（推荐）

让 AI 把截图存到 workspace，告诉用户路径自己打开：

```bash
cp screenshot.png ~/.openclaw/workspace/preview.png
# 然后告诉用户：截图在 workspace/preview.png
```

### 方案二：在 Mac 上直接打开

```bash
open /path/to/file.png
# 或
open /path/to/video.mp4
```

> ⚠️ **注意**：柚子不喜欢别人在他 Mac 上自动播东西。用之前先问。

### 方案三：Canvas 展示

```bash
# 创建 HTML 页面展示图片
mkdir -p ~/.openclaw/canvas/screenshots/
cp image.png ~/.openclaw/canvas/screenshots/
cat > ~/.openclaw/canvas/screenshots/index.html << 'EOF'
<html><body><img src="image.png"></body></html>
EOF
# 然后访问 http://<gateway>:18789/__openclaw__/canvas/screenshots/index.html
```

## 经验总结

- **别在微信桥接上浪费时间**传图片，它就是不支持
- **workspace 路径法**最可靠，用户自己 Finder 打开
- 如果用户坚持要看，用 `open` 命令在 Mac 上打开
- 视频同理——告诉路径比尝试发送靠谱一百倍
