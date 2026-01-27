# Build MkDocs Documentation

This action builds static HTML documentation from Markdown sources
using **MkDocs with the Material theme**.

## Design Principles

- Documentation lives **inside the plugin repository**
- Generated HTML is a **build artifact**
- This action performs **build only**
- Deployment and versioning are handled by the calling workflow

The action is intentionally minimal and side-effect free.

---

## What This Action Does

- Sets up a deterministic Python environment
- Installs MkDocs and the Material theme
- Runs `mkdocs build`
- Outputs generated HTML to a configurable directory

---

## What This Action Does NOT Do

- No deployment (GitHub Pages, FTP, etc.)
- No commits back to the plugin repository
- No knowledge about plugin names or versions
- No release logic

---

## Inputs

| Name            | Required | Default     | Description |
|-----------------|----------|-------------|-------------|
| `docs_dir`      | no       | `docs`      | Directory containing documentation sources |
| `mkdocs_config` | no       | `mkdocs.yml`| Path to MkDocs configuration file |
| `site_name`     | no       | –           | Override `site_name` from config |
| `site_url`      | no       | –           | Override `site_url` from config |
| `output_dir`    | no       | `site`      | Output directory for generated HTML |

---

## Typical Usage

```yaml
- uses: ./.github/actions/build-mkdocs
  with:
    site_name: Plugin XYZ
    site_url: https://org.github.io/docs/plugin-xyz/latest/
```