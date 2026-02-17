# Budding Q-notation to a Separate Repository

This guide is for maintainers who want to extract the Q-notation module into its own independent repository.

---

## Why Bud Off?

Q-notation has been designed as a **modular dependency** of the Agentic Work Week (AWW) framework, but it can evolve independently. Reasons to create a separate repository:

1. **Independent evolution**: Q-notation can be versioned and released separately
2. **Broader applications**: Use Q-notation beyond AWW contexts
3. **Community focus**: Dedicated community around notation theory
4. **Tool development**: Easier to build parsers, analyzers, compilers
5. **Academic work**: Separate citation and research focus

---

## Current Structure (Ready to Bud)

The `q-notation/` directory is **self-contained** and includes:

```
q-notation/
├── README.md          # Complete overview and documentation
├── THEORY.md          # Theoretical foundations (Hofstadter, Levenshtein, etc.)
├── EXAMPLES.md        # Practical applications and code samples
├── QUICKREF.md        # One-page quick reference guide
├── CONTRIBUTING.md    # Contribution guidelines
├── CHANGELOG.md       # Version history
└── LICENSE            # MIT license (independent)
```

**Total**: ~2,400 lines of comprehensive documentation

---

## Budding Process

### Step 1: Create New Repository

```bash
# On GitHub
# Create new repository: wordgarden-dev/q-notation
# Description: "Phonetic automation quantification - encoding automation levels through C/K → Q transformations"
# Initialize: Empty (no README, license, or .gitignore)
```

### Step 2: Extract Q-notation Directory

```bash
# Clone AWW repo
git clone https://github.com/wordgarden-dev/agentic-work-week.git
cd agentic-work-week

# Use git filter-branch or git subtree to extract q-notation/
git subtree split --prefix=q-notation -b q-notation-standalone

# Create new repo and push
cd ..
mkdir q-notation
cd q-notation
git init
git pull ../agentic-work-week q-notation-standalone
git remote add origin https://github.com/wordgarden-dev/q-notation.git
git branch -M main
git push -u origin main
```

**Alternative (simpler)**:
```bash
# Just copy the directory
cp -r agentic-work-week/q-notation/ q-notation-new-repo/
cd q-notation-new-repo
git init
git add .
git commit -m "Initial commit: Q-notation v0.1.0 extracted from AWW"
git remote add origin https://github.com/wordgarden-dev/q-notation.git
git push -u origin main
```

### Step 3: Update README.md in New Repo

Move content from `q-notation/README.md` to root `README.md`, adjusting links:

```markdown
# Before (in q-notation/README.md)
See [THEORY.md](THEORY.md)
Parent: [Agentic Work Week](../)

# After (in new repo root)
See [THEORY.md](THEORY.md)
Related: [Agentic Work Week](https://github.com/wordgarden-dev/agentic-work-week)
```

### Step 4: Add Repository Metadata

Create `.github/` directory with:

```
.github/
├── ISSUE_TEMPLATE/
│   ├── bug_report.md
│   ├── feature_request.md
│   └── question.md
├── PULL_REQUEST_TEMPLATE.md
└── workflows/
    └── ci.yml  (if adding tooling)
```

### Step 5: Update AWW Repository

In the original AWW repo, update references:

```markdown
# README.md
- **Q-notation**: Phonetic automation quantification system
  - **Repository**: https://github.com/wordgarden-dev/q-notation
  - **Documentation**: https://github.com/wordgarden-dev/q-notation#readme
  - **Version**: v0.1.0+

# Keep a symlink or remove q-notation/ directory
# Option A: Remove and link externally
rm -rf q-notation/
# Update all references to link to new repo

# Option B: Keep as git submodule
git submodule add https://github.com/wordgarden-dev/q-notation.git q-notation
```

---

## Post-Budding Checklist

### New Q-notation Repository

- [ ] All markdown files at root level
- [ ] Update all internal links (remove `../` references to AWW)
- [ ] Add GitHub repository metadata (.github/)
- [ ] Create initial release (v0.1.0)
- [ ] Set up GitHub Pages (optional, for documentation)
- [ ] Add topics/tags: automation, notation, linguistics, phonetics
- [ ] Configure branch protection (main branch)
- [ ] Add collaborators/maintainers
- [ ] Update description and README badges
- [ ] Link to AWW as "related project"

### Original AWW Repository

- [ ] Update README to link to new Q-notation repo
- [ ] Remove q-notation/ directory OR convert to submodule
- [ ] Update FICTION.md and other docs with new links
- [ ] Create release note about Q-notation budding
- [ ] Update CHANGELOG with extraction event
- [ ] Mention in future AWW documentation

### Cross-Repository

- [ ] Ensure bidirectional links work
- [ ] Coordinate versioning (Q-notation v0.x.y, AWW v0.x.y)
- [ ] Align on shared terminology
- [ ] Cross-reference in documentation
- [ ] Consider shared GitHub organization/team

---

## Version Management

### Q-notation Repository
- **Independent versioning**: v0.1.0, v0.2.0, etc.
- **Semantic versioning**: Major.Minor.Patch
- **Changelog**: Track Q-notation evolution independently

### AWW Repository
- **Dependency versioning**: Reference Q-notation v0.x.y
- **Update as needed**: Pin to stable Q-notation releases
- **Co-evolution**: Q-notation changes may inform AWW updates

---

## Communication

### Announce the Budding

```markdown
# In AWW CHANGELOG.md
## [0.2.0] - 2026-XX-XX
### Changed
- Q-notation extracted to independent repository
- See https://github.com/wordgarden-dev/q-notation
- AWW now references Q-notation v0.1.0 as external dependency

# In Q-notation CHANGELOG.md (new repo)
## [0.1.0] - 2026-02-17
### Added
- Initial release as independent repository
- Extracted from Agentic Work Week framework
- Complete documentation suite (README, THEORY, EXAMPLES, etc.)
```

### Cross-Promotion

- Link AWW in Q-notation README as "Primary Use Case"
- Link Q-notation in AWW README as "Core Dependency"
- Mention in README badges, shields.io
- Cross-reference in blog posts, talks, papers

---

## Maintaining the Relationship

### Q-notation → AWW
- Q-notation is **general-purpose** (not AWW-specific)
- AWW is one application of Q-notation
- Changes to Q-notation core don't require AWW coordination
- AWW can adopt new Q-notation versions at its own pace

### AWW → Q-notation
- AWW may propose Q-notation extensions
- AWW-specific applications stay in AWW repo
- Domain-agnostic patterns can be upstreamed to Q-notation
- Examples from AWW can be added to Q-notation/EXAMPLES.md

---

## Future: Package Distribution

### npm Package (JavaScript/TypeScript)
```bash
# In q-notation repo
npm init
# Create package.json
# Publish: npm publish @wordgarden-dev/q-notation
```

### PyPI Package (Python)
```bash
# In q-notation repo
# Create setup.py or pyproject.toml
# Publish: python -m build && twine upload dist/*
```

### Go Module
```bash
# In q-notation repo
go mod init github.com/wordgarden-dev/q-notation
# Publish: git tag v0.1.0 && git push --tags
```

---

## Conclusion

Q-notation is **ready to bud** into an independent repository. The current structure in the AWW repo is self-contained and can be extracted with minimal changes.

**Recommendation**: 
1. Complete AWW v0.2.0 milestone
2. Extract Q-notation to separate repo
3. Release Q-notation v0.1.0 officially
4. Update AWW to reference external Q-notation
5. Co-evolve both projects

---

**Prepared by**: Agent refactoring task  
**Date**: 2026-02-17  
**Status**: Ready for budding when maintainers choose
