# D3 CI Composite Actions

Zentrale Sammlung wiederverwendbarer GitHub Composite Actions

Ziel dieses Repositories ist es, CI-Logik zu standardisieren, Copy-Paste zu vermeiden und Workflows in Projekt-Repositories schlank zu halten.

## Grundprinzipien

Dieses Repository enthält nur Actions, keine Projekt-Pipelines. Die Actions sind bewusst klein, fokussiert und generisch konzipiert. Infrastruktur, 
Secrets und Matrix-Logik sollen bitte im verwendenden Projekt definiert werden. Jeder Ordner enthält genau eine Composite Action.

## Versionierung

Dieses Repository verwendet semantische Versionierung über Git-Tags.

Empfohlene Nutzung in Projekten:

```
uses: d3datadevelopment/ci-documentation-actions/<action-name>@v1
```

`v1` zeigt immer auf die aktuelle stabile Version der Major-Reihe. In `rel_X.x` befindet sich die aktuellste Pre-Release Version.

## Enthaltene Actions

**Build MkDocs Documentation** ist eine schlanke Composite GitHub Action zum **Erzeugen statischer HTML-Dokumentation** auf Basis von Markdown-Dateien mit **MkDocs und dem Material Theme**.
Sie ist bewusst auf den **Build-Schritt** fokussiert und eignet sich als Baustein in mehrstufigen CI-Pipelines, z. B. in Kombination mit separaten Publish- oder Deploy-Actions.

Weitere Infos [hier](./build-docs/README.md).

### publish-docs - versionierte Dokumentation aus CI heraus deployen

**Publish Documentation** ist eine Composite GitHub Action zur **kontrollierten Veröffentlichung versionierter Dokumentation** in ein separates GitHub-Pages-Repository.
Sie ist dafür gedacht, **bereits generierte Dokumentation** (z. B. aus MkDocs, Docusaurus, Sphinx, Custom-Generatoren) aus einem CI-Workflow heraus **strukturiert, reproduzierbar und versionssicher** zu publizieren.

Weitere Infos [hier](./publish-docs/README.md).

## Beispiele

Schaue in den [Beispiele-Ordner](./examples) für Integrationsbeispiele.
