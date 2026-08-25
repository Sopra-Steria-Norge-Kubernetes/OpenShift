![OpenShift Documentation](https://github.com/Sopra-Steria-Norge-Kubernetes/OpenShift/actions/workflows/ci.yml/badge.svg)

# OpenShift
User documentation for OpenShift delivery by Sopra Steria.

## mkdocs
Use the folloxing about page for how to use mkdocs: [How to use mkdocs](https://github.com/Sopra-Steria-Norge-Kubernetes/OpenShift/blob/main/docs/about/how-to-use-mkdocs.md)

### Using `uv`

Easiest way to get started developing the docs locally, is by using [uv](https://docs.astral.sh/uv/):

```shell
brew install uv
```

Setup mkdocs:

```shell
uv venv
uv pip install -U mkdocs-material mkdocs-glightbox
uv run mkdocs serve
```
