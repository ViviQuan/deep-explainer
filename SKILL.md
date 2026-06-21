---
name: deep-explainer
description: >-
  Use when the user provides an academic paper (PDF, arxiv URL, pasted text) or a GitHub repository link
  and asks for explanation, analysis, walkthrough, deep dive, "解读", "解释", "讲解", "分析", "explain this code",
  "walk me through", "how does this work". Covers paper-to-HTML, repo-to-HTML, and combined paper+code explanations
  with code highlighting, formula rendering, architecture diagrams, and step-by-step walkthroughs.
---

# Deep Explainer

Turn any academic paper or GitHub repository into a beautiful, self-contained HTML explainer page that makes readers feel they understand the content better than reading the original.

## Positioning vs paper-analyzer

| | paper-analyzer | **deep-explainer** |
|---|---|---|
| Input | Paper-first | **Paper OR GitHub repo OR both** |
| Code handling | Searches for related code | **Clones repo directly, explains file-by-file, function-by-function** |
| Output focus | Paper method analysis | **Code implementation + paper theory, cross-referenced** |
| HTML style | 3 style choices | **Modern tech blog (Medium/Notion aesthetic)** |

## Quick Start

```
/explain https://arxiv.org/abs/2401.xxxxx
/explain https://github.com/user/repo
/explain /path/to/paper.pdf
/explain /path/to/paper.pdf --with-code https://github.com/user/repo
```

---

## Mandatory Workflow (6 Rounds, No Skipping)

### Round 1: Identify Input Type ⛔

| Input Type | Detection | Action |
|-----------|-----------|--------|
| arxiv URL | `arxiv.org/abs/` or `arxiv.org/pdf/` | Fetch abstract + full HTML |
| PDF file | Local path with `.pdf` | **Use cascading PDF extraction (see below)** |
| GitHub URL | `github.com/user/repo` | Clone repo, analyze structure |
| Paper + Code | `--with-code` flag or both provided | Read paper first, then code, cross-reference |
| Plain text | User pastes content directly | Use as-is |

**Self-check**: Do I have complete content? No → try alternative method.

#### PDF Extraction — Cascading Strategy ⛔

⚠️ **DO NOT use the Read tool on PDF files.** Many models (especially non-Claude models) cannot read PDFs natively and will error out immediately. Always extract text via CLI tools first.

**Always run these steps in order. Stop at the first method that yields complete text.**

**Method 1: `pdftotext` (poppler) — TRY THIS FIRST**
```bash
# Install if missing (macOS):
which pdftotext || brew install poppler
# Install if missing (Linux):
# which pdftotext || sudo apt-get install -y poppler-utils

# Extract with layout preservation:
pdftotext -layout "/path/to/paper.pdf" /tmp/paper.txt

# Verify quality:
head -50 /tmp/paper.txt
wc -l /tmp/paper.txt
```
- If `pdftotext` is available and output has readable text → **done, use this**.
- If not installed and cannot install → Method 2.

**Method 2: Python + PyMuPDF (`fitz`)**
```bash
python3 -c "
import fitz
doc = fitz.open('/path/to/paper.pdf')
for i, page in enumerate(doc):
    print(f'--- Page {i+1} ---')
    print(page.get_text())
" > /tmp/paper.txt 2>/dev/null
head -50 /tmp/paper.txt
```
- If `fitz` not installed: `pip install pymupdf` then retry.
- Works on most PDFs including those with embedded fonts.

**Method 3: Python + `pdfplumber`**
```bash
python3 -c "
import pdfplumber
with pdfplumber.open('/path/to/paper.pdf') as pdf:
    for i, page in enumerate(pdf.pages):
        print(f'--- Page {i+1} ---')
        text = page.extract_text()
        if text: print(text)
" > /tmp/paper.txt 2>/dev/null
head -50 /tmp/paper.txt
```
- If not installed: `pip install pdfplumber` then retry.
- Better at preserving table structure than PyMuPDF.

**Method 4: arxiv HTML (for arxiv papers only)**
- If the paper is on arxiv, fetch `https://arxiv.org/html/{paper_id}` for the full HTML version.
- Often better than PDF extraction — structured, clean, machine-readable text.

**Method 5: Native Read tool (last resort)**
- Only try if ALL CLI methods above failed.
- Use the Read tool on the PDF file directly.
- Some models support this, many do not — that's why it's last.

**Method 6: OCR fallback (scanned PDFs)**
```bash
# Only if all above fail — likely a scanned/image PDF
python3 -c "
import fitz
doc = fitz.open('/path/to/paper.pdf')
for i, page in enumerate(doc):
    pix = page.get_pixmap(dpi=300)
    pix.save(f'/tmp/pdf_page_{i}.png')
" 2>/dev/null
ls /tmp/pdf_page_*.png
```
- Extract images, then process with vision model or tesseract OCR.

**After extraction, read the result in batches:**
```bash
# Check total size first
wc -l /tmp/paper.txt
# Then read in chunks of 200 lines
# Read /tmp/paper.txt offset=0 limit=200
# Read /tmp/paper.txt offset=200 limit=200
# ...continue until done
```

**Quality check** — after any method, verify:
- [ ] Paper title visible in output?
- [ ] Abstract / summary readable?
- [ ] At least some section headings present?
- [ ] Not more than 20% garbled characters?

If quality check fails → try next method. If ALL methods fail, inform the user and ask them to paste the text manually.

### Round 2: Deep Reading ⛔

**Paper mode**:
1. Read full text (extracted via Round 1), extract: title, authors, abstract, core methods, experimental results
   - If text is long (>200 lines), read in batches: `Read /tmp/paper.txt offset=0 limit=200`, then `offset=200 limit=200`, etc.
   - For arxiv HTML, fetch and read the main content sections sequentially
2. Identify 1-3 core innovations
3. Find code repository cited in paper (if any)
4. Map the method's input → processing → output flow

**Code mode**:
1. Clone repo: `git clone --depth 1 <url> /tmp/repo_name`
   - If clone fails (auth required, large repo) → use GitHub API: `curl -s https://api.github.com/repos/{owner}/{repo}/git/trees/main?recursive=1`
   - If API rate-limited → browse the repo structure via web and read key files individually
2. Read README → understand project goals
3. Read directory structure: `find /tmp/repo_name -type f | head -100` → understand module layout
4. Read entry files (main.py / index.ts / app.py etc.) → understand execution flow
5. Read core implementation files → understand key algorithms and data structures
6. Read config files (package.json / requirements.txt / pyproject.toml) → understand parameters and dependencies
7. Map architecture diagram / data flow diagram

**Paper + Code mode**:
1. Read paper first, understand "what it wants to do"
2. Then read code, understand "how it's implemented"
3. Cross-reference: each paper formula/algorithm → corresponding code file/function

### Round 3: Structured Analysis ⛔ (internal, do not show process)

Produce these analysis results (internal use only, not shown to user):

**Paper analysis**:
- One-sentence summary
- Core innovations (1-3, each one sentence)
- Method details: for each innovation ① problem ② approach ③ why it works ④ formula
- Experimental results: most convincing data
- Limitations

**Code analysis**:
- Project architecture overview
- Core module list (each module: responsibility, key files, core functions)
- Data flow: complete path from input to output
- Key algorithm walkthrough: each algorithm's ① purpose ② implementation ③ code correspondence
- Design patterns and engineering highlights

### Round 4: Ask User ⛔

**Must ask** (if user hasn't specified):

```
I've finished reading. Before generating the HTML explainer, a few questions:

1. **Depth**:
   - Quick overview (~2000 words, core points only)
   - Standard explainer (~5000 words, detailed explanation) ← recommended
   - Deep dive (~8000+ words, line-by-line code walkthrough)

2. **Code-related** (if applicable):
   - Function-by-function walkthrough?
   - Code snippet highlights?
   - Architecture / data flow diagrams?

3. **Language**: Chinese (with English technical terms) / English only
```

### Round 5: Generate HTML ⛔

Generate a complete HTML file following the template and specs in [references/html-template.md](references/html-template.md).

⚠️ **CRITICAL INSTRUCTION — READ CAREFULLY:**

1. **DO NOT announce what you are about to do.** Do not write "Now I'll generate the HTML" or "Let me write the file" or any similar text. These are wasted tokens.
2. **Call the Write tool IMMEDIATELY** with the complete HTML content. Your ONLY action in this round is a single Write tool call containing the full HTML from `<!DOCTYPE html>` to `</html>`.
3. **DO NOT stop mid-file.** The HTML WILL be 500-1500 lines. This is expected. Write it all in one Write call.
4. **If you feel the file is too large**, write it anyway. Do not ask to continue. Do not split into parts. One Write call, full content.
5. **Zero preamble text.** Your message to the user in this round should be empty — just the Write tool call.

**File naming**:
- Paper: `[paper-short-name]-explainer.html`
- Code: `[repo-name]-explainer.html`
- Both: `[paper-short-name]-code-explainer.html`

Output to current working directory.

### Round 6: Self-Review ⛔

⚠️ **DO NOT announce "let me review" or "checking now".** Just do the review silently. If fixes are needed, apply them directly with Edit — do not explain what you're about to fix. Your final message should be a brief summary only: "Done. File: [path]. Sections: [list]. Word count: [N]."

Check each item, fix until all pass:

**General**:
- [ ] Word count met? (quick ≥2000 / standard ≥5000 / deep ≥8000)
- [ ] Valid HTML, opens directly in browser?
- [ ] Sidebar navigation works?
- [ ] Code blocks have syntax highlighting?
- [ ] Formulas render correctly (KaTeX)?
- [ ] Diagrams render correctly (Mermaid)?
- [ ] Responsive design, readable on mobile?
- [ ] Dark mode toggle works?
- [ ] No AI filler phrases?

**Paper explainer checks**:
- [ ] One-sentence summary clear?
- [ ] Each core innovation has its own deep section?
- [ ] Every formula has a plain-language explanation?
- [ ] Experimental data has tables + interpretation?
- [ ] At least 2 limitations identified?
- [ ] ⚠️ Every method has a concrete running example with specific inputs/outputs?
- [ ] ⚠️ Complex concepts have real-world analogies?
- [ ] ⚠️ At least one numerical walkthrough (showing actual numbers, not just symbols)?

**Code explainer checks**:
- [ ] Project architecture diagram (Mermaid)?
- [ ] Each core module explained?
- [ ] Code snippets annotated with file path:line numbers?
- [ ] Clear data flow diagram?
- [ ] Every code block has a "what this does" explanation?
- [ ] ⚠️ Key functions traced through with concrete input shapes/values?
- [ ] ⚠️ At least one end-to-end example showing data flowing through the whole pipeline?

---

## HTML Output Spec

See [references/html-template.md](references/html-template.md) for the full template.

**Core requirements**:
- **Single self-contained file**: all CSS/JS inline or via CDN, no external dependencies
- **Modern tech blog style**: Medium/Notion aesthetic
- **Sticky sidebar TOC**: auto-generated from h2/h3, scroll-spy highlighting
- **Code highlighting**: highlight.js (GitHub theme, dark mode variant)
- **Formula rendering**: KaTeX (`$$...$$` block, `$...$` inline)
- **Diagrams**: Mermaid (architecture, flowcharts, data flow)
- **Dark mode**: toggle button in top-right, persisted to localStorage
- **Responsive**: sidebar collapses to floating button on mobile

---

## Code Explainer Rules

### Code Snippet Display

1. **Always annotate source**: every code block preceded by `file_path:line_number` caption
2. **Always explain**: every code block followed by plain-language explanation of what it does and why
3. **Limit length**: single code block ≤ 30 lines, split and explain in segments if longer
4. **Highlight key lines**: use comments to mark core logic
5. **Add flowcharts**: complex logic gets a Mermaid diagram

### Code Section Structure

```markdown
## Core Module: [Module Name]

### What It Does
One sentence stating the module's responsibility.

### How It's Organized
List key files and their relationships.

### Key Implementation
[Code snippets + step-by-step explanation]

### Design Highlights
What's clever about this module's engineering.
```

### ⚠️ Mandatory: Trace Through Code with Concrete Examples

**Every code explanation MUST walk through a specific example.** Do NOT just say what a function does — show it in action.

**For each key function / class / module, you MUST:**

1. **Show a concrete input** — what does the data look like going IN? E.g., "Suppose we pass in a batch of 4 images, each 224×224×3."

2. **Trace the execution** — step through the code line by line with the example input. Show intermediate values. E.g., "After `self.conv1(x)`, the shape changes from [4, 3, 224, 224] to [4, 64, 112, 112]."

3. **Show the output** — what comes out? What does it mean?

4. **Explain the "why"** — why is this code written this way? What would happen if it were written differently? E.g., "The residual connection `x + self.attn(x)` is critical — without it, gradients would vanish through the 12 transformer layers."

**Bad (just describes, no example):**
> The forward method applies attention and adds a residual connection.

**Good (traces through with data):**
> Let's trace what happens when we pass in `x` with shape `[batch=4, seq_len=128, d=768]`:
> 1. `self.norm(x)` → normalizes, shape still `[4, 128, 768]`
> 2. `self.attn(normed)` → multi-head attention computes Q,K,V each `[4, 128, 768]`, splits into 12 heads of 64 dims each, computes attention weights `[4, 12, 128, 128]`, outputs `[4, 128, 768]`
> 3. `x + self.attn(normed)` → residual connection adds the original input back. This is the key design: even if attention outputs zeros, the signal still flows through via the skip connection.
> 4. Result: `[4, 128, 768]` — same shape, but now each token has information from all other tokens in the sequence.

---

## Paper Explainer Rules

### Formula Display

1. **Plain language first**: explain what the formula means in words before showing it
2. **Translate after**: every formula followed by a one-sentence human-readable explanation
3. **Define symbols**: explain mathematical notation on first appearance
4. **Use KaTeX**: `$$...$$` for block, `$...$` for inline

### Paper Section Structure

```markdown
## One-Sentence Summary
Under 100 words. What does this paper do?

## Research Background
What problem does this field solve? What are the limitations of existing methods?

## Core Method
### Innovation 1: [Name]
**Problem**: What's being solved?
**Approach**: How is it done? (with formula + diagram)
**Why It Works**: How is it better than prior work?

### Innovation 2: [Name]
...

## Experimental Results
Key data tables + deep interpretation.

## Limitations
Author-stated + your independent assessment.
```

### ⚠️ Mandatory: Example-Driven Explanation

**Every method section MUST include concrete examples.** Abstract descriptions alone are unacceptable.

**For each innovation / algorithm / mechanism, you MUST provide:**

1. **A running example** — pick a specific, concrete input and trace it through the entire method step by step. Do NOT just say "the model takes input and produces output." Show WHAT the input looks like, WHAT happens at each step, and WHAT the output looks like.

2. **A worked-through numerical example** (when applicable) — if the method involves math (attention scores, routing weights, loss functions), show actual numbers, not just symbols. E.g., don't just write $QK^T$; show "Q = [0.2, 0.8], K = [0.5, 0.3], so QK^T = 0.34".

3. **An analogy** — for each non-obvious concept, provide a real-world analogy. E.g., "Mixture-of-Experts is like a hospital: instead of one general doctor seeing every patient, you have specialists (cardiologist, neurologist) and a triage nurse (the router) who sends each patient to the right specialist."

4. **A before/after comparison** — show what happens WITHOUT the innovation vs WITH it, using the same example input. This makes the contribution tangible.

**Bad (abstract, no example):**
> The gating network computes a sparse probability distribution over experts and routes each token to the top-k experts.

**Good (concrete, with example):**
> The gating network takes each token and decides which experts should handle it. For example, given the token "cat", the router computes logits for all 8 experts: [2.1, 0.3, 1.8, 0.1, 0.5, 0.2, 1.9, 0.4]. After softmax and top-2 selection, experts 1 and 7 are chosen (weights 0.45 and 0.38). This means "cat" — a noun — gets routed to the syntactic expert (1) and the semantic expert (7), while the math-specialist expert (4) is skipped entirely. Compare this to a dense model where ALL experts process every token — MoE saves 75% of the computation here.

---

## Guardrails

- **No fabrication**: all explanations must be grounded in actual paper/code content
- **Code must be real**: referenced code must come from actual files, never invented
- **Accurate references**: paper figure/table numbers, code file paths must be correct
- **No AI filler**: avoid "deep dive into", "it is worth noting", "in conclusion", "leveraging"
- **Short paragraphs**: max 4 sentences per paragraph
- **Explain jargon**: brief definition on first use of technical terms
- **⚠️ Every method needs an example**: if you describe a mechanism without a concrete example, it's incomplete. Abstract descriptions without examples are not acceptable.
