# How to write documentation with Zensical

This project uses [Zensical](https://zensical.org/docs/get-started/) to build and preview its documentation site.

## Prerequisites

- [uv](https://docs.astral.sh/uv/getting-started/installation/) installed.
- Git access to this repository.

## Preview the site locally

Clone the repository, install the Zensical version used by continuous integration, and build the site:

```bash
git clone https://github.com/Sopra-Steria-Norge-Kubernetes/OpenShift.git
cd OpenShift
uv venv
uv pip install zensical==0.0.57
uv run zensical build --clean
```

The build should finish with `No issues found`.

Start the live preview:

```bash
uv run zensical serve
```

Open <http://127.0.0.1:8000/>. Zensical rebuilds the site when you save a documentation or configuration change.

## Documentation structure

Documentation source files are Markdown files under `docs/`. Images and other static assets are also stored there. The site configuration and navigation are defined in the root [mkdocs.yml](../../mkdocs.yml), which Zensical supports as a compatibility configuration.

## Add or update a page

1. Create or edit a Markdown file in the relevant `docs/` subdirectory.
2. Add the page to the appropriate `nav` section in [mkdocs.yml](../../mkdocs.yml).
3. Run `uv run zensical build --clean` and resolve any reported issues.
4. Use the local preview to review the rendered page.

For example, add a page beneath an existing section:

```yaml
nav:
  - OpenShift Tenants:
      - New page: OpenShift Tenants/new-page.md
```

Use relative links to Markdown files and assets so Zensical can validate them during the build.

## Publish changes

Work on a branch and open a pull request to `main`. When the pull request is merged, [the documentation workflow](../../.github/workflows/ci.yml) builds the site with Zensical and deploys it to GitHub Pages.

The published documentation is available at <https://sopra-steria-norge-kubernetes.github.io/OpenShift/>.
