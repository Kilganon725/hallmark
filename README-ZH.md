# Hallmark 中文快速指南

> 原项目：https://github.com/Nutlope/hallmark
> 本 fork：Kilganon725/hallmark（公开 fork，默认分支 `main` 不变）
> 原项目作者：Nutlope；许可证：MIT（见 fork 中的 `LICENSE`）。
> 本文件为中文入门指南，内容译自并浓缩自原项目 README；如有出入，以原项目 README 为准。

## Hallmark 是什么

Hallmark 是一个为编程助手准备的设计技能（skill），支持 Claude Code、
Codex、Cursor 和 OpenCode。它的目标很直接：让助手做出来的页面不再一眼
就是“AI 生成的”。

它的做法是：

- 提供 48 个主题（theme），每个主题是一种“材料”（纸张、字体、颜色、
  表面、组件与质感），而不是一套固定版式；
- 设定一条不可逾越的质量底线（Floor），并用检查工具在真实浏览器里
  验页面；
- 按页面要完成的“任务”来布局：预约页就让人预约，商店页就让人购买，
  订阅页就完成订阅，页面以它的核心动作为结尾。

## 安装

```bash
npx skills add nutlope/hallmark -g
```

- 需要 Node 18 或更高版本。
- `-g` 表示为所有项目安装；去掉 `-g` 则只装到当前项目。
- 浏览器检查（`sloplint --render`、截图）还需要全局安装
  `puppeteer-core` 并装有 Chrome；没有它们时，静态检查仍可运行。

| 工具 | 安装位置 | 调用方式 |
| --- | --- | --- |
| Claude Code | `~/.claude/skills/hallmark` | `/hallmark <提示词>`，或直接描述一次新构建 |
| Codex CLI | `~/.agents/skills/hallmark` | `$hallmark <提示词>`，或直接描述一次新构建 |
| Cursor | 使用上面已安装的副本 | `/hallmark <提示词>`，或直接描述一次新构建 |
| OpenCode | 使用上面已安装的副本 | `/hallmark <提示词>`，或直接描述一次新构建 |

只装给某一个工具，例如：

```bash
npx skills add nutlope/hallmark -g --agent claude-code
```

从克隆安装时，`node skills/hallmark/scripts/install.mjs` 会先校验全部
主题与参考文件再复制；`install-hook.mjs` 可为 Claude Code 加上编辑时的
Floor 检查。

## 一次构建是怎么跑的

1. **预检。** 先读项目（字体、配色、框架、已锁定的 `design.md`），
   并只问一次它需要的真实素材：照片、截图、真实数字，不编造。
2. **发主题、画结构。** `scripts/theme.mjs deal` 会按提示词给出三个
   合适的主题；`draw` 按页面任务画出页面结构。
3. **先做 Hero，再冷评审。** Hero 单独先做、渲染并评审，之后才做
   页面其余部分。
4. **真实浏览器检查。** `scripts/sloplint.mjs --render` 在 8 个宽度下
   检查页面，再由一个全新上下文的子代理复审。成品是自包含的
   HTML + CSS，首个 CSS 注释里盖着它做过的决策。

质量门分三档：

- **Floor（底线，绝不能上线）：** 对比度、焦点、减动效、编造事实、
  手机端布局坏掉。
- **Reflex（模型惯性）：** 模型容易默认写成的样子；有理由时可在
  记录中推翻。
- **Finish（打磨细节）：** 检查，但从不阻拦上线。

## 改版与审计

```bash
/hallmark redesign ./src/app
/hallmark audit ./index.html
```

- **redesign：** 重建已有页面，保留内容与接线；`sloplint --before`
  先记录全部链接、字段、处理函数与标题，`sloplint --kept` 会在丢
  了其中任何一项时让改版失败；交付物是改版前后的并排对照。
- **audit：** 只出报告，不改页面。
- 说 **custom** 则不用主题：没有版式、组件与配色方案，只有底线、
  质量门与一段简短指引，去找一个只有这个客户才有的想法并让它
  主导页面。

只检查任意页面：

```bash
node skills/hallmark/scripts/sloplint.mjs <文件或目录> --render
```

## 许可证说明

- 项目本身：MIT，可自由使用、fork 与发布。
- 照片库：`skills/hallmark/scripts/photos.json` 列出的 755 张照片来自
  Wikimedia Commons，每张照片保留自己的许可证（CC0、公有领域、
  CC BY 或 CC BY-SA），使用时页面上会注明来源；它不使用 Unsplash、
  Pexels、Pixabay 等图库。

## 本 fork 的改动

本 fork 在一次提交中新增两个文档文件，不改技能行为：

- `UNDERSTANDING.md`：对原项目的研究笔记（它是干什么的、为什么火、
  核心实现、技术栈、适用场景与局限）。
- `README-ZH.md`（本文件）：中文快速指南。

原作者与 MIT 许可证均予保留。
