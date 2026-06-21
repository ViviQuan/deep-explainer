# deep-explainer — Paper & Code Deep Explainer

> Turn any academic paper or GitHub repository into a beautiful, self-contained
> HTML explainer page. Supports PDF, arxiv URLs, GitHub repos, and combined
> paper+code analysis — with code highlighting, KaTeX formulas, Mermaid diagrams,
> dark mode, and example-driven explanations that make readers feel they
> understand the content better than reading the original.

**Install:**

```bash
npx skills add ViviQuan/deep-explainer
```

Or manually:

```bash
git clone https://github.com/ViviQuan/deep-explainer ~/.claude/skills/deep-explainer
```

**中文文档:** [README.zh-CN.md](README.zh-CN.md)

## Quick Start

```
/deep-explainer https://arxiv.org/abs/2401.xxxxx
/deep-explainer https://github.com/user/repo
/deep-explainer /path/to/paper.pdf
/deep-explainer /path/to/paper.pdf --with-code https://github.com/user/repo
```

The skill auto-detects input type, extracts content via CLI tools (pdftotext,
pymupdf, pdfplumber — cascading fallbacks for any model), performs deep analysis,
and generates a single self-contained HTML file.

## What's in the Box

| Feature | Details |
|---------|---------|
| 📄 **Paper explainer** | PDF, arxiv URL, pasted text → HTML |
| 💻 **Code explainer** | GitHub repo → file-by-file, function-by-function walkthrough |
| 🔗 **Paper + Code** | Cross-references paper formulas ↔ code implementations |
| 🎨 **Modern tech blog style** | Medium/Notion aesthetic, clean typography |
| 📑 **Sidebar TOC** | Auto-generated from headings, scroll-spy highlighting |
| 🌓 **Dark mode** | Toggle button, persisted to localStorage |
| 📱 **Responsive** | Sidebar collapses to floating button on mobile |
| 💻 **Code highlighting** | highlight.js with GitHub theme |
| 📐 **Formula rendering** | KaTeX (inline `$...$` + block `$$...$$`) |
| 📊 **Diagrams** | Mermaid (flowcharts, architecture, sequence, class) |
| 🧮 **Example-driven** | Every method has concrete examples, numerical walkthroughs, analogies |
| 🔧 **Robust PDF reading** | Cascading CLI extraction: pdftotext → pymupdf → pdfplumber → arxiv HTML → native Read → OCR |

## How It Works — 6-Round Workflow

```
Round 1: Identify Input     → detect type (PDF / arxiv / GitHub / text)
Round 2: Deep Reading        → extract full content via CLI tools
Round 3: Structured Analysis → internal: innovations, modules, data flow
Round 4: Ask User            → depth, code details, language
Round 5: Generate HTML       → Write tool, single complete file
Round 6: Self-Review         → quality checklist, fix issues
```

### PDF Extraction — Cascading Strategy

Many models cannot read PDFs natively. The skill uses CLI tools with automatic
fallbacks:

| Priority | Method | When |
|----------|--------|------|
| 1 | `pdftotext` (poppler) | Default — fastest, most reliable |
| 2 | `pymupdf` (fitz) | Complex PDFs, embedded fonts |
| 3 | `pdfplumber` | Best table preservation |
| 4 | arxiv HTML | For arxiv papers — structured, clean |
| 5 | Native Read | Last resort — model-dependent |
| 6 | OCR (tesseract) | Scanned/image PDFs |

Each step includes auto-install (`brew install poppler` / `pip install pymupdf`)
and quality checks (title visible? abstract readable? <20% garbled?).

### Code Repository Handling

```
git clone --depth 1 <url> /tmp/repo
  ↓ fails?
GitHub API: /repos/{owner}/{repo}/git/trees/main?recursive=1
  ↓ rate-limited?
Browse web → read key files individually
```

## Output: Modern Tech Blog HTML

A single self-contained HTML file with:

```
┌──────────┬─────────────────────────────────┐
│          │         ┌─────────────┐         │
│ Sidebar  │  auto   │   Content   │  auto   │
│ (sticky) │  margin │  max 960px  │  margin │
│ TOC nav  │         │  centered   │         │
│          │         └─────────────┘         │
└──────────┴─────────────────────────────────┘
```

- **Header**: fixed top bar with logo + dark mode toggle
- **Sidebar**: auto-generated TOC from h2/h3, scroll-spy active highlighting
- **Content**: centered in available space, 960px max-width
- **Mobile**: sidebar collapses to floating 📑 button

### HTML Template Stack

| Library | Purpose | CDN |
|---------|---------|-----|
| KaTeX | Formula rendering | `cdn.jsdelivr.net/npm/katex@0.16.9` |
| Mermaid | Diagrams | `cdn.jsdelivr.net/npm/mermaid@10` |
| highlight.js | Code highlighting | `cdn.jsdelivr.net/gh/highlightjs/cdn-release@11.9.0` |

No build step. No npm install. Pure static HTML/CSS/JS.

## Example-Driven Explanations

The skill enforces concrete examples at every level:

**For papers** — each innovation must have:
- A **running example** with specific input traced through the method
- A **numerical walkthrough** with actual numbers (not just symbols)
- An **analogy** connecting to real-world concepts
- A **before/after comparison** showing the impact

**For code** — each key function must have:
- **Concrete input** (shape, values, type)
- **Line-by-line execution trace** with intermediate values
- **Output** with interpretation
- **"Why" explanation** — what would happen if written differently

## Project Structure

```
deep-explainer/
├── SKILL.md                     Agent-facing skill definition
├── README.md                    This file
└── references/
    └── html-template.md         Full HTML template (CSS + JS + usage guide)
```

## vs paper-analyzer

| | paper-analyzer | **deep-explainer** |
|---|---|---|
| Input | Paper-first | **Paper OR GitHub repo OR both** |
| Code handling | Searches for related code | **Clones repo, explains file-by-file** |
| Output focus | Paper method analysis | **Code + theory, cross-referenced** |
| HTML style | 3 style choices | **Modern tech blog (Medium/Notion)** |
| PDF reading | Native model read | **CLI-first cascading (model-agnostic)** |
| Examples | Optional | **Mandatory — enforced by skill** |

## Tips

- **For arxiv papers**: the skill fetches both the abstract page and the full
  HTML version. HTML extraction is often cleaner than PDF.
- **For large repos**: the skill uses `git clone --depth 1` and reads entry
  files + core implementations only, not every file.
- **Depth selection**: "Quick overview" (~2000 words) for a summary,
  "Standard" (~5000 words) for detailed explanation, "Deep dive" (~8000+ words)
  for line-by-line code walkthrough.
- **Language**: supports Chinese (with English technical terms) or full English.
  Follows user's language by default.

## License

MIT.
