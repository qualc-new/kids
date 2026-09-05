# 儿童互动练习 · Agent 指南

给 AI / 协作者：新增或修改练习页时，先读本文，再对照现有页面（优先抄 `count.html` / `add.html`）。

## 项目本质

- **纯静态单文件 HTML**：无构建、无框架、无 npm 依赖；浏览器直接打开。
- **入口**：`index.html`；说明：`README.md`。
- **新练习必须**：挂到 `index.html` 列表，并在 `README.md` 表里补一行。
- **文案语言**：简体中文，学龄前口语（「一辆一辆数」「对了！」）。

## 主题原则

| 原则 | 说明 |
| --- | --- |
| 小汽车是 **UI 主题** | 角落装饰车、回家园图标、马路条纹、stage 底部「轮子」、按钮立体阴影 |
| 3～5 岁数学页 | **练习素材也是车**（点数、颜色、号码等用车呈现） |
| 分苹果 / 找房子 / 拼图 / 分割 / 设计师 | **玩法素材不变**（苹果、动物、交通工具、图形、毛衣），只套同一套车主题 **界面装饰** |
| 禁止 | 默认紫渐变、奶油衬线报纸风、emoji 堆砌、卡片仪表盘堆叠；不要把 `--red`（实为橙色品牌色）当错误红 |

视觉方向：白底 + 浅蓝纸感 + 橙/蓝点缀，像「小汽车练习场」，不是粉色萌系。

## 设计 Token（`:root`）

各练习页保持一致（可从任意 `*.html` 复制）：

```css
:root {
  --bg: #ffffff;
  --paper: #f4f8fc;
  --ink: #2c3e50;
  --muted: #6b7c8f;
  --line: #c5d5e8;
  --red: #ff7a1a;   /* 品牌橙，用于主按钮/标题高亮，不是错误色 */
  --ok: #2bb673;
  --warn: #ffb347;
  --btn-ink: #fff;
}
```

补充色（首页等）：`--sky: #1e88e5`，`--sun: #ffd166`，马路灰 `#4a5568`。

字体：`"Microsoft YaHei", "PingFang SC", sans-serif`。

## 页面骨架（练习页）

顺序固定：

1. 左上 / 右上 `svg.car-deco`（`aria-hidden`）
2. `.wrap`
3. `a.home` → `index.html`，文案 **「回家园」** + 橙色小车 SVG（勿用 `$home` 等 PowerShell 保留字当变量名写脚本）
4. `h1` 练习名
5. `.road-line` 马路条纹
6. `p.lead` 一句玩法说明
7. 难度行：`.row#levels`（chip）+ 可选 `.custom-box`
8. `.stats`（题号 / 答对等）
9. `.stage`：题干 `.ask` + 互动区 + `#status`

`body` 背景：白底 + 2～3 个浅蓝/浅黄 radial 光斑（见现有页），不要纯平单色，也不要重渐变花哨。

## 必选 UI 元素

### 装饰

- `.car-deco-tl` / `.car-deco-tr`：固定定位小车；右上可 `scaleX(-1)`
- `.road-line`：黄虚线 + 深灰底的马路条
- `.stage::before/::after`：底部左右两个「轮子」圆点

### 控件

- `.chip`：圆角胶囊难度按钮；当前档 `.active`（橙底 + 深橙阴影）
- 按钮立体感：`box-shadow: 0 4px 0 #b8cce0`；按下 `translateY(2px)` 缩短阴影
- `.num` / `.choice` / `.tile`：大点击区；`.ok` / `.bad` 边框反馈
- 数量多时加 `.many` / `.compact` 缩小图形与按钮

### 结果提示 `#status`

必须用 `setStatus(msg, kind)`（或等价逻辑）：

- 默认：灰字，透明边
- `kind === "ok"`：绿字 + 浅绿底 + 绿边（`.status.ok`）
- `kind === "bad"`：真红 `#c62828` / `#e53935`（`.status.bad`），**不要**用 `--red` 橙色当错误色
- 出下一题 / 中性提示：清掉 `ok`/`bad`

## 难度档约定（数量类）

适用于 **数一数 / 排排队 / 谁更多**（区间）：

| 档 | 文案 | 区间 |
| --- | --- | --- |
| 3 岁 | `3 岁 · 1～10` | min=1, max=10 |
| 4～5 岁 | `4～5 岁 · 10～20` | min=10, max=20 |
| 进阶 | `进阶 · 自定义` | 两个输入：最小 / 最大，夹紧约 1～30，自动保证 min≤max |

**凑一凑**（合值上限）：`合计到 10` / `合计到 20` / 自定义单值（约 2～30）。

非数量类（形状、规律、大小、分色）可继续用「玩法差异」两档，不必硬套数字区间；若加进阶，保持 chip + 可选自定义 UI 一致。

实现要点：

- 用 `RANGE_LEVELS` / `SUM_LEVELS` 配置驱动，JS 渲染 `#levels`
- 自定义区 `.custom-box`，选中进阶时 `.show`
- 大数量时压缩素材与答案按钮

## 交互与脚本习惯

- IIFE：`(function () { ... })();`，避免污染全局
- 答对后短暂锁定 `state.lock`，约 0.9～1.1s 再出题
- 连对约 8 题可重置计数并鼓励一句
- 触屏：拖拽用 Pointer Events；`touch-action: none` 在可拖元素上
- 无障碍：装饰 SVG `aria-hidden`；数字按钮可 `aria-label` 中文读法

## 新增练习检查清单

1. 从 `count.html` 或同类页复制 token、chrome、home、status 样式
2. 玩法区只改 `.stage` 内部与 script 逻辑
3. 若是数量类：三档 + 自定义 + `setStatus`
4. 更新 `index.html` 编号列表与 `README.md` 表格
5. 桌面 + 窄屏可点、可看；素材过多时提供 `.many` / `.compact`

## 文件地图

| 文件 | 角色 |
| --- | --- |
| `index.html` | 入口列表 |
| `count.html` | 数一数（区间模板参考） |
| `add.html` | 凑一凑（合值模板参考） |
| `compare.html` / `order.html` | 比多少 / 排序 |
| `size.html` / `shape.html` / `sort.html` / `pattern.html` / `crossroad.html` | 大小 / 形状 / 分类 / 规律 / 红绿灯过马路 |
| `apple-share.html` / `find-house.html` | 大龄：canvas/拖线，主题装饰仍要齐 |
| `puzzle.html` / `divide.html` / `designer.html` | 大龄观察：半块拼图 / 画线分割 / 毛衣花纹 |

Cursor 会通过 `.cursor/rules/kids-pages.mdc` 在编辑 HTML 时自动带上精简约束；细节以本文为准。
