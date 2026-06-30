# Contributing

Contributions are welcome! This learning path improves through community input.

---

## What you can contribute

- **New module** — Learning content for a Kubernetes topic not yet covered
- **New lab** — Hands-on exercise with step-by-step instructions and manifests
- **Exercise** — New troubleshooting scenarios or practice tasks
- **Bug fix** — Incorrect commands, outdated content, broken links
- **Improvement** — Clarity, completeness, better explanations

---

## Quality rules

### General

- Write content that works with a local kind cluster
- Use only official sources: kubernetes.io, CNCF, official tool documentation
- No invented kubectl commands or YAML fields
- All YAML manifests must be syntactically valid
- No secrets, passwords, or tokens — dummy values only in `secret.example.yaml`

### Modules

- Follow the module structure defined in [CLAUDE.md](https://github.com/SvnSchmdt/k8s-learning-path/blob/main/CLAUDE.md)
- Include at minimum: goal, key concepts, practical task, checkpoint, definition of done
- Checkpoint must have at least 3 verifiable questions

### Labs

- Follow the lab structure defined in [CLAUDE.md](https://github.com/SvnSchmdt/k8s-learning-path/blob/main/CLAUDE.md)
- Must include: "Was du baust" section with ASCII diagram, expected output, validation, cleanup
- Must work with a local kind cluster

---

## How to contribute

```bash
# 1. Fork and clone
git clone https://github.com/YOUR-USERNAME/k8s-learning-path.git
cd k8s-learning-path

# 2. Create a branch
git checkout -b add/module-16-advanced-scheduling

# 3. Make your changes
# Follow the templates in CLAUDE.md

# 4. Validate YAML
kubectl apply --dry-run=client -f docs/labs/XX-new-lab/manifests/

# 5. Test MkDocs build
pip install -r requirements.txt
mkdocs build --strict

# 6. Add new pages to mkdocs.yml nav

# 7. Commit and push
git add .
git commit -m "add: Module 16 - Advanced Scheduling"
git push origin add/module-16-advanced-scheduling
```

---

## PR checklist

- [ ] No secret, password, or token committed
- [ ] All new YAML manifests validated (`--dry-run=client`)
- [ ] No invented commands or API fields
- [ ] New pages added to `mkdocs.yml` nav
- [ ] `mkdocs build --strict` passes without errors
- [ ] Module/Lab template followed
- [ ] Cleanup steps present (for labs)

---

## Commit message conventions

| Prefix | Use for |
|--------|---------|
| `add:` | New content |
| `fix:` | Bug fix |
| `update:` | Content update |
| `docs:` | Documentation only |

---

For the full German-language contribution guide, see [CONTRIBUTING.md](https://github.com/SvnSchmdt/k8s-learning-path/blob/main/CONTRIBUTING.md) in the repository root.
