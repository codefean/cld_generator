# Causal Loop Diagram Generator

A self-contained, single-file web app for generating causal loop diagrams (CLDs) from a simple text format. Detects feedback loops automatically and labels them as **Reinforcing (R)** or **Balancing (B)**.


## What it does

- Parse a list of cause-effect connections like `Population -> Births +`
- Render an interactive force-directed diagram (drag nodes, zoom, pinch on mobile)
- Auto-detect every feedback loop and classify it
- Export to **`.rda`** (R binary), **`.csv`**, or plain-text loop reports
- Copy the loops summary to clipboard

Works entirely in the browser. No server, no build step, no dependencies to install.

## Input format

One connection per line:

```
Source -> Target +
Source -> Target -
```

- **Arrow:** `->`, `→`, `=>`, or `causes`
- **Polarity:** `+` (same direction) or `-` (opposite direction) at end of line
- **Names:** any text — multi-word names work without quotes
- **Comments:** start a line with `#`

Example:

```
# Burnout cycle
Workload -> Stress +
Stress -> Performance -
Performance -> Workload +
```

## Loop classification

A feedback loop is **Reinforcing (R)** if it has an even number of negative links, and **Balancing (B)** if it has an odd number. This is the standard convention from systems dynamics.

## Deploy to GitHub Pages

1. Create a new repository on GitHub
2. Drop `index.html` (and optionally `.nojekyll`) into the repo root
3. Settings → Pages → Source: **Deploy from a branch** → branch `main`, folder `/` (root)
4. Wait ~30 seconds, then visit `https://<your-username>.github.io/<repo-name>/`

That's the whole deploy. No actions, no workflows, no build.

## Local preview

Just open `index.html` in any browser, or serve the folder:

```bash
python3 -m http.server 8000
# then open http://localhost:8000
```

## Tech

Single HTML file with React, D3, Lucide, and Tailwind loaded from CDNs. JSX is compiled in-browser by Babel Standalone — fine for an app of this size, and avoids any build tooling. All export logic (including the hand-written R serialization) runs client-side.

## Working with exported files in R

```r
load("cld.rda")
head(cld)
#         from         to polarity
# 1 Population     Births        +
# 2     Births Population        +
# ...
```

Or from CSV:

```r
cld <- read.csv("cld.csv", stringsAsFactors = FALSE)
```
