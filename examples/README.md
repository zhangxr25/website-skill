# Examples

实际跑出来的报告，给以后排版时参考布局和数据组织方式。

## mdf-acoustic-brochure（2026-06-26）· ⭐ 当前标准范例（v2 分页 brochure）

- **主题**：Amazon NA · MDF Acoustic Wood Slat Wall Panels · Market Brochure
- **文件**：`examples/mdf-acoustic-brochure/index.html` + `images/` + `example.pdf`
- **页数**：9 页（Letter 竖版，真分页，每页带页脚页码），已逐页验证**不溢出、无空白页、无内容被裁**
- **每页用的 block**：
  1. cover（满幅 hero + 4 meta chip）
  2. stat cards ×4（400+/mo · #5 · $199 · 5.0%，最后一个带增长柱 sparkline）+ 2×2 boxes + banner
  3. 2×2 boxes + image gallery ×4（场景墙）+ path rows ×3
  4. bullet boxes ×4 + 色卡 ×5（饰面）+ checklist table + banner
  5. product cards ×2（#1 NeatiEase / #2 TONOR）——卡较高，每页 2 张
  6. product cards ×2（#3 Art3d / #4 RITOLLO）
  7. product card ×1（#5 VEVOR）+ pattern boxes ×3（橙顶边）+ banner
  8. bullet boxes ×4 + 编号 steps ×4 + banner
  9. methodology 双栏 + sources ×7 + disclaimer
- **产品卡样式**：大图（230px）左 + 标题 + 品牌/市场行 + 细分隔线 + 开放式 4×2 规格 + 蓝竖条说明框 + 斜体 Evidence + 链接按钮（Amazon US 蓝 / CA 灰）。卡较高，**每页放 2 张**（详见 [`../references/template-guide.md`](../references/template-guide.md)）。
- **特点**：几乎用到了所有 block 类型；版式节奏 = 封面大图 / 数据看板 / 图片墙 / 卡片 / 双栏；图表（增长柱 / 色卡）只可视化原文已有数字。

> 这份就是 template.html 把所有 `{{TOKEN}}` 填上 MDF 内容后的样子——做新报告时可直接对照它怎么排。

---

## （历史）mdf-acoustic-report（2026-06-25）· v1 单页长滚版，已弃用

v1 是"整份 = 一张超长 PDF"的单页长滚模式 + 内容混排，已在 v2 改造中废弃。
旧模板备份在上级目录 `template.html.bak-20260626` / `SKILL.md.bak-20260626`。
