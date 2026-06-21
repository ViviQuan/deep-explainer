# deep-explainer — 论文/代码深度解读

> 将学术论文或 GitHub 代码仓库转化为精美的自包含 HTML 解读页面。
> 支持 PDF、arxiv 链接、GitHub 仓库、论文+代码联合分析。
> 代码高亮、KaTeX 公式、Mermaid 图表、暗色模式、举例驱动的解释——
> 让读者觉得"比我自己读还清楚"。

**安装：**

```bash
claude skill add deep-explainer
```

或直接克隆到 `~/.claude/skills/deep-explainer/`。

**English:** [README.md](README.md)

## 快速使用

```
/explain https://arxiv.org/abs/2401.xxxxx
/explain https://github.com/user/repo
/explain /path/to/paper.pdf
/explain /path/to/paper.pdf --with-code https://github.com/user/repo
```

自动检测输入类型，通过 CLI 工具提取内容（pdftotext / pymupdf / pdfplumber 级联回退，兼容任意模型），深度分析后生成一个自包含 HTML 文件。

## 功能一览

| 功能 | 说明 |
|------|------|
| 📄 **论文解读** | PDF、arxiv URL、粘贴文本 → HTML |
| 💻 **代码解读** | GitHub 仓库 → 逐文件、逐函数讲解 |
| 🔗 **论文+代码** | 论文公式 ↔ 代码实现 交叉对照 |
| 🎨 **现代技术博客风** | Medium/Notion 排版美学 |
| 📑 **左侧目录导航** | 从 h2/h3 自动生成，滚动高亮 |
| 🌓 **暗色模式** | 右上角切换，状态持久化 |
| 📱 **响应式** | 移动端 sidebar 折叠为浮动按钮 |
| 💻 **代码高亮** | highlight.js，GitHub 主题 |
| 📐 **公式渲染** | KaTeX，行内 `$...$` + 块级 `$$...$$` |
| 📊 **图表** | Mermaid 流程图、架构图、时序图、类图 |
| 🧮 **举例驱动** | 每个方法都有具体例子、数值推演、类比 |
| 🔧 **健壮的 PDF 读取** | 级联 CLI 提取：pdftotext → pymupdf → pdfplumber → arxiv HTML → 原生 Read → OCR |

## 工作流程 — 6 轮强制执行

```
Round 1: 识别输入       → 检测类型（PDF / arxiv / GitHub / 文本）
Round 2: 深度阅读       → 通过 CLI 工具提取完整内容
Round 3: 结构化分析     → 内部完成：创新点、模块、数据流
Round 4: 询问用户       → 深度、代码细节、语言
Round 5: 生成 HTML      → Write 工具，单个完整文件
Round 6: 自我审查       → 质量检查清单，修复问题
```

### PDF 提取 — 级联策略

很多模型无法原生读取 PDF。本 skill 使用 CLI 工具，自动级联回退：

| 优先级 | 方法 | 适用场景 |
|--------|------|---------|
| 1 | `pdftotext` (poppler) | 默认首选——最快、最可靠 |
| 2 | `pymupdf` (fitz) | 复杂 PDF、嵌入字体 |
| 3 | `pdfplumber` | 表格保留最好 |
| 4 | arxiv HTML | arxiv 论文——结构化、干净 |
| 5 | 原生 Read | 最后手段——取决于模型 |
| 6 | OCR (tesseract) | 扫描件 / 图片 PDF |

每一步都包含自动安装（`brew install poppler` / `pip install pymupdf`）和质量检查（标题可见？摘要可读？乱码 <20%？）。

### 代码仓库处理

```
git clone --depth 1 <url> /tmp/repo
  ↓ 失败？
GitHub API: /repos/{owner}/{repo}/git/trees/main?recursive=1
  ↓ 限流？
浏览器访问 → 逐个读取关键文件
```

## 输出效果：现代技术博客 HTML

单个自包含 HTML 文件：

```
┌──────────┬─────────────────────────────────┐
│          │         ┌─────────────┐         │
│ 侧边栏   │  自动   │   正文内容   │  自动   │
│ (sticky) │  边距   │  最大 960px  │  边距   │
│ 目录导航  │         │   居中显示   │         │
│          │         └─────────────┘         │
└──────────┴─────────────────────────────────┘
```

- **顶栏**：固定顶部，logo + 暗色模式切换
- **侧边栏**：从 h2/h3 自动生成目录，滚动高亮当前章节
- **正文**：在可用空间内居中，最大宽度 960px
- **移动端**：侧边栏折叠为浮动 📑 按钮

### HTML 技术栈

| 库 | 用途 | CDN |
|----|------|-----|
| KaTeX | 公式渲染 | `cdn.jsdelivr.net/npm/katex@0.16.9` |
| Mermaid | 图表 | `cdn.jsdelivr.net/npm/mermaid@10` |
| highlight.js | 代码高亮 | `cdn.jsdelivr.net/gh/highlightjs/cdn-release@11.9.0` |

无需构建，无需 npm install，纯静态 HTML/CSS/JS。

## 举例驱动的解释

本 skill 在每个层面都强制要求具体例子：

**论文解读** — 每个创新点必须有：
- **具体例子**：拿一个实际输入，走完整个方法流程
- **数值推演**：展示实际数字，不只是数学符号
- **类比**：连接现实世界的概念
- **前后对比**：有这个创新 vs 没有，效果差异

**代码解读** — 每个关键函数必须有：
- **具体输入**：shape、值、类型
- **逐行执行追踪**：中间值变化
- **输出结果**：附带解读
- **"为什么"解释**：如果写法不同会怎样

## 项目结构

```
deep-explainer/
├── SKILL.md                     技能定义（面向 Agent）
├── README.md                    英文文档
├── README.zh-CN.md              中文文档（本文件）
└── references/
    └── html-template.md         完整 HTML 模板（CSS + JS + 使用说明）
```

## 与 paper-analyzer 的区别

| | paper-analyzer | **deep-explainer** |
|---|---|---|
| 输入 | 以论文为主 | **论文 OR GitHub 仓库 OR 两者结合** |
| 代码处理 | 搜索关联代码 | **克隆仓库，逐文件解读** |
| 输出重心 | 论文方法分析 | **代码 + 理论，交叉对照** |
| HTML 风格 | 3 种风格可选 | **现代技术博客风（Medium/Notion）** |
| PDF 读取 | 模型原生读取 | **CLI 优先级联（模型无关）** |
| 举例 | 可选 | **强制——由 skill 规则保证** |

## 使用建议

- **arxiv 论文**：会同时获取摘要页和全文 HTML 版本。HTML 提取通常比 PDF 更干净。
- **大型仓库**：使用 `git clone --depth 1`，只读入口文件和核心实现，不读每个文件。
- **深度选择**："快速概览"（~2000 字）看要点，"标准解读"（~5000 字）详细解释，"深度长文"（~8000+ 字）逐行代码解读。
- **语言**：支持中文（技术术语保留英文）或全英文，默认跟随用户语言。

## 许可证

MIT.
