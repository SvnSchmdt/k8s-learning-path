# Kubernetes Learning Path

A practical, step-by-step Kubernetes learning path — from your first Pod to GitOps and production readiness.

The full documentation lives in [`docs/`](docs/) and is published as a MkDocs website.

## Website

**[https://svnschmdt.github.io/k8s-learning-path/](https://svnschmdt.github.io/k8s-learning-path/)**

## Run locally

```bash
pip install -r requirements.txt
mkdocs serve
```

Then open: [http://127.0.0.1:8000](http://127.0.0.1:8000)

## Content

| Area | Description |
|------|-------------|
| [docs/roadmap/](docs/roadmap/) | Phase-by-phase learning plan and checkpoints |
| [docs/modules/](docs/modules/) | 16 structured learning modules (00–15) |
| [docs/labs/](docs/labs/) | 8 hands-on labs with kind, kubectl, and Helm |
| [docs/exercises/](docs/exercises/) | Beginner, intermediate, and troubleshooting exercises |
| [docs/resources/](docs/resources/) | Glossary, cheat sheet, and official documentation links |
| [docs/examples/](docs/examples/) | Ready-to-use YAML manifests |

## Local cluster quickstart

```bash
# Install prerequisites (macOS)
brew install kind kubectl helm

# Create cluster
kind create cluster --name k8s-learning

# Verify
kubectl get nodes
```

See [Lab 00](docs/labs/00-local-cluster-kind/README.md) for the full walkthrough.

## Built with

- [MkDocs](https://www.mkdocs.org/)
- [Material for MkDocs](https://squidfunk.github.io/mkdocs-material/)
- [GitHub Pages](https://pages.github.com/)

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines on how to add modules, labs, or fix issues.
