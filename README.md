# INTCO Decor — Report Skill

INTCO Decor 品牌视觉的报告模板 + Skill。一套设计 token，套出与 [intco-wallskirt.vercel.app](https://intco-wallskirt.vercel.app) 同款的网页/PDF。

## 怎么用

### 方式 A：对 Claude Code 说话（推荐）

> "按 INTCO Decor 风格生成一份 XXX 报告"

Claude 会自动识别（SKILL.md 触发规则），向你确认输入（文字内容 + 图片 + 报告主题），然后：
1. 套 `template.html`
2. 替换 `{{PLACEHOLDER}}`
3. 用 headless Chrome 生成 PDF

### 方式 B：你自己复制模板手改

```bash
mkdir ~/Desktop/my-new-report
cp ~/.claude/skills/intco-decor-report/template.html ~/Desktop/my-new-report/index.html
# 然后在编辑器里搜 {{ 把所有 placeholder 改成实际内容
```

生成 PDF：
```bash
"/Applications/Google Chrome.app/Contents/MacOS/Google Chrome" \
  --headless --disable-gpu \
  --no-pdf-header-footer --print-to-pdf-no-header \
  --virtual-time-budget=10000 \
  --print-to-pdf=$HOME/Desktop/my-new-report/report.pdf \
  "file://$HOME/Desktop/my-new-report/index.html"
```

## 目录结构

```
intco-decor-report/
├── SKILL.md       # Claude Code 触发规则 + 执行步骤（机器读）
├── README.md      # 本文件（人读）
├── template.html  # 单文件 HTML 模板（CSS 嵌入 + 全部 section 示例 + placeholder）
└── examples/      # 已生成的报告示例（参考）
    └── README.md
```

## Sections 一览（template.html 提供的 building blocks）

| Section | 适合场景 | 必有 |
|---|---|---|
| top-strip + header | 顶部信息 + Logo | ✓ |
| hero | 大图 + 标题 + lead 段 | ✓ |
| signal cards (3x) | 大数字看板（销量/排名/转化率等） | 看需要 |
| takeaways (3x) | 结论性要点 | 看需要 |
| scenes gallery (2x2) | 场景/用例图集 | 看需要 |
| product cards (Nx 垂直) | Top N 产品对比 | 看需要 |
| methodology (2 列) | Scope / 数据口径 | 看需要 |
| footer | 品牌 + meta | ✓ |

不需要的 section 直接整块删除。

## 改 placeholder 的快速方法

```bash
# 搜还有哪些 placeholder 没填
grep -o '{{[A-Z_0-9]*}}' index.html | sort -u

# 批量替换某个变量
sed -i '' 's/{{REPORT_DATE}}/2026-06-25/g' index.html
```

## 视觉规范（**不可动**）

- 主色 `#1a3d6e`（深蓝） / 强调 `#e85a2e`（橙）
- 字体 Inter 400-800
- 内容居中 max-width `1280px`
- 圆角 卡片 8px / 按钮 4px

详见 SKILL.md 第 2 节。

## 已有示例

- `examples/mdf-acoustic-report/` — Amazon NA MDF Acoustic Wood Slat Panels Top 5 Market Readout

## 想加新 section 怎么办

1. 在 `~/Desktop/<某个报告>` 里手写实验新 section（保持 token 一致）
2. 调好之后把 HTML 块 + 对应 CSS 块 append 到 `template.html`
3. 在 SKILL.md 第 3 节"标准 section 库"加一条
4. 在 README.md 这张表加一条
