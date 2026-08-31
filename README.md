![OpenShift Documentation](https://github.com/Sopra-Steria-Norge-Kubernetes/OpenShift/actions/workflows/ci.yml/badge.svg)

# OpenShift
User documentation for OpenShift delivery by Sopra Steria.

## Documentation development
See [How to use Zensical](https://github.com/Sopra-Steria-Norge-Kubernetes/OpenShift/blob/main/docs/about/how-to-use-zensical.md) for guidance on writing and publishing documentation.

### Using `uv`

The easiest way to develop the documentation locally is with [uv](https://docs.astral.sh/uv/):

```shell
brew install uv
```

Set up Zensical and start the local preview:

```shell
uv venv
uv pip install zensical==0.0.57
uv run zensical serve
```

The preview is available at <http://127.0.0.1:8000/>.
