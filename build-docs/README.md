# HTML-Dokumentation aus Markdown erzeugen

**Build MkDocs Documentation** ist eine schlanke Composite GitHub Action zum **Erzeugen statischer HTML-Dokumentation** auf Basis von Markdown-Dateien mit **MkDocs und dem Material Theme**.
Sie ist bewusst auf den **Build-Schritt** fokussiert und eignet sich als Baustein in mehrstufigen CI-Pipelines, z. B. in Kombination mit separaten Publish- oder Deploy-Actions.

Die Action übernimmt ausschließlich das Setup der Build-Umgebung und die deterministische Erzeugung der HTML-Artefakte.

## Typische Einsatzszenarien

* Generierung von HTML-Dokumentation für Module, Plugins oder Libraries
* Vorbereitung von Artefakten für GitHub Pages oder interne Doku-Repositories
* CI-Pipelines mit klarer Trennung von Build und Deploy
* Einheitliche Dokumentations-Builds über mehrere Projekte hinweg

## Enthaltene Schritte

**1. Python-Setup**

* Installation einer definierten Python-Version (3.11)
* Reproduzierbare Build-Umgebung für MkDocs

---

**2. Installation der Dokumentations-Toolchain**

Installierte Komponenten:

* `mkdocs`
* `mkdocs-material`
* `mkdocs-macros-plugin`
* `mkdocs-include-markdown-plugin`

Damit werden sowohl klassische Material-Dokumentationen als auch modulare, wiederverwendbare Markdown-Strukturen unterstützt.

---

**3. Build der Dokumentation**

* Verwendung einer konfigurierbaren `mkdocs.yml`
* Ausgabe der generierten HTML-Dateien in ein frei wählbares Zielverzeichnis
* Klare, nachvollziehbare Build-Ausgabe im CI-Log
* Kein Server, kein Preview, kein Deploy

## Inputs

| Name            | Required | Default     | Description |
|-----------------|----------|-------------|-------------|
| `docs_dir`      | no       | `docs`      | Directory containing documentation sources |
| `mkdocs_config` | no       | `mkdocs.yml`| Path to MkDocs configuration file |
| `site_name`     | no       | –           | Override `site_name` from config |
| `site_url`      | no       | –           | Override `site_url` from config |
| `output_dir`    | no       | `site`      | Output directory for generated HTML |

## Integrationsbeispiel

```
- name: Build documentation
  uses: d3datadevelopment/ci-actions/build-docs@v1
  with:
    mkdocs_config: docs/mkdocs.yml
    output_dir: ${{ github.workspace }}/documentation
```