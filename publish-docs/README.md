# Publish Docs to GitHub Pages

Diese **GitHub Action** veröffentlicht **generierte Dokumentationen** versioniert in einem separaten **GitHub-Pages-Repository**.

Typischer Anwendungsfall:
Plugins oder Libraries erzeugen ihre Dokumentation in einer CI (z. B. aus Markdown). Diese Action übernimmt das **strukturierte Ablegen**, **Ersetzen** und **Veröffentlichen** der Inhalte.

---

## Features

* Versionierte Ablage pro Plugin
* Vollständiger Replace bei Neuveröffentlichung
* Idempotent (gleicher Input → gleiches Ergebnis)
* Fail-fast Input-Validierung
* Keine Node- oder Build-Abhängigkeiten
* Transparent & debug-freundlich (reines Bash)

---

## Zielstruktur im Docs-Repository

```
/
├─ index.html
├─ plugin-a/
│  ├─ index.html
│  ├─ 1.0.0/
│  └─ 1.0.1/
├─ plugin-b/
│  ├─ index.html
│  └─ 2.3.0/
```

Jede Version ist ein vollständiger Snapshot.
Bestehende Versionen werden bei erneutem Deploy **komplett ersetzt**.

---

## Inputs

| Name               | Pflicht | Beschreibung                           |
| ------------------ | ------- | -------------------------------------- |
| `plugin_id`        | ja      | Eindeutige Plugin-ID (wird Ordnername) |
| `version`          | ja      | Versionskennung der Dokumentation      |
| `source_dir`       | ja      | Pfad zur generierten Dokumentation     |
| `docs_repo`        | ja      | Ziel-Repository (GitHub Pages)         |
| `docs_branch`      | nein    | Ziel-Branch (Default: `main`)          |
| `target_base_path` | nein    | Basisverzeichnis im Docs-Repository    |
| `github_token`     | ja      | Access Token mit Schreibrechten        |

---

## Berechtigungen

Das übergebene `github_token` benötigt Schreibrechte (`contents: write`) auf dem Docs-Repository.

Hinweis:
Der automatische `GITHUB_TOKEN` reicht oft nicht aus, wenn das Ziel-Repository ein anderes ist.
In diesem Fall muss ein Personal Access Token (PAT) verwendet werden.

---

## Input-Validierung

Die Action prüft unter anderem:

* Pflichtinputs sind nicht leer
* `source_dir` existiert
* `plugin_id` enthält nur ordnerfreundliche Zeichen
* `version` enthält keine Leerzeichen oder Slashes

Bei einem Fehler bricht die Action sofort ab.

---

## Beispiel: Verwendung im Workflow

```yaml
- name: Publish documentation
  uses: ./.github/actions/publish-docs-to-pages
  with:
    plugin_id: oxid-paypal
    version: 1.0.1
    source_dir: build/docs
    docs_repo: my-org/plugin-docs
    docs_branch: main
    target_base_path: .
    github_token: ${{ secrets.DOCS_REPO_TOKEN }}
```

---

## Interner Ablauf (Kurzfassung)

1. Checkout des Docs-Repositories
2. Ausgabe der übergebenen Inputs (Debug)
3. Validierung aller Parameter
4. Entfernen der bestehenden Version (falls vorhanden)
5. Kopieren der neuen Dokumentation
6. Commit & Push (nur bei Änderungen)
