# HTML Template — Modern Tech Blog Style

## Design Philosophy

Inspired by Medium, Notion, and Stripe Docs:
- **Clean**: generous whitespace, never cluttered
- **Readable**: comfortable line-height, font-size, paragraph spacing
- **Professional**: code highlighting, formula rendering, diagram support
- **Modern**: dark mode, responsive layout, smooth animations

---

## Full HTML Template

Use this template as the base when generating HTML. All CSS/JS is inline or via CDN.

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>📄 Title — Deep Explainer</title>

<!-- KaTeX Formula Rendering -->
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/katex@0.16.9/dist/katex.min.css">
<script defer src="https://cdn.jsdelivr.net/npm/katex@0.16.9/dist/katex.min.js"></script>
<script defer src="https://cdn.jsdelivr.net/npm/katex@0.16.9/dist/contrib/auto-render.min.js"
  onload="renderMathInElement(document.body,{delimiters:[{left:'$$',right:'$$',display:true},{left:'$',right:'$',display:false}]})"></script>

<!-- Mermaid Diagrams -->
<script src="https://cdn.jsdelivr.net/npm/mermaid@10/dist/mermaid.min.js"></script>

<!-- highlight.js Code Highlighting -->
<link rel="stylesheet" href="https://cdn.jsdelivr.net/gh/highlightjs/cdn-release@11.9.0/build/styles/github.min.css" id="hljs-light">
<link rel="stylesheet" href="https://cdn.jsdelivr.net/gh/highlightjs/cdn-release@11.9.0/build/styles/github-dark.min.css" id="hljs-dark" disabled>
<script src="https://cdn.jsdelivr.net/gh/highlightjs/cdn-release@11.9.0/build/highlight.min.js"></script>

<style>
/* ===== CSS Variables ===== */
:root {
  --bg: #ffffff;
  --bg-secondary: #f8f9fa;
  --bg-tertiary: #f1f3f5;
  --text: #1a1a2e;
  --text-secondary: #4a4a68;
  --text-muted: #8b8ba8;
  --accent: #2563eb;
  --accent-light: #dbeafe;
  --accent-dark: #1d4ed8;
  --border: #e5e7eb;
  --border-light: #f0f0f5;
  --code-bg: #f6f8fa;
  --code-text: #24292e;
  --shadow: 0 1px 3px rgba(0,0,0,0.06), 0 1px 2px rgba(0,0,0,0.04);
  --shadow-lg: 0 10px 25px rgba(0,0,0,0.08);
  --radius: 8px;
  --radius-lg: 12px;
  --sidebar-width: 260px;
  --content-width: 960px;
  --header-height: 60px;
  --font-sans: -apple-system, BlinkMacSystemFont, "Segoe UI", "Noto Sans SC", "PingFang SC", sans-serif;
  --font-serif: "Noto Serif SC", "Source Han Serif SC", Georgia, serif;
  --font-mono: "JetBrains Mono", "Fira Code", "SF Mono", Consolas, monospace;
}

[data-theme="dark"] {
  --bg: #0d1117;
  --bg-secondary: #161b22;
  --bg-tertiary: #21262d;
  --text: #e6edf3;
  --text-secondary: #b1bac4;
  --text-muted: #768390;
  --accent: #58a6ff;
  --accent-light: #1c3a5e;
  --accent-dark: #79c0ff;
  --border: #30363d;
  --border-light: #21262d;
  --code-bg: #161b22;
  --code-text: #e6edf3;
  --shadow: 0 1px 3px rgba(0,0,0,0.3);
  --shadow-lg: 0 10px 25px rgba(0,0,0,0.4);
}

/* ===== Reset & Base ===== */
*, *::before, *::after { margin: 0; padding: 0; box-sizing: border-box; }
html { scroll-behavior: smooth; scroll-padding-top: calc(var(--header-height) + 20px); }
body {
  font-family: var(--font-sans);
  color: var(--text);
  background: var(--bg);
  line-height: 1.8;
  font-size: 16px;
  transition: background 0.3s, color 0.3s;
}

/* ===== Header ===== */
.site-header {
  position: fixed; top: 0; left: 0; right: 0;
  height: var(--header-height);
  background: var(--bg);
  border-bottom: 1px solid var(--border);
  display: flex; align-items: center; justify-content: space-between;
  padding: 0 24px;
  z-index: 100;
  backdrop-filter: blur(10px);
  background: rgba(255,255,255,0.85);
}
[data-theme="dark"] .site-header { background: rgba(13,17,23,0.85); }
.site-header .logo { font-weight: 700; font-size: 1.1rem; color: var(--accent); }
.site-header .meta { font-size: 0.85rem; color: var(--text-muted); }
.header-actions { display: flex; gap: 8px; align-items: center; }
.theme-toggle {
  background: var(--bg-tertiary); border: 1px solid var(--border);
  border-radius: 6px; padding: 6px 10px; cursor: pointer;
  font-size: 1rem; color: var(--text); transition: all 0.2s;
}
.theme-toggle:hover { background: var(--accent-light); }

/* ===== Sidebar ===== */
.sidebar {
  position: sticky; top: var(--header-height);
  width: var(--sidebar-width); min-width: var(--sidebar-width);
  height: calc(100vh - var(--header-height));
  overflow-y: auto; padding: 20px 16px;
  border-right: 1px solid var(--border);
  background: var(--bg-secondary);
  z-index: 50;
  flex-shrink: 0;
}
.sidebar::-webkit-scrollbar { width: 4px; }
.sidebar::-webkit-scrollbar-thumb { background: var(--border); border-radius: 2px; }
.toc-title {
  font-size: 0.75rem; font-weight: 600; text-transform: uppercase;
  letter-spacing: 0.08em; color: var(--text-muted); margin-bottom: 12px;
}
.toc-list { list-style: none; }
.toc-list li { margin-bottom: 2px; }
.toc-list a {
  display: block; padding: 5px 10px; border-radius: 6px;
  font-size: 0.85rem; color: var(--text-secondary);
  text-decoration: none; transition: all 0.15s;
  border-left: 2px solid transparent;
}
.toc-list a:hover { color: var(--accent); background: var(--accent-light); }
.toc-list a.active {
  color: var(--accent); font-weight: 600;
  border-left-color: var(--accent); background: var(--accent-light);
}
.toc-list .toc-h3 { padding-left: 24px; font-size: 0.8rem; }

/* ===== Page Layout ===== */
.page-wrapper {
  display: flex;
  margin-top: var(--header-height);
  min-height: calc(100vh - var(--header-height));
}

/* ===== Main Content ===== */
.main-content {
  flex: 1;
  min-width: 0;
  max-width: var(--content-width);
  padding: 40px 48px 80px;
  margin: 0 auto;
}

/* ===== Typography ===== */
h1 {
  font-size: 2.2rem; font-weight: 800; line-height: 1.25;
  margin-bottom: 12px; letter-spacing: -0.02em;
}
h2 {
  font-size: 1.5rem; font-weight: 700; line-height: 1.3;
  margin-top: 48px; margin-bottom: 16px; padding-bottom: 8px;
  border-bottom: 2px solid var(--border);
  letter-spacing: -0.01em;
}
h3 {
  font-size: 1.2rem; font-weight: 600; line-height: 1.4;
  margin-top: 32px; margin-bottom: 12px;
}
h4 {
  font-size: 1.05rem; font-weight: 600;
  margin-top: 24px; margin-bottom: 8px;
}
p { margin-bottom: 16px; }
a { color: var(--accent); text-decoration: none; }
a:hover { text-decoration: underline; }
strong { font-weight: 600; color: var(--text); }
em { font-style: italic; }
hr { border: none; border-top: 1px solid var(--border); margin: 32px 0; }

/* ===== Meta Info ===== */
.paper-meta {
  background: var(--bg-secondary); border: 1px solid var(--border);
  border-radius: var(--radius-lg); padding: 20px 24px;
  margin-bottom: 32px; font-size: 0.9rem; line-height: 2;
}
.paper-meta .label { color: var(--text-muted); font-weight: 500; min-width: 60px; display: inline-block; }
.paper-meta a { color: var(--accent); }

/* ===== Summary Box ===== */
.summary-box {
  background: linear-gradient(135deg, var(--accent-light), rgba(99,102,241,0.08));
  border: 1px solid var(--accent);
  border-radius: var(--radius-lg); padding: 24px 28px;
  margin: 24px 0; position: relative;
}
.summary-box::before {
  content: "💡"; position: absolute; top: -12px; left: 20px;
  font-size: 1.2rem; background: var(--bg); padding: 0 8px;
}
.summary-box h3 { margin-top: 0; color: var(--accent); font-size: 1rem; }
.summary-box p:last-child { margin-bottom: 0; }

/* ===== Code Blocks ===== */
pre {
  background: var(--code-bg); color: var(--code-text);
  border: 1px solid var(--border); border-radius: var(--radius);
  padding: 16px 20px; margin: 16px 0;
  overflow-x: auto; font-size: 0.88rem; line-height: 1.6;
  font-family: var(--font-mono);
}
code {
  font-family: var(--font-mono); font-size: 0.9em;
}
p code, li code, td code {
  background: var(--code-bg); color: var(--accent-dark);
  padding: 2px 6px; border-radius: 4px;
  font-size: 0.88em; border: 1px solid var(--border-light);
}
[data-theme="dark"] p code { color: var(--accent); }
.code-caption {
  font-size: 0.8rem; color: var(--text-muted);
  margin-bottom: -8px; padding: 8px 12px;
  background: var(--bg-tertiary); border-radius: var(--radius) var(--radius) 0 0;
  border: 1px solid var(--border); border-bottom: none;
  font-family: var(--font-mono);
}
.code-caption + pre { border-top-left-radius: 0; border-top-right-radius: 0; margin-top: 0; }

/* ===== Tables ===== */
table {
  width: 100%; border-collapse: collapse; margin: 16px 0;
  font-size: 0.92rem;
}
th, td {
  border: 1px solid var(--border); padding: 10px 14px; text-align: left;
}
th { background: var(--bg-secondary); font-weight: 600; font-size: 0.85rem; }
tr:hover { background: var(--bg-secondary); }

/* ===== Blockquotes ===== */
blockquote {
  border-left: 3px solid var(--accent); margin: 16px 0;
  padding: 12px 20px; background: var(--bg-secondary);
  border-radius: 0 var(--radius) var(--radius) 0;
  color: var(--text-secondary); font-style: italic;
}
blockquote p:last-child { margin-bottom: 0; }

/* ===== Lists ===== */
ul, ol { margin: 12px 0; padding-left: 24px; }
li { margin-bottom: 6px; }
li > ul, li > ol { margin-top: 6px; margin-bottom: 6px; }

/* ===== Info Cards ===== */
.info-card {
  background: var(--bg-secondary); border: 1px solid var(--border);
  border-radius: var(--radius-lg); padding: 20px 24px; margin: 16px 0;
}
.info-card h4 { margin-top: 0; color: var(--accent); }
.info-card ul { margin-bottom: 0; }

/* ===== Callout Boxes ===== */
.callout {
  border-radius: var(--radius); padding: 14px 18px;
  margin: 16px 0; font-size: 0.92rem;
  display: flex; gap: 10px; align-items: flex-start;
}
.callout .icon { font-size: 1.1rem; flex-shrink: 0; margin-top: 2px; }
.callout.info { background: #dbeafe; border: 1px solid #93c5fd; }
.callout.warning { background: #fef3c7; border: 1px solid #fcd34d; }
.callout.success { background: #d1fae5; border: 1px solid #6ee7b7; }
.callout.error { background: #fee2e2; border: 1px solid #fca5a5; }
[data-theme="dark"] .callout.info { background: #1e3a5f; border-color: #2563eb; }
[data-theme="dark"] .callout.warning { background: #422006; border-color: #d97706; }
[data-theme="dark"] .callout.success { background: #064e3b; border-color: #10b981; }
[data-theme="dark"] .callout.error { background: #450a0a; border-color: #ef4444; }

/* ===== Mermaid ===== */
.mermaid {
  background: var(--bg-secondary); border: 1px solid var(--border);
  border-radius: var(--radius); padding: 20px; margin: 16px 0;
  text-align: center; overflow-x: auto;
}

/* ===== Animations ===== */
@keyframes fadeInUp {
  from { opacity: 0; transform: translateY(10px); }
  to { opacity: 1; transform: translateY(0); }
}
.main-content > * { animation: fadeInUp 0.4s ease-out; }

/* ===== Mobile Responsive ===== */
.mobile-toc-toggle {
  display: none; position: fixed; bottom: 20px; right: 20px;
  width: 48px; height: 48px; border-radius: 50%;
  background: var(--accent); color: white; border: none;
  font-size: 1.3rem; cursor: pointer; z-index: 200;
  box-shadow: var(--shadow-lg); transition: transform 0.2s;
}
.mobile-toc-toggle:hover { transform: scale(1.1); }

@media (max-width: 1024px) {
  .page-wrapper { display: block; }
  .sidebar {
    position: fixed; top: var(--header-height); left: 0;
    transform: translateX(-100%); transition: transform 0.3s;
    width: 280px; z-index: 150; height: calc(100vh - var(--header-height));
  }
  .sidebar.open { transform: translateX(0); }
  .main-content { max-width: 100%; padding: 24px 20px 60px; margin: 0; }
  .mobile-toc-toggle { display: flex; align-items: center; justify-content: center; }
  h1 { font-size: 1.7rem; }
  h2 { font-size: 1.3rem; }
}

@media (max-width: 640px) {
  body { font-size: 15px; }
  .main-content { padding: 16px 16px 60px; }
  pre { font-size: 0.82rem; padding: 12px 14px; }
  table { font-size: 0.85rem; }
  th, td { padding: 8px 10px; }
}
</style>
</head>

<body>

<!-- Header -->
<header class="site-header">
  <div>
    <span class="logo">📖 Deep Explainer</span>
    <span class="meta"> · Paper/Code Deep Explainer</span>
  </div>
  <div class="header-actions">
    <button class="theme-toggle" onclick="toggleTheme()" title="Toggle dark mode">🌓</button>
  </div>
</header>

<!-- Page Layout Wrapper -->
<div class="page-wrapper">

<!-- Sidebar / TOC -->
<nav class="sidebar" id="sidebar">
  <div class="toc-title">Contents</div>
  <ul class="toc-list" id="toc-list">
    <!-- Auto-generated from h2/h3 by JS -->
  </ul>
</nav>

<!-- Mobile TOC Toggle -->
<button class="mobile-toc-toggle" id="toc-toggle" onclick="toggleSidebar()">📑</button>

<!-- Main Content -->
<article class="main-content" id="content">

  <!-- ===== Fill content here ===== -->

</article>

</div><!-- end .page-wrapper -->

<!-- Scripts -->
<script>
// ===== Theme Toggle =====
function toggleTheme() {
  const html = document.documentElement;
  const isDark = html.getAttribute('data-theme') === 'dark';
  html.setAttribute('data-theme', isDark ? '' : 'dark');
  document.getElementById('hljs-light').disabled = !isDark;
  document.getElementById('hljs-dark').disabled = isDark;
  localStorage.setItem('theme', isDark ? 'light' : 'dark');
  mermaid.initialize({
    theme: isDark ? 'dark' : 'default',
    securityLevel: 'loose'
  });
}
// Load saved theme
if (localStorage.getItem('theme') === 'dark') {
  document.documentElement.setAttribute('data-theme', 'dark');
  document.getElementById('hljs-light').disabled = true;
  document.getElementById('hljs-dark').disabled = false;
}

// ===== Auto-generate TOC =====
function generateTOC() {
  const content = document.getElementById('content');
  const headings = content.querySelectorAll('h2, h3');
  const tocList = document.getElementById('toc-list');
  tocList.innerHTML = '';
  headings.forEach((h, i) => {
    if (!h.id) h.id = 'section-' + i;
    const li = document.createElement('li');
    const a = document.createElement('a');
    a.href = '#' + h.id;
    a.textContent = h.textContent;
    if (h.tagName === 'H3') a.className = 'toc-h3';
    li.appendChild(a);
    tocList.appendChild(li);
  });
}

// ===== Scroll Spy =====
function initScrollSpy() {
  const links = document.querySelectorAll('.toc-list a');
  const headings = [];
  links.forEach(a => {
    const id = a.getAttribute('href').slice(1);
    const el = document.getElementById(id);
    if (el) headings.push({ el, a });
  });

  function update() {
    let current = headings[0];
    for (const h of headings) {
      if (h.el.getBoundingClientRect().top <= 100) current = h;
    }
    links.forEach(a => a.classList.remove('active'));
    if (current) current.a.classList.add('active');
  }
  window.addEventListener('scroll', update, { passive: true });
  update();
}

// ===== Mobile Sidebar Toggle =====
function toggleSidebar() {
  document.getElementById('sidebar').classList.toggle('open');
}
document.addEventListener('click', (e) => {
  const sidebar = document.getElementById('sidebar');
  const toggle = document.getElementById('toc-toggle');
  if (sidebar.classList.contains('open') && !sidebar.contains(e.target) && e.target !== toggle) {
    sidebar.classList.remove('open');
  }
});

// ===== Init =====
document.addEventListener('DOMContentLoaded', () => {
  generateTOC();
  initScrollSpy();
  hljs.highlightAll();
  mermaid.initialize({
    theme: localStorage.getItem('theme') === 'dark' ? 'dark' : 'default',
    securityLevel: 'loose',
    startOnLoad: true
  });
});
</script>

</body>
</html>
```

---

## Usage Notes

### Content Insertion Point

Insert explainer content at `<!-- ===== Fill content here ===== -->`. Content structure:

```html
<!-- Title -->
<h1>Paper Title / Project Name — Deep Explainer</h1>

<!-- Meta info -->
<div class="paper-meta">
  <span class="label">Paper:</span> <a href="...">arxiv link</a><br>
  <span class="label">Authors:</span> Author list<br>
  <span class="label">Code:</span> <a href="...">GitHub link</a><br>
  <span class="label">Depth:</span> Standard explainer
</div>

<!-- One-sentence summary -->
<div class="summary-box">
  <h3>TL;DR</h3>
  <p>This paper proposes XXX method to solve YYY problem through ZZZ approach.</p>
</div>

<!-- Body content -->
<h2 id="background">Research Background</h2>
<p>...</p>

<h2 id="method">Core Method</h2>
<h3 id="method-1">Innovation 1: XXX</h3>
<p>...</p>

<!-- Code example -->
<div class="code-caption">model.py:42-58</div>
<pre><code class="language-python">class Attention(nn.Module):
    def __init__(self, d_model, n_heads):
        super().__init__()
        self.n_heads = n_heads
        self.d_k = d_model // n_heads
        # Core: three linear projections for Q, K, V
        self.W_q = nn.Linear(d_model, d_model)
        self.W_k = nn.Linear(d_model, d_model)
        self.W_v = nn.Linear(d_model, d_model)
</code></pre>
<p>This code defines the core multi-head attention class. <code>W_q</code>, <code>W_k</code>, <code>W_v</code> project the input into Query, Key, and Value vector spaces respectively.</p>

<!-- Formula -->
<p>The core attention formula:</p>
<p>$$\text{Attention}(Q, K, V) = \text{softmax}\left(\frac{QK^T}{\sqrt{d_k}}\right)V$$</p>
<p>Here $\sqrt{d_k}$ is the scaling factor that prevents large dot products from causing vanishing gradients in softmax.</p>

<!-- Mermaid diagram -->
<pre class="mermaid">
flowchart LR
    Input["Input x"] --> Q["Q = xW_q"]
    Input --> K["K = xW_k"]
    Input --> V["V = xW_v"]
    Q --> Attn["Q x K^T / sqrt(d_k)"]
    K --> Attn
    Attn --> Softmax["Softmax"]
    Softmax --> Output["Output = Attn x V"]
    V --> Output
</pre>

<!-- Table -->
<table>
  <tr><th>Method</th><th>BLEU</th><th>Latency</th><th>Params</th></tr>
  <tr><td>Baseline</td><td>32.1</td><td>120ms</td><td>65M</td></tr>
  <tr><td><strong>This paper</strong></td><td><strong>38.7</strong></td><td><strong>85ms</strong></td><td><strong>52M</strong></td></tr>
</table>
```

### KaTeX Formula Rules

| Type | Syntax | Example |
|------|--------|---------|
| Inline | `$...$` | `$\alpha + \beta$` |
| Block | `$$...$$` | `$$\sum_{i=1}^{n} x_i$$` |
| Superscript | `^{...}` | `$x^{2}$` |
| Subscript | `_{...}` | `$x_{i}$` |
| Fraction | `\frac{...}{...}` | `$\frac{a}{b}$` |
| Greek letters | `\alpha, \beta, ...` | `$\alpha$` |
| Vectors | `\mathbf{...}` | `$\mathbf{q}$` |
| Matrices | `\begin{pmatrix}...\end{pmatrix}` | `$$\begin{pmatrix} a & b \\ c & d \end{pmatrix}$$` |

### Mermaid Diagram Rules

- Wrap with `<pre class="mermaid">`
- Avoid Chinese punctuation and special characters in node text
- Common diagram types:
  - `flowchart LR/TB` — flowcharts
  - `sequenceDiagram` — sequence diagrams
  - `classDiagram` — class diagrams
  - `graph LR` — simple relationship graphs

---

## Dark Mode

Handled automatically by JS:
- Toggle button (🌓) in top-right corner
- State persisted to `localStorage`
- KaTeX, Mermaid, highlight.js all follow theme automatically
