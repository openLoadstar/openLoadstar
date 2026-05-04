> 🌐 **English** | **[한국어](CONTRIBUTING.ko.md)**

# Contributing to openLoadstar

Thank you for considering a contribution to openLoadstar. This document outlines the general procedures and conventions for effective collaboration.

---

## 🧭 Before You Start

openLoadstar consists of five repositories. Pick the one that matches your contribution.

| Repository | What contributions go here |
|:---|:---|
| [spec](https://github.com/openLoadstar/spec) | Changes to the LOADSTAR methodology, element formats, address convention, and other specifications |
| [cli](https://github.com/openLoadstar/cli) | New features and bug fixes for the Go-based CLI |
| [ui](https://github.com/openLoadstar/ui) | Improvements to the Spring Boot + React web UI |
| [mcp](https://github.com/openLoadstar/mcp) | Python MCP server work, external AI client integration |
| [openLoadstar](https://github.com/openLoadstar/openLoadstar) | Unified README, documentation, and ecosystem-wide guidance |

> Not sure where it belongs? Ask first in [openLoadstar Discussions](https://github.com/openLoadstar/openLoadstar/discussions).

---

## 🤝 How to Contribute

### 🐛 Bug reports

Open an **Issue** in the relevant repo with:

- The environment in which the bug occurred (OS, component version or commit hash)
- Reproduction steps (a minimal example if possible)
- Expected vs. actual behavior
- Relevant logs or error messages

### 💡 Feature proposals

Small improvements: open an Issue. **Larger changes (new APIs, SPEC changes, etc.) — start a thread in Discussions first.** Reaching consensus there makes the PR much more likely to merge.

### 🔧 Code contributions

1. **Fork** the repository.
2. Create a **topic branch** off `main` (e.g. `feat/show-recent-flag`, `fix/validate-broken-ref`).
3. Add or update **tests** that match your changes.
4. Run the tests locally and confirm they pass.
5. Open a **PR** describing your intent, how to test it, and any related Issues.
6. After CI passes and a maintainer reviews it, the PR will be merged.

> ⚠️ Don't open PRs directly against `main`. The standard flow is fork → topic branch.

---

## 🛠️ Development Setup

For each component's build steps and dependencies, see the README in that repo.

| Component | Required tools |
|:---|:---|
| spec | A markdown editor |
| cli | Go 1.21+ |
| ui | JDK 17+, Node 18+, Maven, npm |
| mcp | Python 3.10+, pip or uv |

Common requirements:
- Git
- A GitHub account with an SSH key or PAT

---

## 📝 Commit Message Convention

We recommend the **[Conventional Commits](https://www.conventionalcommits.org/)** format.

```
<type>(<scope>): <subject>

<body>
```

### Frequently used types

| Type | Meaning |
|:---|:---|
| `feat` | New feature |
| `fix` | Bug fix |
| `docs` | Documentation changes (README, comments) |
| `refactor` | Refactoring (no behavior change) |
| `test` | Test additions or fixes |
| `chore` | Build, configuration, miscellaneous |

### Examples

```
feat(cli): add --recent flag to show command
fix(ui): prevent crash when WayPoint has no CHILDREN
docs: clarify CODE_MAP scope rule in spec
chore: bump golangci-lint to 1.55
```

- Keep the subject under 50 characters; English or Korean both fine.
- Use the body to explain **why** (the code already shows the **what**).
- For breaking changes, note them in the footer (`BREAKING CHANGE:`).

---

## 🎨 Code Style

Follow each component's automatic formatter and linter. Keep warnings at zero before opening a PR.

| Component | Tools |
|:---|:---|
| cli (Go) | `gofmt`, `go vet`, `golangci-lint` |
| ui backend (Java) | `mvn spotless:apply` (where present), `mvn verify` |
| ui frontend (TS/React) | `npm run lint`, `npm run format` (Prettier) |
| mcp (Python) | `ruff`, `black` |
| Markdown | ~80 chars per line; keep table alignment (`:---:`) consistent |

---

## ✅ Testing Rules

- **New features ship with tests** — pick whichever fits, unit or integration.
- **Bug fixes ship with regression tests** — to prevent the same bug from coming back.
- A PR cannot merge while existing tests are broken — CI blocks it automatically.

---

## 🔄 PR Checklist

Before opening a PR, please confirm:

- [ ] You worked on a topic branch (no direct edits to `main`)
- [ ] Commit messages follow Conventional Commits
- [ ] You added tests for new features
- [ ] All existing tests pass
- [ ] Zero linter / formatter warnings
- [ ] The PR description explains intent and how to test
- [ ] You referenced the related Issue number (if any)
- [ ] You also updated LOADSTAR metadata (`.loadstar/`) — when changing code

---

## 📜 Updating LOADSTAR Metadata

When contributing code to openLoadstar's own components, **reflect your changes in the metadata too**.

1. Find the target WayPoint (`loadstar show`).
2. Add `- [ ] task description` to the TECH_SPEC (or use an existing item).
3. After the work is done, mark it `- [x] YYYY-MM-DD task description`.
4. If all items in the WP are complete, transition the STATUS `S_PRG → S_STB`.
5. Include the metadata changes in your commit.

For details, see the [Things to Watch Out For](./README.md#️-things-to-watch-out-for) section in the openLoadstar README.

---

## ⚖️ License Agreement

All code contributed to openLoadstar is distributed under the [Apache License 2.0](./LICENSE). By submitting a PR, you agree that your contribution is licensed under those terms.

> 📌 We don't require a separate CLA (Contributor License Agreement); if that policy ever changes, we'll announce it ahead of time.

---

## 🌟 Code of Conduct

The openLoadstar community aims to be a place where every contributor is respected and safe to participate. Discrimination, harassment, and offensive language are not tolerated. If something happens, please let the maintainers know.

---

## 🙏 Thanks

We appreciate every contribution — code, documentation, issues, and ideas alike. openLoadstar grows through community contributions.
