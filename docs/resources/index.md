# Resources

Reference material to support your learning throughout the path. Keep these open while working through labs and modules.

---

## Available resources

| Resource | Description |
|----------|-------------|
| [Glossary](glossary.md) | Definitions for 40+ Kubernetes terms |
| [Cheat Sheet](cheat-sheet.md) | kubectl commands by category, including the 10-step troubleshooting workflow |
| [Official Docs](official-docs.md) | Curated links to official Kubernetes documentation, tools, and certifications |

---

## Quick links

**When you are stuck on a command:**
→ [Cheat Sheet](cheat-sheet.md)

**When you encounter an unfamiliar term:**
→ [Glossary](glossary.md)

**When you want to go deeper:**
→ [Official Docs](official-docs.md)

---

## GitHub Pages deployment

This repository uses GitHub Actions to deploy the MkDocs website to GitHub Pages.

In GitHub, make sure the following setting is configured:

**Settings → Pages → Build and deployment → Source → GitHub Actions**

Once configured, every push to `main` will trigger a new deployment to:

```
https://svnschmdt.github.io/k8s-learning-path/
```
