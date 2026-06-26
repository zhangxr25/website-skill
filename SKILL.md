---
name: intco-decor-report
version: 1.0.0
description: "用 INTCO Decor 品牌视觉（蓝主色 #1a3d6e + 橙强调 #e85a2e + Inter 字体 + 白色卡片 + sticky header + 深蓝 top-strip + 大 hero banner + 一致的 section 节奏）生成单页 HTML 报告并导出 PDF。**触发场景**：用户说『按 INTCO Decor 风格』『用 Decor 视觉』『做一份 INTCO Decor 风格的报告/市场情报/产品手册/landing page』，或在 intco-wallskirt / wallskirt / 海外产品目录类项目里要做新的可视化资料时。**不触发**：BASIC Medical（用 basicmedical-website skill）、英科再生客户情报（用 intco-recycling-customer-intelligence）、销售易 / 报销 / 名片识别 / 飞书卡片等。"
metadata:
  requires:
    bins: ["python3", "Google Chrome.app"]
  filesNeeded:
    - "template.html (本目录)"
    - "用户提供的图片"
    - "用户提供的报告内容（markdown / 数据点 / 文字）"
---

# intco-decor-report

## 1. 何时使用本 Skill

### 1.1 触发条件

以下任一即触发：

- 用户明确说"按 INTCO Decor 风格"、"用 Decor 视觉"、"INTCO Decor 模板"
- 用户给一份内容（市场情报、产品介绍、行业 readout、活动总结）要做成"专业可发外的可视化报告"
- 上下文涉及 intco-wallskirt / intco-decor / intco-frames / 海外 B2B 装饰建材产品目录、Amazon 市场情报、品牌物料
- 用户给 zip / md / 数据表 + 图片，要"做一份网页 / 报告 / PDF"，且没指定别的风格

### 1.2 不触发

- BASIC Medical 网站 → 用 `basicmedical-website` skill
- 英科再生**客户情报建联报告**（针对具体公司做背调）→ 用 `intco-recycling-customer-intelligence`
- 飞书内的卡片回复 → 用 `feishu-card`
- 用户明确要"另一种风格"或"参考某竞品视觉"

## 2. 视觉规范（不可动的部分）

| Token | 值 | 用途 |
|---|---|---|
| `--primary` | `#1a3d6e` | 大标题、Logo、按钮、强调数字 |
| `--primary-dk` | `#0f2647` | top-strip 底色、footer 底色 |
| `--accent` | `#e85a2e` | "Market Readout" 小标签、价格、signal-card 左边线、takeaway 编号、size-chip active 态 |
| `--accent-dk` | `#c9461b` | 按钮 hover |
| `--gray-50/100/200/300/500/700/900` | 见 template | 卡片底 / 边框 / 次要文字 |
| 字体 | Google Fonts `Inter` 400–800 | 全站统一 |
| 圆角 | 卡片 `8px`，按钮 `4px` | |
| shadow | `var(--shadow)` / `var(--shadow-lg)` | 卡片悬浮 |
| max-width | `1280px`（section-inner） | 内容居中宽度上限 |

**绝不改**：主色 / 字体 / 圆角 / max-width / hero overlay 渐变方向。
**可改**：每个 section 的 grid 列数、卡片数量、内容文字、配图。

## 3. 标准 section 库（按需取用）

template.html 提供以下 section building blocks，按报告内容挑选：

1. **top-strip**（深蓝细条）+ **header**（logo + 右侧 meta） — 必有
2. **hero**（满宽图 + 深蓝渐变 overlay + 标题 + lead 段 + 橙色小标签） — 必有
3. **signal cards**（3×1 横排，大数字 + label + desc，左边线橙色） — 数据/指标看板用
4. **lead 段**（大字加粗 highlight 段） — section 开篇引子
5. **takeaway cards**（3×1 横排，"01 / XXX" 编号 + 短标题 + 段落） — 结论性要点
6. **scenes gallery**（2×2 网格，图 + 标题 + 一行描述） — 场景/用例展示
7. **product cards**（垂直堆叠，左 240px 方图 + 右 specs 4×2 网格 + Note + Evidence + 链接按钮，rank chip 橙色叠图左上） — Top N 产品/案例
8. **methodology 2 列**（左 included/excluded ▸/✕ 列表，右 Data Read 列表） — Scope/方法论
9. **footer**（深蓝底，居中品牌 + 一行 meta） — 必有

## 4. 执行步骤

### Step 1 — 确认输入

读用户给的素材：
- 文字内容（md / 段落 / 数据点）
- 图片（hero / scenes / products 各几张）
- 输出位置（默认 `~/Desktop/<报告名>/`，没指定就用 `intco-decor-report-<日期>`）
- 报告主题 / 标题 / 副标题

如果信息不全（图缺、数据缺、标题缺），**先问清楚再开工**，不要瞎填。

### Step 2 — 准备目录结构

```
~/Desktop/<报告名>/
├── index.html
├── images/
│   ├── scenes/           # hero 图、4 张场景图
│   └── products/         # rank_N 产品图（如有）
└── <报告名>_<日期>.pdf    # 生成后的 PDF
```

### Step 3 — 套模板生成 HTML

1. 复制 `~/.claude/skills/intco-decor-report/template.html` → 目标目录 `index.html`
2. 按内容删除不需要的 section（比如纯文字报告就不要 product cards）
3. 替换所有 `{{PLACEHOLDER}}` 文字
4. 替换 `<img src="...">` 为实际图片路径
5. signal/takeaway/scene/product cards 按需要复制对应块，调数量

### Step 4 — 生成 PDF（用户要 PDF 时）

PDF 走 **单页超长方案**（single-page PDF）：整份报告 = 1 页 PDF，纸张尺寸 = 内容真实总高度。
跟在浏览器里滚动一模一样的连贯感，**没有任何分页切断**。

**默认手机版（414 px 宽）**：因为常见分发场景是手机（微信/邮件/云盘转发，对方手机 PDF reader 打开）。
要桌面版（1280 px 宽）需要同时改两处：(1) `template.html` 末尾 JS 里 `const w = 414` 改成 1280，(2) 下面 Chrome 命令 `--window-size=414,800` 改成 `--window-size=1280,800`。

#### 手机版 PDF（默认）

```bash
"/Applications/Google Chrome.app/Contents/MacOS/Google Chrome" \
  --headless --disable-gpu \
  --window-size=414,800 \
  --no-pdf-header-footer --print-to-pdf-no-header \
  --virtual-time-budget=15000 \
  --print-to-pdf="<目标目录>/<报告名>_Mobile_<日期>.pdf" \
  "file://<目标目录绝对路径>/index.html"
```

#### 桌面版 PDF（按需）

先改 `template.html` 里 `const w = 414` → `const w = 1280`，再:

```bash
"/Applications/Google Chrome.app/Contents/MacOS/Google Chrome" \
  --headless --disable-gpu \
  --window-size=1280,800 \
  --no-pdf-header-footer --print-to-pdf-no-header \
  --virtual-time-budget=15000 \
  --print-to-pdf="<目标目录>/<报告名>_Desktop_<日期>.pdf" \
  "file://<目标目录绝对路径>/index.html"
```

**关键参数**：
- `--window-size` width **必须** 跟 template JS 里 `const w` 一致。否则 DOM 测量按一个宽度量、@page 按另一个宽度 print → reflow 差异 → PDF 底部大块空白或多页。
- `--virtual-time-budget=15000`：15 秒等 JS 跑完 + 图片加载完。
- `--no-pdf-header-footer` / `--print-to-pdf-no-header`：去掉 Chrome 自动加的页眉页脚（页码、日期那些）。

**原理**（template.html 末尾的 JS 做的事）：
1. `load` 事件后用 `requestAnimationFrame` 等一帧（让 layout settle）
2. 找最后一个 `<footer>` 或 `<section>`，量 `getBoundingClientRect().bottom`
3. 加 `+200` buffer（print-reflow 字体/图片 late-layout 差异）
4. 注入 `<style>@page { size: <w>px <H>px; margin:0 }`
5. Chrome `--print-to-pdf` 读这个 @page → 输出 1 张 <w>×H 的纸

**适用场景**（事实）：PDF reader 滚动阅读（手机/电脑/Adobe Reader/Preview/Chrome/iOS Books/Android），微信/邮件/云盘分发。
**不适用场景**（事实）：标准纸张打印（A4/Letter 装不下超长 PDF）—— 要打印需另出一份分页 PDF（不在本 skill 范围内）。

`@media print and (min-width:1025px)` 块只在**桌面 print** 时生效（强制多列）。手机 print（viewport 414）自动走 `@media (max-width:1024px)` 的单列响应式，**无需特殊处理**。

### Step 5 — 验证

用 Read 工具读 PDF 前 2-3 页，检查：
- hero 满宽显示
- signal/takeaway 横排没塌成单列（如果塌了 → 检查 `@media print` 块是否被改坏）
- 产品卡片左图右内容布局正确（如果变成图占满 → 同上）
- 没有大块空白页

### Step 6 — 报告产物

告诉用户：
- HTML 路径（本地可打开）
- PDF 路径（可外发）
- 总页数
- 用了哪些 section，没用哪些

## 5. 部署选项（用户要发布时）

- **本地预览**：`python3 -m http.server 8765` 在目标目录跑，浏览器开 `http://localhost:8765`
- **Vercel 部署**：目标目录跑 `vercel --prod --yes`，然后 `vercel alias set <deploy-url> <subdomain>.vercel.app`（参考 intco-wallskirt 已有流程）
- **PDF 外发**：直接发 PDF 文件

## 6. 常见坑

- **PDF 底部出现大块空白 / PDF 分成多页没法单页**：**最常见**。Chrome `--window-size` 宽度跟 template JS 里 `const w` 不一致 — DOM 测量按一个宽度量、@page print 按另一个宽度 reflow → 实际比测出的高（多页）或短（底部空白）。**修法**：两边对齐（手机版都 414，桌面版都 1280）。

- **PDF 出现奇怪的灰色背景 / 灰色横条 / 整段灰色**：缺 `-webkit-print-color-adjust: exact; print-color-adjust: exact;` 在 body。Chrome `--print-to-pdf` 默认不打印 background-color（节省墨水的历史习惯），导致 body 白色 / `.section` 白色全变成 user-agent 默认 print 背景（通常是灰色）。**修法**：body CSS 一定要带这两个属性（template.html 已加；自己改 CSS 时别动）。

- **PDF 多页 / 切断**：template.html 末尾的 single-page JS 没执行（被改坏 / virtual-time-budget 太短 / DOM 没有 `<footer class="footer">`）。检查 JS 是否在 `<script>` 标签里，virtual-time-budget ≥ 15000。

- **PDF 卡片变单列、占满整页**（老桌面分页方案的坑，新方案不会有）：仅在你回退到多页桌面方案才遇到 — 检查 `@media print and (min-width:1025px)` 块里 grid 是否都 `!important` 强制多列。

- **PDF 渲染图片不全 / 黑屏**：`--virtual-time-budget` 太短，从 8000 提到 15000-20000 ms。

- **Chrome headless 报 GCM / PHONE_REGISTRATION / DEPRECATED_ENDPOINT error**：忽略，不影响 PDF 输出。

- **字体没加载**：HTML 必须 `<link preconnect>` Google Fonts，离线环境下提前下载 Inter 字体本地化。

- **图片路径相对/绝对混用**：模板用相对路径 `images/scenes/xxx.jpg`，Chrome headless 必须用 `file://<绝对路径>/index.html` 才能解析。

## 7. 参考

- 视觉来源：[intco-wallskirt](https://intco-wallskirt.vercel.app) — 同一套 design tokens
- 已有示例：见 `examples/` 子目录的说明
