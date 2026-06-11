# phd_writing

A LaTeX project for PhD proposal writing, organised by chapter/topic.

---

## LaTeX Setup Tutorial

This guide walks you through setting up this project correctly from scratch.

---

### Step 1: Install a LaTeX Distribution

On macOS, install **MacTeX** (full distribution, recommended):

```bash
brew install --cask mactex
```

Or download directly from [tug.org/mactex](https://tug.org/mactex/).

After installation, verify it works:

```bash
pdflatex --version
latexmk --version
```

---

### Step 2: Install VS Code Extension

Install the **LaTeX Workshop** extension in VS Code:

1. Open the Extensions panel (`Cmd+Shift+X`)
2. Search for `LaTeX Workshop` by James Yu
3. Click **Install**

LaTeX Workshop handles compilation, live PDF preview, and syntax highlighting automatically.

---

### Step 3: Understand the Project Structure

```
phd_writing/
  proposal/
    proposal.tex              ← ROOT file (compile this one only)
    preamble.tex              ← all \usepackage{} calls (create this)
    macros.tex                ← custom \newcommand definitions (create this)
    references.bib            ← single bibliography file (create this)
    continuum_model/
      cm.tex                  ← chapter content only, no \documentclass
      figures/                ← store figures here
    molecular_dynamics/
      md.tex
      figures/
    sensitivity_analysis/
      sensitivity_analysis.tex
      figures/
```

**Key rule:** Only `proposal.tex` has `\documentclass`. All sub-files contain content only.

---

### Step 4: Set Up the Root File (`proposal.tex`)

Replace the contents of `proposal.tex` with this structure:

```latex
\documentclass[12pt, a4paper]{report}

\input{preamble}   % load all packages
\input{macros}     % load custom commands

\begin{document}

\title{Your PhD Proposal Title}
\author{Your Name}
\date{\today}
\maketitle

\tableofcontents

\include{continuum_model/cm}
\include{molecular_dynamics/md}
\include{sensitivity_analysis/sensitivity_analysis}

\printbibliography

\end{document}
```

> Use `\include{}` for chapters (starts new page, supports selective compilation).  
> Use `\input{}` for preamble/macros (no page break, no caching).

---

### Step 5: Create `preamble.tex`

Create `proposal/preamble.tex` and add your packages:

```latex
% Typography & encoding
\usepackage[T1]{fontenc}
\usepackage[utf8]{inputenc}
\usepackage{lmodern}

% Math
\usepackage{amsmath, amssymb, amsthm}

% Figures
\usepackage{graphicx}
\graphicspath{
  {continuum_model/figures/}
  {molecular_dynamics/figures/}
  {sensitivity_analysis/figures/}
}

% Tables
\usepackage{booktabs}

% Hyperlinks & PDF metadata
\usepackage[colorlinks=true, linkcolor=blue, citecolor=blue]{hyperref}

% Bibliography (BibLaTeX + Biber)
\usepackage[backend=biber, style=authoryear, sorting=nyt]{biblatex}
\addbibresource{references.bib}
```

---

### Step 6: Set Up Sub-files (Chapters)

Each chapter file should follow this pattern.

**Option A — Simple `\input` (no standalone compilation):**

```latex
% cm.tex
\chapter{Continuum Model}

\section{Introduction}
Content here...
```

**Option B — `subfiles` package (recommended: lets you compile chapters independently):**

First add `\usepackage{subfiles}` to `preamble.tex`.

Then each sub-file becomes:

```latex
% cm.tex
\documentclass[../proposal]{subfiles}

\begin{document}

\chapter{Continuum Model}

\section{Introduction}
Content here...

\end{document}
```

You can then compile `cm.tex` directly to preview just that chapter.

---

### Step 7: Add the Magic Comment to Sub-files

Add this as the **first line** of every sub-file so VS Code always compiles from the root:

```latex
% !TEX root = ../../proposal.tex
```

Adjust the relative path depending on the file's depth.

---

### Step 8: Configure `latexmk` for Building

Create a file `.latexmkrc` in the `proposal/` directory:

```perl
# .latexmkrc
$pdf_mode = 1;          # generate PDF via pdflatex
$bibtex_use = 2;        # run biber automatically
$clean_ext = "bbl bcf run.xml";
```

To build the whole proposal, run from `proposal/`:

```bash
latexmk -pdf proposal.tex
```

To auto-recompile on save (watch mode):

```bash
latexmk -pdf -pvc proposal.tex
```

To clean build artifacts:

```bash
latexmk -c
```

---

### Step 9: Add a `.gitignore`

Create `.gitignore` at the repo root to avoid committing build artifacts:

```
# LaTeX build artifacts
*.aux
*.bbl
*.bcf
*.blg
*.fdb_latexmk
*.fls
*.log
*.out
*.run.xml
*.synctex.gz
*.toc
*.lof
*.lot

# Build output (keep the final PDF if desired)
# *.pdf
```

Commit the `.gitignore` before your next `git add .`.

---

### Step 10: Verify the Setup

1. Open `proposal/proposal.tex` in VS Code
2. Press `Cmd+Shift+P` → **LaTeX Workshop: Build LaTeX project**
3. A `proposal.pdf` should appear alongside `proposal.tex`
4. Press `Cmd+Alt+V` to open the live PDF preview

---

### Quick Reference

| Task | Command |
|---|---|
| Build PDF | `latexmk -pdf proposal.tex` |
| Watch & rebuild | `latexmk -pdf -pvc proposal.tex` |
| Clean artifacts | `latexmk -c` |
| Full clean (inc. PDF) | `latexmk -C` |
| Check for errors | `latexmk -pdf 2>&1 \| grep -i error` |

