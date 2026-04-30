# Git and Version Control Skill

## Overview
Expertise in Git workflow management, repository organization, and collaborative development practices for BI and Data Engineering projects.

## Capabilities

### Repository Management
- Repository initialization and setup
- Remote repository configuration
- Branching strategies (GitFlow, GitHub Flow)
- Tagging and versioning (semantic versioning)
- Repository organization and structure

### Workflow Patterns
- Feature branch workflow
- Pull request / merge request process
- Code review practices
- Conflict resolution
- Stashing and patching

### Collaboration
- Working with remote repositories
- Fork and pull request model
- Submodule management
- Large file handling (Git LFS)
- Integration with CI/CD

### Documentation
- README.md standards
- Markdown formatting
- CHANGELOG maintenance
- Commit message conventions
- Wiki and GitHub Pages

## Workflow Guidelines

### Branching Strategy
```
main        → Production-ready code
├── develop → Integration branch for features
│   ├── feature/sql-optimization
│   ├── feature/snowflake-stored-proc
│   └── feature/powerbi-dashboard
├── hotfix  → Urgent production fixes
└── release → Release preparation
```

### Commit Message Convention
```
Type: Short description (max 50 chars)

Longer explanation if needed (wrap at 72 chars)
- Bullet points for multiple changes
- Reference issues: Fixes #123

Types:
- feat: New feature
- fix: Bug fix
- docs: Documentation changes
- style: Formatting, semicolons, etc.
- refactor: Code restructuring
- test: Adding tests
- chore: Maintenance tasks
```

Example:
```
feat: Add SCD Type 2 merge procedure

Implement MERGE statement for customer dimension
with proper change detection and history tracking.

- Add IS_CURRENT flag handling
- Set END_DATE for closed records
- Insert new records with START_DATE

Fixes #45
```

### Pull Request Template
```markdown
## Description
Brief description of changes

## Type of Change
- [ ] New feature
- [ ] Bug fix
- [ ] Documentation
- [ ] Refactoring

## Testing
- [ ] Unit tests pass
- [ ] Integration tests pass
- [ ] Manual testing completed

## Checklist
- [ ] Code follows style guidelines
- [ ] Documentation updated
- [ ] Commit messages are descriptive
- [ ] No sensitive data committed
```

## Repository Structure

### BI/Data Engineering Project Layout
```
project-root/
├── README.md
├── .gitignore
├── LICENSE
├── docs/
│   ├── architecture.md
│   ├── data-dictionary.md
│   └── runbook.md
├── sql/
│   ├── ddl/
│   ├── dml/
│   └── procedures/
├── python/
│   ├── extract/
│   ├── transform/
│   └── load/
├── powerbi/
│   ├── models/
│   └── reports/
├── config/
│   ├── dev/
│   ├── staging/
│   └── prod/
└── tests/
    ├── unit/
    └── integration/
```

### .gitignore for Data Projects
```gitignore
# Python
__pycache__/
*.py[cod]
*$py.class
*.so
.Python
env/
venv/
.env

# Jupyter
.ipynb_checkpoints

# IDEs
.vscode/
.idea/
*.swp
*.swo

# Data files (don't commit large data)
*.csv
*.parquet
*.xlsx
*.xls
data/
datasets/

# Credentials (NEVER commit these)
*.key
*.pem
config/credentials.yml
.env.local

# OS
.DS_Store
Thumbs.db
```

## Best Practices
1. Commit early and often with meaningful messages
2. Never commit sensitive data (credentials, API keys)
3. Use branches for all new features
4. Review code before merging
5. Keep main branch deployable at all times
6. Tag releases with semantic versions (v1.0.0)
7. Document everything in README files
8. Use .gitignore to exclude unnecessary files

## Common Commands

### Daily Workflow
```bash
# Start new feature
git checkout -b feature/new-analysis

# Make changes, then commit
git add .
git commit -m "feat: Add customer segmentation analysis"

# Push to remote
git push -u origin feature/new-analysis

# Create pull request (via GitHub/GitLab UI)

# After merge, clean up
git checkout main
git pull origin main
git branch -d feature/new-analysis
```

### Advanced Operations
```bash
# Interactive rebase for clean history
git rebase -i HEAD~3

# Stash changes temporarily
git stash
git stash pop

# Cherry-pick specific commits
git cherry-pick abc123

# View history with graph
git log --oneline --graph --all

# Find commits by content
git log -S "search_term"
```

## Business Context
Version control enables:
- Collaboration without conflicts
- Rollback to previous versions
- Audit trail of changes
- Parallel development (branching)
- Code review and quality control
- Documentation of decisions
- Release management

## Integration with AI Workflows
- Use GitHub Copilot or similar for code suggestions
- Store AI prompts in versioned markdown files
- Track AI-assisted code with comments
- Document AI-generated solutions
- Version control knowledge base (like this repo)
