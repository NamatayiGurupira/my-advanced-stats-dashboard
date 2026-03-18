# 📖 Advanced Workshop Guide

## From Repo to Dashboard: Advanced Governance through Automated Testing and CI/CD Deployment

> **"Professionalisation at scale — demonstrating and evidencing we follow best practice."**
>
> Welcome to the advanced workshop! This guide covers security, automated quality control, team governance, CI/CD pipelines, and advanced Copilot skills. You'll finish with a self-protecting, self-deploying repository that any NHS or public-sector data team would be proud of.

---

## ⏱️ Workshop at a Glance

| Module | Topic                               | Time    |
| ------ | ----------------------------------- | ------- |
| 1      | Security & Push Protection          | ~20 min |
| 2      | Pre-commit Hooks                    | ~20 min |
| 3      | Branch Protection & CODEOWNERS      | ~20 min |
| 4      | CI/CD with GitHub Actions           | ~20 min |
| 5      | Run the Notebook & Deploy           | ~15 min |
| 6      | Advanced Copilot for Governance     | ~15 min |
| 7      | Share Your Work!                    | ~5 min  |

**Total: ~115 minutes** (plus questions and exploration time)

---

## 🎯 What You'll Build

By the end of this workshop you'll have a repository that:

- **Self-cleans**: pre-commit hooks strip notebook outputs automatically — no accidental data leaks
- **Self-checks**: GitHub Actions runs quality checks on every pull request
- **Self-deploys**: every merge to `main` publishes your live dashboard to GitHub Pages automatically
- **Self-governs**: CODEOWNERS ensures the right people review the right files

> 💡 **This is what production-ready looks like in the public sector.** These aren't abstract concepts — they are the same practices used by the NHS England data team, the UK Cabinet Office, and data teams across government.

---

## Module 1 — Security & Push Protection

### Why does this matter?

Every week, credentials and sensitive data are accidentally committed to GitHub repositories by well-meaning analysts. An API key in a notebook. A patient identifier in a CSV. A database password in a config file. GitHub has built-in shields to stop this — but you need to know how to use them.

> **Safety rails, not policing.** These tools free you to focus on the analysis, not on worrying about what you might accidentally commit.

---

### 1.1 — Enable Secret Scanning

**Secret Scanning** automatically scans every push to your repository for known secret patterns (API keys, tokens, credentials) and alerts you immediately.

1. Go to your repository on GitHub
2. Click **Settings** → **Code security and analysis** (or **Security** → **Code security**)
3. Find **Secret scanning** and click **Enable**
4. Optionally enable **Push protection** (see below)

Once enabled, GitHub will:
- Scan all existing content
- Alert you to any detected secrets
- Show alerts under **Security** → **Secret scanning alerts**

---

### 1.2 — Enable Push Protection

**Push Protection** goes further: it intercepts your push *in real-time* and **blocks it** if it detects a secret. You'll get a clear message explaining what was detected and how to remove it.

1. In **Settings** → **Code security and analysis**
2. Under **Secret scanning**, click **Enable** next to **Push protection**

> 💡 **Try it out:** Create a test file with a fake AWS key pattern (`AKIAIOSFODNN7EXAMPLE`) and try to commit it. GitHub will block the push and show you exactly what was detected.

---

### 1.3 — What Push Protection looks like

When you try to push a commit containing a secret, you'll see something like:

```
remote: error: GH013: Repository rule violations found for refs/heads/main.
remote: - GITHUB PUSH PROTECTION
remote:   —————————————————————————————————————————
remote:     Detected secret(s)
remote:     Location:     config.py:12
remote:     Secret type:  aws_access_key_id
```

You have three options:
1. **Remove the secret** (correct approach) — delete it from the file, commit again
2. **Use a `.env` file** — store secrets outside the repo (see `.gitignore`)
3. **Mark as a test secret** — only if it's genuinely fake data for testing

---

### 1.4 — Best practice: `.gitignore` and environment variables

Never hardcode secrets in code. Instead, use environment variables:

```python
import os

# ✅ Good — reads from environment, never committed
API_KEY = os.environ.get("MY_API_KEY")

# ❌ Bad — hardcoded secret that could be pushed
API_KEY = "sk-abc123realtoken"
```

Make sure your `.gitignore` excludes secret files:

```gitignore
.env
*.env
secrets.json
config/local.py
```

> 💡 **Copilot tip:** Ask Copilot _"How do I load environment variables from a .env file in Python?"_ to set this up safely.

---

## Module 2 — Pre-commit Hooks: Your Self-Cleaning Repository

### What are pre-commit hooks?

Pre-commit hooks are scripts that run **automatically** every time you make a `git commit`. If any hook fails, the commit is blocked until you fix the issue. This is your first line of defence — problems are caught before they ever reach GitHub.

This template has hooks pre-configured in `.pre-commit-config.yaml`. Your Codespace installs them automatically.

---

### 2.1 — Understanding `.pre-commit-config.yaml`

Open `.pre-commit-config.yaml` in your repository. You'll see two sets of hooks:

**`nbstripout`** — strips Jupyter Notebook outputs:
```yaml
- repo: https://github.com/kynan/nbstripout
  rev: 0.7.1
  hooks:
    - id: nbstripout
```

This automatically removes all cell outputs, execution counts, and metadata from notebooks before committing. Why?
- Outputs can contain **real data** (patient counts, financial figures)
- Outputs make **diffs unreadable** — a chart PNG is thousands of lines of Base64
- Outputs contain **execution timestamps** that create noise in version history

**`ruff`** — Python linting and formatting:
```yaml
- repo: https://github.com/astral-sh/ruff-pre-commit
  rev: v0.4.7
  hooks:
    - id: ruff
      args: [--fix]
    - id: ruff-format
```

Ruff automatically fixes common Python errors and formats your code to a consistent standard — faster than Black, and it catches real bugs too.

---

### 2.2 — See the hooks in action

In your Codespace terminal, try committing a Python file with a style issue:

```bash
# Create a test file with poor formatting
echo "x=1+2
y=   3" > /tmp/test_style.py

# Copy it to the repo
cp /tmp/test_style.py test_style.py

# Try to commit it
git add test_style.py
git commit -m "Add test file"
```

You'll see Ruff automatically fix and re-stage the file:
```
ruff....................................................................Passed
ruff-format.............................................................Fixed
```

> 💡 **Key insight:** The hook fixed the code *for you* — you didn't have to manually format anything. This is what "automated quality" means.

---

### 2.3 — Install hooks manually (if needed)

If you're working outside Codespaces, install hooks with:

```bash
pip install pre-commit
pre-commit install
```

To run hooks on all files manually:
```bash
pre-commit run --all-files
```

---

### 2.4 — Clean up and commit

After exploring the hooks, remove the test file:

```bash
git restore .        # discard any uncommitted changes
# or
git rm test_style.py
```

---

## Module 3 — Branch Protection & CODEOWNERS

### Why branch protection?

Without branch protection, anyone with write access can push directly to `main` — including broken code, untested changes, or accidental deletions. Branch protection rules ensure that all changes to `main` go through a pull request and pass quality checks first.

> **Quality and resilience** — this is about continuity and standards, not surveillance.

---

### 3.1 — Set up Branch Protection Rules

1. Go to your repository on GitHub
2. Click **Settings** → **Branches**
3. Under **Branch protection rules**, click **Add branch ruleset** (or **Add rule**)
4. Set **Branch name pattern** to `main`
5. Enable the following:
   - ✅ **Require a pull request before merging**
     - Set **Required approvals** to `1`
   - ✅ **Require status checks to pass before merging**
     - Search for and add `Lint & Code Quality` (from your CI workflow)
   - ✅ **Do not allow bypassing the above settings** (optional but recommended)
6. Click **Create** or **Save changes**

Now nobody (including you!) can push directly to `main` without a passing PR.

---

### 3.2 — Test your branch protection

Try to push directly to main:

```bash
# Make a small change
echo "# test" >> README.md
git add README.md
git commit -m "Test direct push"
git push origin main
```

You should see:
```
remote: error: GH006: Protected branch update failed for refs/heads/main.
remote: error: Required status check "Lint & Code Quality" is expected.
```

Now create a branch and make a proper pull request:

```bash
git checkout -b feature/test-protection
git push origin feature/test-protection
```

Then on GitHub, open a pull request from your branch to `main`.

---

### 3.3 — Set up CODEOWNERS

Open `.github/CODEOWNERS` and replace `@YOUR-GITHUB-USERNAME` with your actual GitHub username:

```
# Default owner for everything
* @your-actual-username

# Data scripts require senior review
/workshop/ @your-actual-username

# Dashboard requires sign-off before going public
/docs/ @your-actual-username

# CI/CD configuration
/.github/ @your-actual-username
```

Commit and push this change. From now on, any pull request that touches these files will automatically request your review.

> 💡 **For teams:** Replace individual usernames with team names like `@your-org/data-governance-team`. This means any team member can approve, providing resilience.

---

### 3.4 — Understanding the governance model

With branch protection and CODEOWNERS in place:

```
Developer pushes code
       ↓
Creates a Pull Request
       ↓
GitHub automatically requests review from CODEOWNERS
       ↓
CI checks run (lint, notebook clean check)
       ↓
Reviewer approves
       ↓
Merge to main is allowed
       ↓
Dashboard automatically deploys
```

This is formal governance without bureaucracy — every step is automated.

---

## Module 4 — CI/CD with GitHub Actions

### What is CI/CD?

**Continuous Integration (CI)** means automatically running tests and checks every time code changes. **Continuous Deployment (CD)** means automatically deploying working code to production. Together, they eliminate the "it works on my machine" problem.

---

### 4.1 — Explore the workflows

Open `.github/workflows/` in your repository. You'll see two files:

**`ci.yml` — Quality checks:**
```yaml
on:
  pull_request:
    branches: [main]
```
Runs on every PR: lints Python with Ruff, checks notebooks have no outputs.

**`deploy.yml` — Automatic deployment:**
```yaml
on:
  push:
    branches: [main]
```
Runs on every push to `main`: deploys `docs/` to GitHub Pages automatically.

---

### 4.2 — Enable GitHub Pages for automated deployment

For the deploy workflow to work, enable GitHub Pages:

1. Go to **Settings** → **Pages**
2. Under **Source**, select **GitHub Actions** (not "Deploy from a branch")
3. Click **Save**

Now every time you merge a pull request to `main`, your dashboard deploys automatically. No manual steps.

---

### 4.3 — Watch a deployment happen

1. Make a small change to `docs/index.html` (e.g., change the title slightly)
2. Commit it on a branch and open a pull request
3. Watch the **CI checks** run in the PR — you'll see ✅ or ❌ next to your commits
4. Merge the PR
5. Go to the **Actions** tab — watch the **Deploy Dashboard to GitHub Pages** workflow run
6. When it completes, click the deployment URL — your live dashboard is updated!

---

### 4.4 — Reading workflow logs

If a check fails, click on the failing check to see the logs:

1. In your pull request, click the ❌ next to a failed check
2. Click **Details**
3. Expand the failing step to see exactly what went wrong

Common issues and fixes:

| Error | Fix |
|-------|-----|
| `ruff: E501 line too long` | Shorten the line or add `# noqa: E501` |
| `Notebook contains outputs` | Run `nbstripout workshop/workshop.ipynb` in the terminal |
| `Deploy failed: Pages not configured` | Enable GitHub Pages under Settings → Pages → GitHub Actions |

---

## Module 5 — Run the Notebook & Deploy Your Dashboard

### 5.1 — Open the notebook

In your Codespace, open `workshop/workshop.ipynb`.

This is the same notebook from the beginner workshop — run it to fetch Stats Wales data, create a chart, and export for the dashboard. The advanced addition here is that:
- Your pre-commit hooks will automatically strip outputs when you commit
- The CI workflow will verify the notebook is clean on your next PR
- Merging will auto-deploy your updated dashboard

---

### 5.2 — The full professional workflow

Practice this workflow — it's what production data pipelines look like:

```bash
# 1. Create a feature branch
git checkout -b feature/add-my-chart

# 2. Run the notebook, customise your chart

# 3. Commit (hooks will strip notebook outputs automatically!)
git add workshop/workshop.ipynb docs/
git commit -m "Add population chart for Wales 2023"
# ↑ nbstripout runs here, then ruff

# 4. Push your branch
git push origin feature/add-my-chart

# 5. Open a Pull Request on GitHub
# ↑ CI checks run here automatically

# 6. Merge when checks pass
# ↑ Dashboard deploys automatically
```

---

### 5.3 — Verify your live dashboard

After merging, visit your GitHub Pages URL:
`https://YOUR-USERNAME.github.io/YOUR-REPO-NAME/`

Your updated dashboard should reflect the latest merge within ~2 minutes.

> 💡 Check the **Actions** tab to see the deployment progress in real time.

---

## Module 6 — Advanced Copilot for Governance and Quality

### 6.1 — Use Copilot to explain complex changes

Before reviewing a pull request, use Copilot to understand what changed:

1. In VS Code, open the **Source Control** panel and view a diff
2. Select the changed code
3. Press `Ctrl+I` and try: _"Explain what this change does and whether it looks safe"_

Copilot can also explain GitHub Actions YAML:
- Select the content of `ci.yml` and ask: _"Explain what each step in this workflow does"_

---

### 6.2 — Generate a pull request summary with Copilot

When creating a pull request in VS Code (with the GitHub Pull Requests extension), Copilot can auto-generate a description:

1. Open the PR creation panel
2. Click the **Copilot icon** next to the description field
3. Copilot summarises your changes automatically

Alternatively, in the GitHub web interface:
1. Open a pull request
2. In the description box, type `/` and look for AI-assisted PR description (available on some plans)

---

### 6.3 — Refactor notebook code into a reusable script

Notebook code is great for exploration, but production pipelines use modular scripts. Ask Copilot to refactor:

1. Select the data-fetching cell from `workshop/workshop.ipynb`
2. Press `Ctrl+I` and try:

```
"Refactor this notebook cell into a reusable Python function called `fetch_stats_wales_data`
that takes a dataset_id and returns a pandas DataFrame. Add proper error handling,
type hints, and a docstring. Follow PEP 8 style."
```

3. Review Copilot's output — does it add error handling? Does the docstring explain parameters?
4. Ask follow-up: _"How would I write a unit test for this function?"_

> ⚠️ Always review Copilot's output. It's an excellent starting point, but you're responsible for what goes into production.

---

### 6.4 — Use Copilot for code review

Copilot can act as a first-pass reviewer:

1. Open a file with some Python code
2. Ask: _"Review this code for potential security issues or data quality problems"_
3. Or: _"Could this code accidentally expose patient data or API credentials?"_

Try it on the notebook's API fetch code — does Copilot spot anything to improve?

---

### 6.5 — Advanced Copilot prompts for data governance

| Goal | Prompt |
|------|--------|
| Explain a workflow | _"Explain what this GitHub Actions workflow does step by step"_ |
| Check for secrets | _"Does this code contain any hardcoded credentials or API keys?"_ |
| Add logging | _"Add structured logging to this function so it logs the dataset ID and row count fetched"_ |
| Add validation | _"Add input validation to check that dataset_id is a valid UUID before making the API call"_ |
| Generate docs | _"Write a CONTRIBUTING.md for this repository explaining the PR review process"_ |

---

## Module 7 — Share Your Work! 🎉

You've built something genuinely professional. This isn't a toy project — it demonstrates:
- Automated security scanning
- Quality-controlled, self-cleaning code
- Formal governance with enforced review processes
- CI/CD deployment to production

### Share it!

**LinkedIn:**

> "Just completed the Advanced Big Data Does GitHub workshop!
>
> Built a self-governing Stats Wales data pipeline featuring:
> ✅ Pre-commit hooks (nbstripout + Ruff) — automatic quality control
> ✅ Branch protection + CODEOWNERS — formal review governance
> ✅ GitHub Actions CI/CD — auto-deployed live dashboard
> ✅ Secret scanning — protecting sensitive data automatically
>
> All running on open Welsh government data via the Stats Wales API.
>
> 🔗 Dashboard: [your GitHub Pages URL]
> 🔗 Code: [your GitHub repo URL]
>
> #DataWales #NHSWales #GitHubCopilot #OpenData #PublicSector"

**Email to your manager:**

Subject: _"Advanced data governance skills from today's workshop"_

> Hi [Name],
>
> I attended the Advanced Big Data Does GitHub workshop today and built a production-ready data pipeline with:
> - Automated security scanning (GitHub Secret Scanning + Push Protection)
> - Pre-commit quality hooks that prevent bad code from entering the repository
> - Branch protection requiring code review before merging
> - CI/CD that automatically deploys our dashboard on every approved change
>
> The live dashboard is here: [your GitHub Pages URL]
> The repository (with all configuration) is here: [your GitHub repo URL]
>
> This is the kind of governance infrastructure that makes team data projects resilient, auditable, and professional. Happy to walk you through it.

---

## 🔧 Troubleshooting

| Problem | Solution |
|---|---|
| Pre-commit hook not running | Run `pre-commit install` in the terminal |
| `nbstripout: command not found` | Run `pip install nbstripout` |
| CI check failing with Ruff errors | Run `ruff check . --fix` to auto-fix, or see the error in the Actions log |
| Deploy workflow fails with Pages error | Go to Settings → Pages → set Source to **GitHub Actions** |
| Branch protection blocking direct push | Create a branch, open a PR — that's the intended workflow! |
| CODEOWNERS not requesting reviewers | Check the file is in `.github/CODEOWNERS` and usernames are correct |
| Codespace doesn't install hooks | Run `pre-commit install` manually in the terminal |

---

## 🚀 What Next?

- **Scheduled data refresh** — Add a `cron:` trigger to `deploy.yml` to refresh Stats Wales data automatically every day
- **Multi-environment deployment** — Add a `staging` branch that deploys to a preview URL before `main`
- **Test coverage** — Add `pytest` to your CI pipeline to run unit tests on your data pipeline
- **Welsh language support** — Change `lang=en-gb` to `lang=cy-gb` in API calls and add a language toggle to the dashboard
- **R integration** — The same governance patterns work for R projects with `lintr` replacing `ruff`

---

## 📚 Useful Links

| Resource | URL |
|---|---|
| Stats Wales API | https://api.stats.gov.wales |
| GitHub Secret Scanning docs | https://docs.github.com/code-security/secret-scanning |
| pre-commit documentation | https://pre-commit.com |
| nbstripout | https://github.com/kynan/nbstripout |
| Ruff linter | https://docs.astral.sh/ruff |
| GitHub Actions docs | https://docs.github.com/actions |
| GitHub Pages docs | https://docs.github.com/pages |
| CODEOWNERS syntax | https://docs.github.com/repositories/managing-your-repositorys-settings-and-features/customizing-your-repository/about-code-owners |
| Branch protection rules | https://docs.github.com/repositories/configuring-branches-and-merges-in-your-repository/managing-protected-branches |

---

_Built with ❤️ for data professionals across Wales. Professional standards. Zero barriers._

