---
name: algorithmic-art
source: https://github.com/anthropics/skills/tree/main/skills/algorithmic-art
author: Anthropic (Official)
category: design
tags: [art, generative, p5.js, creative-coding, visual]
collected: 2026-05-08
---

# algorithmic-art

> 用 p5.js 创作生成艺术，基于算法哲学和有种子的随机性。

## 触发场景

用户要求：
- 生成艺术、算法艺术、创意编程
- 粒子系统、流场、noise field 可视化
- 参数化视觉作品
- 任何"用代码画画"的需求

## 两阶段创作流程

### 第一阶段：算法哲学（.md 文件）

在写代码前，先确立一个**算法运动宣言**：

```
命名运动（1-2个词）：
"有机湍流" / "量子谐波" / "涌现静默" / "分形呼吸"

核心哲学（4-6段）涵盖：
- 计算过程与数学关系如何表达？
- Noise 函数和随机性如何分布？
- 粒子行为和力场动态如何运作？
- 时间演化和系统状态如何变化？
- 参数变化如何产生涌现复杂性？

关键要求：
- 强调"算法工艺感"——最终代码应像是耗费无数小时精心打磨的
- 体现"深厚的计算专业知识"
```

### 第二阶段：p5.js 实现（.html + .js）

**核心结构模板：**

```javascript
// sketch.js — 90% 算法生成，10% 关键参数
const CONFIG = {
  seed: 42,           // 可重现的随机种子
  particleCount: 800,
  noiseScale: 0.003,
  speed: 1.5,
  alpha: 15,          // 轨迹透明度（拖尾效果）
}

let particles = []

function setup() {
  createCanvas(800, 800)
  randomSeed(CONFIG.seed)
  noiseSeed(CONFIG.seed)
  colorMode(HSB, 360, 100, 100, 100)
  background(0)

  // 初始化粒子
  for (let i = 0; i < CONFIG.particleCount; i++) {
    particles.push({
      x: random(width),
      y: random(height),
      hue: random(360),
    })
  }
}

function draw() {
  // 半透明覆盖（产生拖尾效果）
  background(0, 0, 0, CONFIG.alpha)

  particles.forEach(p => {
    // Perlin noise 驱动流场
    let angle = noise(p.x * CONFIG.noiseScale,
                      p.y * CONFIG.noiseScale,
                      frameCount * 0.005) * TWO_PI * 4

    p.x += cos(angle) * CONFIG.speed
    p.y += sin(angle) * CONFIG.speed

    // 边界环绕
    if (p.x < 0) p.x = width
    if (p.x > width) p.x = 0
    if (p.y < 0) p.y = height
    if (p.y > height) p.y = 0

    // 绘制粒子
    stroke(p.hue, 80, 90, 80)
    strokeWeight(1.5)
    point(p.x, p.y)
  })
}
```

**HTML 嵌入模板：**

```html
<!DOCTYPE html>
<html>
<head>
  <style>
    body { margin: 0; background: #000; display: flex;
           justify-content: center; align-items: center; height: 100vh; }
  </style>
</head>
<body>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/p5.js/1.9.0/p5.min.js"></script>
  <script src="sketch.js"></script>
</body>
</html>
```

## 常见算法模式

| 模式 | 关键技术 |
|------|---------|
| 流场 | `noise(x, y, t)` → 角度 |
| 粒子系统 | 更新位置 + 半透明拖尾 |
| L系统 | 递归字符串替换 → 树/分形 |
| 细胞自动机 | Conway's Game of Life 变体 |
| 反应扩散 | Gray-Scott 模型 |
| 波形叠加 | sin/cos 多频叠加 |

## 质量标准

- 必须有**可重现性**（randomSeed + noiseSeed）
- 代码注释说明每个参数的视觉作用
- 运行流畅（60fps），无明显卡顿
- 视觉效果有**深度感**，不是简单几何图形
