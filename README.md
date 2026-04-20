# From Repo to Dashboard: Advanced Governance 🏴󠁧󠁢󠁷󠁬󠁳󠁿 (Namatayi)

## Automated Testing, CI/CD Deployment & Team Governance for Data Analysts

> **"Professional-grade data pipelines, without the complexity."**

Welcome back! 👋 This is the **advanced** Big Data Does GitHub workshop. You can start here directly, or use it as a follow-on from the [beginner workshop](https://github.com/NHS-Wales-Big-Data-Events/Big-Data-Does-GitHub-Workshop-Template).

This template takes you from individual analysis to **production-ready, team-governed, automatically-deployed** data products. The focus is on **automating quality**, **preventing sensitive data leaks**, and establishing **formal review processes** — the kind of professionalism that belongs in national health data infrastructure.

By the end, you'll have:

- ✅ A **self-cleaning repository** that automatically strips notebook outputs before every commit
- ✅ **Automated quality checks** (CI) that run on every pull request
- ✅ A **live dashboard** that deploys itself to GitHub Pages on every merge to `main`
- ✅ **Branch protection** rules and **CODEOWNERS** for formal team review
- ✅ Hands-on experience with **GitHub Copilot** for code review and refactoring

---

## 🚀 Quick Start (5 minutes)

### Step 1 — Use this template

Click the green **"Use this template"** button at the top of this page → **"Create a new repository"**. Give it a name like `my-advanced-stats-dashboard`.

### Step 2 — Enable GitHub Pages (automated deployment)

In your new repository:
1. Click **Settings** → **Pages**
2. Under **Source**, select **GitHub Actions**
3. That's it — the `deploy.yml` workflow will handle everything automatically on every push to `main`.

### Step 3 — Open in Codespaces

Click the green **"Code"** button → **"Codespaces"** tab → **"Create codespace on main"**.

The Codespace automatically installs all tools including **pre-commit hooks** — your "self-cleaning" safety rails are active from the moment you start coding.

### Step 4 — Follow the Workshop Guide

Open [`WORKSHOP_GUIDE.md`](./WORKSHOP_GUIDE.md) for the full advanced walkthrough.

---

## 📋 Workshop Overview

| Module | Topic                          | What you'll learn                                                 |
| ------ | ------------------------------ | ----------------------------------------------------------------- |
| 1      | 🔒 Security & Push Protection  | Secret scanning, push protection, safety rails not policing       |
| 2      | 🪝 Pre-commit Hooks             | nbstripout, Ruff/Black, automated cleaning before every commit    |
| 3      | 🌿 Branch Protection           | Protecting `main`, required PR reviews, status checks             |
| 4      | 👥 CODEOWNERS & Governance     | Automatic reviewer assignment for critical files                  |
| 5      | ⚙️ CI/CD with GitHub Actions   | Quality checks on PRs, auto-deploy dashboard to GitHub Pages      |
| 6      | 🤖 Advanced Copilot            | PR summaries, code review, refactoring notebook code into scripts |
| 7      | 🌐 Your Live Dashboard         | Run the notebook, publish to GitHub Pages, share your work        |

---

## 📁 Repository Structure

```
.
├── README.md                        ← You are here
├── WORKSHOP_GUIDE.md                ← Step-by-step advanced workshop walkthrough
├── .pre-commit-config.yaml          ← Pre-commit hooks (nbstripout + Ruff)
├── .devcontainer/
│   └── devcontainer.json            ← Codespaces config (includes pre-commit tools)
├── .github/
│   ├── CODEOWNERS                   ← Automatic reviewer assignment
│   └── workflows/
│       ├── ci.yml                   ← Quality checks on every pull request
│       └── deploy.yml               ← Auto-deploy dashboard to GitHub Pages
├── workshop/
│   └── workshop.ipynb               ← Stats Wales data notebook
└── docs/
    └── index.html                   ← Live dashboard (auto-deployed via GitHub Pages)
```

---

## 🛠️ Pre-requisites

- **A GitHub account** (free at [github.com](https://github.com))
- **Basic Git experience** — you should be comfortable with commit, branch, and pull request
- **A browser** — Codespaces handles everything else
- **Basic YAML familiarity** — helpful for reading the workflow files

> Coming from the beginner workshop? You already have all of these. 🎉

---

## 🔧 What's Pre-configured For You

### 🪝 Pre-commit Hooks (`.pre-commit-config.yaml`)

Automatically activated in Codespaces. Every commit you make will:
- **Strip notebook outputs** via `nbstripout` — no more accidental data leaks
- **Lint your Python** via `ruff` — catches errors before they hit the repo
- **Format your code** via `ruff format` — consistent style, automatically

### ⚙️ GitHub Actions Workflows

| Workflow | Trigger | What it does |
|---|---|---|
| `ci.yml` | Every PR to `main` | Runs Ruff linting + checks notebooks are clean |
| `deploy.yml` | Every push to `main` | Deploys `docs/` to GitHub Pages automatically |

### 👥 CODEOWNERS (`.github/CODEOWNERS`)

A template that automatically assigns reviewers to pull requests touching critical files. Edit it to add your GitHub username.

---

## 🏴󠁧󠁢󠁷󠁬󠁳󠁿 About the Data

This workshop uses the **Stats Wales API** — a free, open API for official Welsh statistics.

- [Browse all datasets](https://api.stats.gov.wales/v1/?lang=en-gb&page_number=1&page_size=100)
- [Stats Wales website](https://statswales.gov.wales)

---

## 🤝 Share Your Work!

You've built something genuinely professional. Show it off!

- **LinkedIn** — tag `#StatsWales #GitHubCopilot #DataWales #NHS`
- **Teams** — drop your GitHub Pages URL in your team channel
- **Your manager** — this demonstrates real production-ready skills 🎉

Suggested post:

> "Just completed the Advanced Big Data Does GitHub workshop! Built a self-governing data pipeline with automated quality checks, CI/CD deployment, and branch protection — all publishing a live Stats Wales dashboard.
> 🔗 [your GitHub Pages URL]
> #DataWales #GitHubCopilot #NHSWales #OpenData"

---

## 📖 Full Walkthrough

Head to **[WORKSHOP_GUIDE.md](./WORKSHOP_GUIDE.md)** for the complete step-by-step guide.

# test
# test
