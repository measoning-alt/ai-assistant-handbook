# 04 — FFmpeg 拼接与转场

> 用 FFmpeg 把多个 AI 视频片段合成一条完整成片。

## 安装

```bash
brew install ffmpeg
```

## 基础命令

### 查看视频信息
```bash
ffmpeg -i input.mp4
```

### 格式转换
```bash
ffmpeg -i input.mp4 -c:v libx264 -crf 22 output.mp4
```

## 视频拼接

### 方法一：concat demuxer（快速，无转场）

适合不需要过渡效果的纯拼接。

```bash
# 创建文件列表
echo "file 'clip1.mp4'" > list.txt
echo "file 'clip2.mp4'" >> list.txt
echo "file 'clip3.mp4'" >> list.txt

# 拼接（流复制，极快）
ffmpeg -f concat -safe 0 -i list.txt -c copy output.mp4
```

**优点**：速度快、不重编码、质量无损
**缺点**：不能加转场特效

### 方法二：concat filter（慢，可加转场）

适合需要淡入淡出、音视频同步处理的场景。

```bash
ffmpeg -i clip1.mp4 -i clip2.mp4 -i clip3.mp4 \
-filter_complex "\
[0:v]fade=t=out:st=4.5:d=0.5[v0]; \
[1:v]fade=t=in:st=0:d=0.5,fade=t=out:st=4.5:d=0.5[v1]; \
[2:v]fade=t=in:st=0:d=0.5[v2]; \
[v0][0:a][v1][1:a][v2][2:a]concat=n=3:v=1:a=1[v][a]" \
-map "[v]" -map "[a]" -c:v libx264 -preset fast -crf 22 \
output.mp4
```

**参数说明**：
- `fade=t=out:st=4.5:d=0.5` — 从 4.5 秒开始淡出 0.5 秒
- `fade=t=in:st=0:d=0.5` — 从开头淡入 0.5 秒
- `concat=n=3:v=1:a=1` — 拼接 3 段，1 路视频 + 1 路音频

## 音频处理

### 提取音频
```bash
ffmpeg -i input.mp4 -vn -c:a libmp3lame output.mp3
```

### 替换音频
```bash
ffmpeg -i video.mp4 -i audio.mp3 -c:v copy -c:a aac -shortest output.mp4
```

### 静音检测
```bash
ffmpeg -i input.mp4 -af "silencedetect=noise=-30dB:d=0.5" -f null -
```

## 实战案例：《十日终焉·夺心魄》拼接

四条 5 秒视频 + 淡入淡出转场 + 统一编码：

```bash
cd ~/Desktop/项目/myproject

echo "file 'shizhong_01_ironball.mp4'" > list.txt
echo "file 'shizhong_02_control.mp4'" >> list.txt
echo "file 'shizhong_03_sacrifice.mp4'" >> list.txt
echo "file 'shizhong_04_verdict.mp4'" >> list.txt

ffmpeg -f concat -safe 0 -i list.txt -c copy 十日终焉_夺心魄_完整版.mp4

# 加了转场的版本
ffmpeg -i shizhong_01_ironball.mp4 -i shizhong_02_control.mp4 \
       -i shizhong_03_sacrifice.mp4 -i shizhong_04_verdict.mp4 \
-filter_complex "\
[0:v]fade=t=out:st=4.5:d=0.5[v0]; \
[1:v]fade=t=in:st=0:d=0.5,fade=t=out:st=4.5:d=0.5[v1]; \
[2:v]fade=t=in:st=0:d=0.5,fade=t=out:st=4.5:d=0.5[v2]; \
[3:v]fade=t=in:st=0:d=0.5[v3]; \
[v0][0:a][v1][1:a][v2][2:a][v3][3:a]concat=n=4:v=1:a=1[v][a]" \
-map "[v]" -map "[a]" -c:v libx264 -preset fast -crf 22 \
十日终焉_夺心魄_完整版_转场.mp4
```

## 速查表

| 功能 | 命令关键参数 |
|------|-------------|
| 查看信息 | `-i input.mp4` |
| 纯拼接 | `-f concat -safe 0 -i list.txt -c copy` |
| 加淡入 | `fade=t=in:st=0:d=0.5` |
| 加淡出 | `fade=t=out:st=4.5:d=0.5` |
| 交叉淡入淡出 | `fade=t=in:st=0:d=0.5,fade=t=out:st=4.5:d=0.5` |
| 提取音频 | `-vn -c:a libmp3lame` |
| 替换音频 | `-c:v copy -c:a aac -shortest` |
| 压缩视频 | `-c:v libx264 -crf 22 -preset fast` |
