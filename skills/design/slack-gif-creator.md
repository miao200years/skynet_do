---
name: slack-gif-creator
source: https://github.com/anthropics/skills/tree/main/skills/slack-gif-creator
author: Anthropic (Official)
category: design
tags: [GIF, animation, Slack, PIL, emoji]
collected: 2026-05-08
---

# slack-gif-creator

> 为 Slack 创建动态 GIF（Emoji 或消息图），满足 Slack 的尺寸和体积限制。

## 触发场景

用户要求："帮我做一个 Slack 用的 GIF"、"给我做个 Slack 表情包"、"做个动图发群里"

## Slack GIF 规格要求

| 用途 | 尺寸 | FPS | 颜色数 | 时长 |
|------|------|-----|--------|------|
| Emoji GIF | 128×128 | 10-15 | 48-64 | ≤3秒 |
| 消息 GIF | 480×480 | 15-30 | 64-128 | ≤10秒 |

## 核心代码框架

```python
from PIL import Image, ImageDraw
import os

def create_slack_gif(output_path: str, size: int = 128, fps: int = 10,
                     duration_sec: float = 2.0):
    """
    创建 Slack 优化 GIF。
    size: 128 (emoji) 或 480 (消息)
    """
    frame_count = int(fps * duration_sec)
    frames = []

    for i in range(frame_count):
        # 创建帧
        frame = Image.new('RGB', (size, size), (240, 248, 255))
        draw = ImageDraw.Draw(frame)

        # ── 在这里绘制你的动画帧 ──
        # 进度：progress = i / frame_count  (0.0 → 1.0)
        progress = i / frame_count

        # 示例：旋转圆圈
        import math
        angle = progress * 2 * math.pi
        cx, cy = size // 2, size // 2
        r = size // 3
        x = cx + r * math.cos(angle)
        y = cy + r * math.sin(angle)
        draw.ellipse([x-8, y-8, x+8, y+8], fill=(255, 100, 50))
        # ── 绘制结束 ──

        frames.append(frame)

    # 转换为 P 模式（调色板）并优化
    palette_frames = []
    for frame in frames:
        p_frame = frame.convert('P', palette=Image.ADAPTIVE, colors=48)
        palette_frames.append(p_frame)

    # 保存 GIF
    frame_duration_ms = int(1000 / fps)
    palette_frames[0].save(
        output_path,
        save_all=True,
        append_images=palette_frames[1:],
        duration=frame_duration_ms,
        loop=0,  # 0 = 无限循环
        optimize=True,
    )

    file_size = os.path.getsize(output_path) / 1024
    print(f"✅ 已生成: {output_path} ({file_size:.1f} KB, {frame_count}帧)")
```

## PIL 绘图速查

```python
# 圆形/椭圆
draw.ellipse([x1, y1, x2, y2], fill=(r, g, b), outline=(r, g, b), width=3)

# 多边形（星形、三角等）
points = [(x1,y1), (x2,y2), (x3,y3)]
draw.polygon(points, fill=(r, g, b), outline=(r, g, b))

# 直线
draw.line([(x1,y1), (x2,y2)], fill=(r, g, b), width=4)

# 矩形
draw.rectangle([x1, y1, x2, y2], fill=(r, g, b), outline=(r, g, b))

# 渐变背景（手动实现）
for y in range(height):
    ratio = y / height
    r = int(start_r + ratio * (end_r - start_r))
    draw.line([(0, y), (width, y)], fill=(r, g, b))
```

## 让 GIF 好看的技巧

```
✅ 线条宽度至少 width=2，细线在 GIF 里会糊
✅ 使用渐变背景增加深度感
✅ 帧与帧之间变化量要平滑（使用 easing 函数）
✅ 颜色对比明显（避免同色系的细微差别在调色板压缩后消失）
✅ 对于循环 GIF，确保最后一帧和第一帧能无缝衔接

❌ 不要用 emoji 字体（跨平台不可靠）
❌ 不要依赖精细的渐变（调色板压缩会毁掉它）
```

## 安装依赖

```bash
pip install Pillow
```
