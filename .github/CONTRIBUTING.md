# Contributing

Thank you for your interest in contributing to `ui-restructure`.

## Rules

1. **No direct commits to `main`.** All changes must come through a Pull Request.
2. **All PRs require review** from the code owner before merging.
3. **Commits must be signed.** Configure GPG or SSH signing before contributing.
4. **Squash merge only.** Keep history clean and linear.
5. **One concern per PR.** Don't bundle unrelated changes.

## How to contribute

```bash
# 1. Fork the repo
# 2. Create a branch from main
git checkout -b feat/your-feature-name

# 3. Make changes
# 4. Validate the skill before opening a PR
npx agent-skills-cli validate ./ui-restructure

# 5. Push and open a PR against main
git push origin feat/your-feature-name
```

## Branch naming

| Type | Pattern | Example |
|---|---|---|
| Feature | `feat/...` | `feat/add-figma-engine` |
| Bug fix | `fix/...` | `fix/linear-spacing` |
| Documentation | `docs/...` | `docs/update-readme` |
| Security | `security/...` | `security/patch-cve` |

## Commit format

```
type: short description (max 72 chars)

Optional longer body.
```

Types: `feat`, `fix`, `docs`, `refactor`, `security`, `chore`
