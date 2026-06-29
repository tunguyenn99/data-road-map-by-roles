# 🌿 Git — Learning Guide (Beginner → Advanced)

## Tổng quan

Git là version control system cho code. Bắt buộc cho mọi role muốn làm việc chuyên nghiệp trong data team.

**Dùng cho:** Tất cả roles (AE, BI Eng bắt buộc; DA, BI, DG nên biết)

---

## Level 1: Beginner (1 tuần)

### Core Concepts
- Repository (repo), commit, branch
- Working directory → Staging area → Repository
- Remote vs Local

### Essential Commands
```bash
# Setup
git init                          # Tạo repo mới
git clone <url>                   # Clone repo từ remote
git config user.name "Your Name"
git config user.email "your.email@company.com"

# Daily workflow
git status                        # Xem thay đổi
git add file.sql                  # Stage 1 file
git add .                         # Stage all (cẩn thận!)
git commit -m "feat: add customer staging model"  # Commit
git push                          # Push to remote
git pull                          # Pull latest changes

# View history
git log --oneline -10             # 10 commits gần nhất
git diff                          # Xem changes chưa stage
git diff --staged                 # Xem changes đã stage
```

### Commit Message Convention
```
feat: add new feature
fix: fix a bug
docs: documentation only
refactor: code refactoring
test: add/update tests
chore: maintenance tasks
```

### Bài tập
1. Tạo repo, add files, commit 5 lần
2. Clone repo, make changes, push
3. View log, diff between commits
4. Practice `.gitignore` patterns

### Resources
| Resource | Link | Type |
|---|---|---|
| Git - The Simple Guide | [rogerdudler.github.io/git-guide](https://rogerdudler.github.io/git-guide/) | Free |
| Learn Git Branching (visual) | [learngitbranching.js.org](https://learngitbranching.js.org/) | Free |
| Oh Shit, Git!?! | [ohshitgit.com](https://ohshitgit.com/) | Free |
| GitHub Desktop (GUI) | [desktop.github.com](https://desktop.github.com/) | Free |

---

## Level 2: Intermediate (2 tuần)

### Branching & Merging
```bash
# Branch workflow
git branch feature/add-customer-model   # Create branch
git checkout feature/add-customer-model  # Switch
git checkout -b feature/new-model        # Create + switch

# Merge
git checkout main
git merge feature/add-customer-model

# Rebase (cleaner history)
git checkout feature/my-branch
git rebase main

# Delete branch after merge
git branch -d feature/done-branch
```

### Pull Request (PR) Workflow
```bash
# 1. Create branch from main
git checkout main && git pull
git checkout -b feature/stg-payment

# 2. Work, commit
git add models/staging/stg_payment.sql
git commit -m "feat: add stg_payment model"

# 3. Push branch
git push -u origin feature/stg-payment

# 4. Create PR on GitHub/GitLab
# 5. Code review
# 6. Merge + delete branch
```

### Conflict Resolution
```bash
# When merge has conflicts
git merge main
# Edit conflicted files (look for <<<<<<< markers)
git add resolved_file.sql
git commit -m "resolve: merge conflict in customer model"
```

### Stash (tạm cất changes)
```bash
git stash                    # Cất changes tạm
git stash list               # List stashes
git stash pop                # Lấy lại changes
git stash drop               # Xóa stash
```

### Bài tập
1. Tạo 3 branches, merge vào main
2. Tạo conflict cố ý, resolve
3. Practice PR workflow: branch → commit → push → PR → merge
4. Use stash: switch branch mid-work

### Resources
| Resource | Link | Type |
|---|---|---|
| Atlassian Git Tutorials | [atlassian.com/git](https://www.atlassian.com/git) | Free |
| Pro Git Book (free) | [git-scm.com/book](https://git-scm.com/book/en/v2) | Free |
| GitHub Skills | [skills.github.com](https://skills.github.com/) | Free |

---

## Level 3: Advanced (2 tuần)

### Git for Teams
```bash
# Rebase interactive (clean up commits before PR)
git rebase -i HEAD~3
# squash, reword, fixup commits

# Cherry-pick specific commit
git cherry-pick <commit-hash>

# Bisect (find bug-introducing commit)
git bisect start
git bisect bad              # Current is broken
git bisect good <old-hash>  # This was working
# Git helps find the breaking commit

# Blame (who wrote this line?)
git blame models/golden/dim_customer.sql
```

### Git Hooks & Automation
```bash
# Pre-commit hooks (quality gates)
# .pre-commit-config.yaml
repos:
  - repo: https://github.com/sqlfluff/sqlfluff
    hooks:
      - id: sqlfluff-lint
        args: [--dialect, redshift]
  - repo: https://github.com/pre-commit/pre-commit-hooks
    hooks:
      - id: trailing-whitespace
      - id: end-of-file-fixer
```

### CI/CD Integration
```yaml
# .gitlab-ci.yml
stages:
  - lint
  - test
  - deploy

lint:
  stage: lint
  script:
    - sqlfluff lint models/ --dialect redshift

test:
  stage: test
  script:
    - dbt test --target dev

deploy:
  stage: deploy
  script:
    - dbt run --target dev
  only:
    - main
```

### Advanced Workflows
```bash
# Git flow
main → develop → feature/* → PR → develop → release/* → main

# Trunk-based (recommended for dbt)
main → feature/* → PR → main (with CI gates)

# Monorepo patterns
# Use --filter for partial clone
git clone --filter=blob:none <url>
```

### Bài tập
1. Set up pre-commit hooks (sqlfluff + yaml lint)
2. Practice interactive rebase: squash 5 commits into 2
3. Write CI pipeline (.gitlab-ci.yml) for dbt project
4. Set up branch protection rules

### Resources
| Resource | Link | Type |
|---|---|---|
| Pre-commit framework | [pre-commit.com](https://pre-commit.com/) | Free |
| GitHub Actions docs | [docs.github.com/actions](https://docs.github.com/en/actions) | Free |
| GitLab CI/CD docs | [docs.gitlab.com/ee/ci](https://docs.gitlab.com/ee/ci/) | Free |
| Conventional Commits | [conventionalcommits.org](https://www.conventionalcommits.org/) | Free |

---
## Alternatives & Công cụ tương đương

| Tool | Description | When to use |
|---|---|---|
| Git (CLI) | Standard, most flexible | Daily work |
| GitHub Desktop | GUI client | Beginners, visual preference |
| GitKraken | Advanced GUI | Complex branching |
| VS Code Git | Integrated | Already in VS Code |
| **Platforms:** | | |
| GitHub | Most popular, open source | Public repos, community |
| GitLab | CI/CD built-in, self-hosted | Enterprise, private |
| Bitbucket | Atlassian ecosystem | Jira integration |
| Azure DevOps | Microsoft ecosystem | Enterprise .NET/Azure |
