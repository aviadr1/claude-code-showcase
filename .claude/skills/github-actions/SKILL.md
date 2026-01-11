# GitHub Actions for Claude Code

Reusable GitHub Actions workflows that integrate Claude Code into your CI/CD pipeline.

## Available Workflows

### 1. PR Review (`reusable-pr-review.yml`)

Automatically reviews pull requests using Claude Code.

**Triggers on:**
- Pull request opened/synchronized
- Issue comments mentioning `@claude`

**Usage:**
```yaml
name: Claude PR Review

on:
  pull_request:
  issue_comment:
    types: [created]

jobs:
  review:
    uses: aviadr1/claude-code-showcase/.github/workflows/reusable-pr-review.yml@main
    secrets:
      ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
    with:
      model: 'claude-sonnet-4-20250514'  # Optional, default shown
      timeout_minutes: 30                  # Optional
```

**Inputs:**
| Input | Type | Default | Description |
|-------|------|---------|-------------|
| `model` | string | `claude-sonnet-4-20250514` | Claude model to use |
| `timeout_minutes` | number | `30` | Timeout in minutes |
| `review_prompt` | string | (built-in) | Custom review prompt |

---

### 2. Documentation Sync (`reusable-docs-sync.yml`)

Periodically checks if documentation is out of sync with code changes.

**Usage:**
```yaml
name: Monthly Docs Sync

on:
  schedule:
    - cron: '0 9 1 * *'  # 1st of each month
  workflow_dispatch:

jobs:
  sync:
    uses: aviadr1/claude-code-showcase/.github/workflows/reusable-docs-sync.yml@main
    secrets:
      ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
    with:
      days_back: 30
      docs_paths: 'docs/ README.md'
```

**Inputs:**
| Input | Type | Default | Description |
|-------|------|---------|-------------|
| `model` | string | `claude-opus-4-5-20251101` | Claude model |
| `timeout_minutes` | number | `60` | Timeout |
| `days_back` | number | `30` | Days to look back for changes |
| `base_branch` | string | `main` | Base branch for PRs |
| `code_patterns` | string | `*.ts *.tsx *.js *.jsx` | File patterns to check |
| `docs_paths` | string | `docs/ README.md` | Documentation paths |
| `custom_prompt` | string | (built-in) | Custom prompt |

---

### 3. Code Quality Review (`reusable-code-quality.yml`)

Periodically reviews random directories for code quality issues and creates fix PRs.

**Usage:**
```yaml
name: Weekly Code Quality

on:
  schedule:
    - cron: '0 8 * * 0'  # Every Sunday
  workflow_dispatch:

jobs:
  quality:
    uses: aviadr1/claude-code-showcase/.github/workflows/reusable-code-quality.yml@main
    secrets:
      ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
    with:
      num_dirs: 3
      source_dir: 'src'
      lint_command: 'npm run lint'
```

**Inputs:**
| Input | Type | Default | Description |
|-------|------|---------|-------------|
| `model` | string | `claude-opus-4-5-20251101` | Claude model |
| `timeout_minutes` | number | `45` | Timeout per directory |
| `num_dirs` | number | `3` | Directories to review |
| `source_dir` | string | `src` | Source directory |
| `base_branch` | string | `main` | Base branch for PRs |
| `file_extensions` | string | `.ts .tsx` | Extensions to review |
| `lint_command` | string | `npm run lint` | Lint command |
| `review_checklist_path` | string | `.claude/agents/code-reviewer.md` | Checklist path |
| `custom_prompt` | string | (built-in) | Custom prompt |

---

### 4. Dependency Audit (`reusable-dependency-audit.yml`)

Periodically audits dependencies for updates and security vulnerabilities.

**Usage:**
```yaml
name: Bi-weekly Dependency Audit

on:
  schedule:
    - cron: '0 10 1,15 * *'  # 1st and 15th
  workflow_dispatch:

jobs:
  audit:
    uses: aviadr1/claude-code-showcase/.github/workflows/reusable-dependency-audit.yml@main
    secrets:
      ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
    with:
      node_version: '20'
      lint_command: 'npm run lint'
      test_command: 'npm test'
```

**Inputs:**
| Input | Type | Default | Description |
|-------|------|---------|-------------|
| `model` | string | `claude-opus-4-5-20251101` | Claude model |
| `timeout_minutes` | number | `45` | Timeout |
| `base_branch` | string | `main` | Base branch for PRs |
| `node_version` | string | `20` | Node.js version |
| `lint_command` | string | `npm run lint` | Lint command |
| `test_command` | string | `npm test` | Test command |
| `custom_prompt` | string | (built-in) | Custom prompt |

---

## Setup Requirements

### 1. Add Anthropic API Key

Add `ANTHROPIC_API_KEY` to your repository secrets:
1. Go to Settings → Secrets and variables → Actions
2. Click "New repository secret"
3. Name: `ANTHROPIC_API_KEY`
4. Value: Your Anthropic API key

### 2. Configure Permissions

Ensure your workflow has appropriate permissions:
```yaml
permissions:
  contents: write       # For creating branches
  pull-requests: write  # For creating PRs
  issues: read          # For reading issue comments (PR review)
```

### 3. Allow Workflow Calls (for reusable workflows)

If your repository is private, you may need to allow workflow calls from other repositories in Settings → Actions → General.

---

## Quick Start

Run `/setup-github-actions` to interactively set up these workflows in your repository.

---

## Best Practices

1. **Start with PR Review** - Most immediate value, low risk
2. **Use Sonnet for PR reviews** - Faster and cheaper for routine reviews
3. **Use Opus for complex tasks** - Code quality and dependency audits benefit from deeper analysis
4. **Customize prompts** - Add project-specific review criteria via `custom_prompt`
5. **Set appropriate timeouts** - Complex codebases may need longer timeouts
