---
name: presentation-structure
description: 关于演示文稿幻灯片格式、权重系统、导航和章节结构的知识
---

# 演示文稿结构技能

关于 `presentation/index.html` 中演示文稿结构如何组织的知识。

## 文件位置

`presentation/index.html` — 一个包含内联CSS和JS的单文件HTML演示文稿。

## 幻灯片格式

每个幻灯片是一个带有 `data-slide`（顺序编号）和可选 `data-level`（过渡点的旅程级别）的div：

```html
<!-- 普通幻灯片 — 从前一个 data-level 幻灯片继承级别 -->
<div class="slide" data-slide="12">
    <h1>幻灯片标题</h1>
    <!-- 内容 -->
</div>

<!-- 级别过渡幻灯片 — 为本幻灯片及之后的所有幻灯片设置新级别 -->
<div class="slide section-slide" data-slide="10" data-level="low">
    <h1>章节名称</h1>
    <p class="section-desc">Level: Low — 本章节描述</p>
</div>

<!-- 标题幻灯片（居中） -->
<div class="slide title-slide" data-slide="1">
    <h1>演示文稿标题</h1>
    <p class="subtitle">副标题文本</p>
</div>
```

## 旅程条级别系统

演示文稿使用4级系统而非累积百分比：

- 级别通过关键过渡幻灯片（章节分隔符）上的 `data-level` 属性设置
- `data-level` 幻灯片之后的所有幻灯片继承该级别，直到下一个过渡
- 旅程条填充至 Low/Medium/High/Pro 分别为 25%/50%/75%/100%
- 旅程条在幻灯片1（标题幻灯片）隐藏；从幻灯片2开始显示
- 第一个 `data-level` 之前的幻灯片（幻灯片2-9）显示空条（尚未设置级别）
- `.level-badge` 由JS注入到带有 `data-level` 的幻灯片的 `<h1>` 中 — 不要在HTML中硬编码

### 各章节的级别转换

| 章节 | 幻灯片范围 | data-level | 条高度 |
|---------|-------------|------------|------------|
| Part 0: 介绍 | 幻灯片 1-4 | （无） | 隐藏/空 |
| Part 1: 前提条件 | 幻灯片 5-9 | （无） | 空 |
| Part 2: 更好的提示 | 幻灯片 10-17 | `low` | 25% |
| Part 3: 项目记忆 | 幻灯片 18-24 | `medium` | 50% |
| Part 4: 结构化工作流 | 幻灯片 25-28 | （继承medium） | 50% |
| Part 5: 领域知识 | 幻灯片 29-33 | `high` | 75% |
| Part 6: 代理工程 | 幻灯片 34-46 | `high` | 75% |
| 附录 | 幻灯片 47+ | （继承high） | 75% |

## 导航系统

- `goToSlide(n)` — 用于TOC链接，必须与实际的 `data-slide` 编号匹配
- `totalSlides` 从DOM自动计算（`document.querySelectorAll('[data-slide]').length`）
- 方向键、空格和触摸滑动用于导航
- 幻灯片计数器在左下角显示 `当前/总数`

## 重新编号规则

添加、删除或重新排序幻灯片后：
1. 重新编号所有 `data-slide` 属性，从1开始顺序编号
2. 更新TOC/Journey Map幻灯片中的所有 `goToSlide()` 调用
3. JS `totalSlides` 自动计算 — 无需手动更新
4. 验证没有间隙或重复

## 章节分隔符格式

章节分隔符使用 `section-slide` 类。级别转换章节分隔符带有 `data-level`，并在描述中显示级别名称：

```html
<div class="slide section-slide" data-slide="10" data-level="low">
    <p class="section-number">Part 2</p>
    <h1>更好的提示</h1>
    <p class="section-desc">Level: Low — 有效提示带来真实结果。</p>
</div>
```

JS将在运行时将 `.level-badge`（例如 "→ Low"）注入到级别转换时 `<h1>` 中 — 不要在HTML中手动添加这些。