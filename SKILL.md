---
name: intco-decor-report
version: 2.0.0
description: "把【用户已有的内容】排成 INTCO Decor 品牌视觉的【分页 brochure】（多页 HTML + 多页 PDF）。这是一个【纯排版 / typesetting】skill——只负责把现成的文字、数据、图片排得漂亮、分页、填满、可外发，**绝不生成、调研、编造任何内容/数据/结论**。视觉 = 蓝主色 #1a3d6e + 橙强调 #e85a2e + Inter 字体 + 白卡片 + 大 hero 封面 + 每节一页 + 页脚页码。**触发**：用户说『按 INTCO Decor 风格排版』『把这份内容做成 Decor 报告/brochure/PDF』『美化这份报告的排版』，或在 intco-wallskirt / wallskirt / 海外装饰建材产品目录类项目里要把已有素材排成可视化报告。**不触发**：BASIC Medical（用 basicmedical-website）、英科再生客户情报背调（用 intco-recycling-customer-intelligence）、销售易/报销/名片识别/飞书卡片。"
metadata:
  requires:
    bins: ["python3", "Google Chrome.app"]
  filesNeeded:
    - "template.html (本目录，分页 brochure 模板)"
    - "用户提供的内容（md / 文字 / 数据点 / 现成 PDF）"
    - "用户提供的图片"
---

# intco-decor-report（v2 · 纯排版 skill）

> 🎯 **本 skill 的使命：让任何人都能用好这一个母版（`template.html`），把它的价值发挥到最大。**
> 所有报告都用这同一个母版——UI 锁死、只换内容。你要做的是「挑页 → 填占位符 → 出 PDF → 逐页验证」。
>
> **动手前先读这两份（就在本 skill 内）：**
> 1. 📐 [`references/template-guide.md`](references/template-guide.md) — **母版使用指南**：8 种页型怎么挑、怎么组装、每个 `{{占位符}}` 填什么、填写铁律。
> 2. 🧭 [`references/layout-pitfalls.md`](references/layout-pitfalls.md) — **排版避坑手册**：真实踩过的 7 个坑 + 解法 + 逐页验证清单。
>
> 完整成品对照：[`examples/mdf-acoustic-brochure/`](examples/mdf-acoustic-brochure/)（8 页，含图与 PDF）。

## 0. 使命与边界（最重要，先读）

**这个 skill 只做一件事：把用户已经有的内容，套进唯一母版 `template.html`，排成漂亮的分页 brochure。**

- ✅ 做：版式、分页、填满留白、品牌视觉、把素材塞进模板、把**已有数字**做成图表、导出多页 PDF。
- 🚫 不做：调研、找产品、写文案、补数据、编结论、扩写。

**铁律：内容一字不改、不增、不减（除非用户明确要求改文字）。**
- 用户给什么文字就排什么文字。缺内容就**问用户要**，绝不自己填。
- 图表（增长柱 / 价格带 / 色卡）只能**可视化内容里已经写明的数字/颜色**——原文没有的数字，绝不画进图里。
- 如果用户给的是一份现成 PDF / 旧报告，要"重排版"：先用 `pdftotext -layout <file.pdf>` 把文字**逐字抽出来**当素材，再排——不要凭印象重写。

## 1. 何时使用

### 1.1 触发
- 用户给一份现成内容（市场调研、产品列表、行业 readout、活动总结、季度 update）要"排成专业可外发的报告 / brochure / PDF"
- 用户说"按 INTCO Decor 风格 / 用 Decor 视觉 / 美化这份报告的排版 / 重排这份报告"
- 上下文 = intco-wallskirt / intco-decor / 海外 B2B 装饰建材产品目录 / Amazon 市场情报物料

### 1.2 不触发
- BASIC Medical 网站 → `basicmedical-website`
- 英科再生**客户背调报告** → `intco-recycling-customer-intelligence`
- 飞书卡片回复 → `feishu-card`
- 用户要"另一种品牌风格" / 参考某竞品视觉

## 2. 视觉规范（UI 锁死，全公司统一母版）

**所有报告都用同一套 UI。这个 skill 的 template.html 就是唯一母版——你只换内容，不改样式。**

| Token | 值 | 用途 |
|---|---|---|
| `--primary` | `#1a3d6e` | 大标题、页码、stat 数字、品牌、产品卡左侧色条/市场药丸 |
| `--primary-dk` | `#0f2647` | 封面遮罩、深色 banner、footer |
| `--accent` | `#e85a2e` | eyebrow 栏目标签、rule、价格、rank 徽章、强调 |
| 字体 | Google Fonts `Inter` 400–900 | 全站统一 |
| 纸张 | **Letter 816×1056px @96dpi**（默认） | A4 切换见 §5 |

**绝不改（任何 CSS / 视觉 / 卡片样式 / 颜色 / 字号 / 间距 / 圆角 / block 的长相）。** 母版长什么样，所有报告就长什么样。
**只能改**：内容（文字 / 数字 / 图片）、以及"这份报告用几页、每页放几张卡 / 几张图 / 几行"。
**唯一例外**：某页内容溢出时，**仅为了把内容塞进一页**可微调该页间距（见 §5 修溢出）——这是排版收尾，不是改设计。

## 3. 页面模型 + block 库（template.html 提供）

> 📐 **8 种页型怎么挑、怎么组装、每个 `{{占位符}}` 填什么 → 见 [`references/template-guide.md`](references/template-guide.md)（母版使用指南）。** 下面是速览。

**页面模型**：每个 `<section class="page">` = 一张 A4/Letter 纸。
一张内容页结构 = `.eyebrow`（橙色栏目标签 + 右侧注释）→ `.ptitle` → `.rule` → 内容 block 们 → 可选 `.banner` → `.foot`（栏目名 + 页码）。

**关键排版手法**：
1. `.body` 是 `flex:1`，内容自动撑满页面。
2. 结尾的深蓝 `.banner` 用 `margin-top:auto` **吸到页底**——这是消灭"下半页留白"的核心技巧。
3. `.foot` 走正常流式布局，**永远排在内容之后，物理上不会和内容重叠**。

**block 库**（按内容自由取用、复制、删除）：
1. **cover** — 封面：满幅 hero 图 + 深蓝遮罩 + 大标题 + 4 个 meta chip（必有，1 页）
2. **stat cards** — 4 个大数字看板（可带 sparkline 增长柱）
3. **insight boxes** — 2×2 卡片（纯文字版 / 项目符号版两种）
4. **banner** — 深蓝收尾横幅（吸页底）
5. **image gallery** — 4 图横排场景墙
6. **path / step rows** — 标签 → 文字的递进行
7. **swatches** — 5 个颜色色卡（饰面/配色用）
8. **checklist table** — 三列表格
9. **product cards** — 左图右 8 格规格的产品卡（每页放 2–3 张，多了会溢出）
10. **pattern boxes** — 3 个小卡
11. **growth bar / price band** — 两个纯 SVG 图表
12. **conversation steps** — 带圆形编号的步骤行
13. **methodology** — 双栏 + sources + disclaimer

**版式节奏要求**：相邻两页**不要长得一样**（别连着堆 4 页 2×2 白盒）。封面=大图、摘要=数据看板、市场=图片墙、产品=卡片、方法论=双栏。

## 4. 执行步骤

### Step 1 — 确认输入（缺就问，不要自己编）
- 内容：md / 段落 / 数据点 / 现成 PDF（PDF 先 `pdftotext -layout` 抽文字）
- 图片：hero ×1、场景图、产品图各几张（放进 `images/scenes/` `images/products/`）
- 标题 / 副标题 / 栏目名 / 日期
- 输出位置（默认 `~/Desktop/<报告名>/`）
- **任何一项缺失 → 先问用户，不要瞎填。**

### Step 2 — 目录结构
```
~/Desktop/<报告名>/
├── index.html
├── images/
│   ├── scenes/      # hero + 场景图
│   └── products/    # 产品图
└── <报告名>_<日期>.pdf
```

### Step 3 — 套模板排版
> 📐 占位符填写细节（每个 `{{TOKEN}}` 填什么、页型怎么挑）→ [`references/template-guide.md`](references/template-guide.md)。
1. 复制 `~/.claude/skills/intco-decor-report/template.html` → 目标 `index.html`
2. 按内容**选页、排页**：cover 必有 + 中间按内容选 block 页 + 结尾 methodology（可选）
3. 替换所有 `{{TOKEN}}`；删掉没用到的 block；product card / gallery card / 表格行按需复制
4. `<img src>` 换成真实图片路径（相对路径 `images/...`）
5. 每页填好 `.foot` 的栏目名 + 页码
6. 参考完整范例：`examples/mdf-acoustic-brochure/index.html`（8 页，已验证不溢出）

### Step 4 — 导出多页 PDF（Letter，分页）

```bash
cd <目标目录>
"/Applications/Google Chrome.app/Contents/MacOS/Google Chrome" \
  --headless --disable-gpu \
  --no-pdf-header-footer --print-to-pdf-no-header \
  --virtual-time-budget=15000 \
  --print-to-pdf="<报告名>_<日期>.pdf" \
  "file://<目标目录绝对路径>/index.html"
```

- 本方案是**真分页**：靠 CSS `@page { size:8.5in 11in }` + `.page { height:1056px } + page-break-after` 切页。
- **不需要**旧版那个"单页超长 JS"（v2 已删除单页长滚模式）。
- `--virtual-time-budget=15000`：等 JS/字体/图片加载。
- 去页眉页脚参数别动（否则 Chrome 自动加页码日期）。

### Step 5 — 验证（必做，逐页读 PDF）

⚠️ **本 skill 最大的坑：固定页高 + `overflow:hidden` → 内容超出会被【裁掉】，而且不报错。** 必须逐页检查。
（完整清单 + 排查思路见 [`references/layout-pitfalls.md`](references/layout-pitfalls.md) §4。）

用 Read 工具读 PDF **每一页**，确认：
- [ ] 页数 = 预期（`pdfinfo <pdf> | grep Pages`），无尾随空白页
- [ ] **没有文字被裁**（尤其每页最后一行 / 列表最后一项 / path 最后一行）
- [ ] **深蓝 banner 没有压住页脚**，banner 两行都完整
- [ ] 产品卡的 source 行、表格最后一行完整
- [ ] 封面满幅、图片都加载出来（没黑块）

**发现溢出怎么修**（按这个顺序，都在 template 的 CSS 改）：
1. 把溢出页的 block 内边距/间距调小（`.box` `.pcard .pb` `.tbl td` `.banner` `.crow` `.pat .b` padding；`.boxes/.pcards` gap）
2. 图片比例改矮（gallery `.im` 用 `4/3` 而不是 `3/4`）
3. 还溢出 → 把该页内容拆成两页（产品卡尤其：每页最多 3 张）
4. 改完重新跑 Step 4 → 再读 PDF 复查

### Step 6 — 交付
告诉用户：HTML 路径、PDF 路径、总页数、每页用了哪个 block、内容是否一字未改。

## 5. A4 切换（默认 Letter，按需）
改 template.html 两处：
1. `.page { width:816px; height:1056px }` → `width:794px; height:1123px`
2. `@media print { @page { size:8.5in 11in } }` → `size:A4`

## 6. 常见坑

> 📖 完整版（7 个坑 + 症状 + 解法 + 全局/单页 class 清单 + 收紧/填满旋钮）见 [`references/layout-pitfalls.md`](references/layout-pitfalls.md)。下面是高频几条速查：

- **内容被裁 / banner 压页脚 / 丢了最后一行**：内容超过页高，被 `overflow:hidden` 裁了。**最常见。** 修法见 Step 5。这是固定分页排版的固有风险——**必须逐页验证，不能假设它会自动换页**。
- **徽章/卡片压在图片上有橙/灰色方块**：那是 box-shadow 在照片上糊成的块。压图的元素别用 box-shadow，靠边框+色条+底色面板做卡感。
- **内容"1.x 页"挤或空**：≈1 页就精确收进一页（收紧+横幅 margin-top:auto 吸底）；真 2 页量再拆。别硬拆 1.1 页。
- **页面太挤**：先砍重复的图（如价格带图和"价格区间"文字盒重复）→ 删图不删数据。
- **弱盒子飘**：给小标题 + 套已有视觉语言（橙顶边，同数据卡）。
- **PDF 出现灰底 / 灰条**：body 缺 `-webkit-print-color-adjust:exact; print-color-adjust:exact;`（template 已加，别删）。Chrome print 默认不打印背景色。
- **图片不全 / 黑屏**：`--virtual-time-budget` 太短，提到 15000–20000。
- **图片 404**：必须用 `file://<绝对路径>/index.html` 打开，图片用相对路径 `images/...`。
- **字体没加载**：保留 `<link>` Google Fonts；离线环境提前本地化 Inter。
- **Chrome headless 报 GCM / PHONE_REGISTRATION error**：忽略，不影响 PDF。

## 7. 参考
- 完整范例（8 页，含图，已验证不溢出）：`examples/mdf-acoustic-brochure/`
- 视觉来源：[intco-wallskirt](https://intco-wallskirt.vercel.app)
- v1 旧模板（单页长滚 + 内容混排）已备份为 `template.html.bak-20260626` / `SKILL.md.bak-20260626`
