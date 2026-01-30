# Versionierte Dokumentation aus CI heraus deployen

**Publish Documentation** ist eine Composite GitHub Action zur **kontrollierten Veröffentlichung versionierter Dokumentation** in ein separates Repository.
Sie ist dafür gedacht, **bereits generierte Dokumentation** (z. B. aus MkDocs, Docusaurus, Sphinx, Custom-Generatoren) aus einem CI-Workflow heraus **strukturiert, reproduzierbar und versionssicher** zu publizieren.

Die Action übernimmt bewusst **keine Generierung**, sondern ausschließlich das **Verteilen, Versionieren und Committen** der fertigen Artefakte.

### Typische Einsatzszenarien

* Versionierte Modul- oder Plugin-Dokumentation
* Zentrale Doku-Repositories für mehrere Kundenprojekte
* Automatisches Publizieren bei Tags oder Releases
* Ersatz für manuelle GitHub-Pages-Pflege
* Kombination mit MkDocs, Material, Docusaurus oder Custom-Generatoren

## Zielstruktur im Pages-Repository

Die Dokumentation wird nach folgendem Schema abgelegt:

```
<target_base_path>/
└── <plugin_id>/
    └── <version>/
        └── ... Doku-Inhalte ...
```

Damit lassen sich mehrere Plugins/Module sowie mehrere Versionen parallel betreiben, ohne sich gegenseitig zu überschreiben.

Jede Version ist ein vollständiger Snapshot.
Bestehende Versionen werden bei erneutem Deploy **komplett ersetzt**.

## Enthaltene Schritte

**1. Initialisierung & Validierung**

* Explizite Ausgabe aller relevanten Parameter
* Strikte Validierung:

  * Pflichtfelder müssen gesetzt sein
  * `plugin_id` nur mit ordnerfreundlichen Zeichen
  * `version` ohne Leerzeichen oder Slashes
  * `source_dir` muss existieren
* Frühes, sauberes Abbrechen bei Konfigurationsfehlern

---

**2. Checkout des Pages-Repositories**

* Checkout eines beliebigen Repositories und Branches
* Authentifizierung über explizites Access Token
* Keine Annahmen über Default-Repos oder Organisationsstruktur

---

**3. Veröffentlichung der Dokumentation**

* Zielverzeichnis wird deterministisch berechnet
* Bestehende Versionen werden **vollständig ersetzt**
* Rekursives Kopieren der erzeugten Doku-Artefakte
* Commit nur bei tatsächlichen Änderungen
* Klar strukturierte Commit-Nachrichten:

  ```
  docs(<plugin_id>): publish <version>
  ```

## Inputs

| Name               | Pflicht | Beschreibung                           |
| ------------------ | ------- | -------------------------------------- |
| `plugin_id`        | ja      | Eindeutige Plugin-ID (wird Ordnername) |
| `version`          | ja      | Versionskennung der Dokumentation      |
| `source_dir`       | ja      | Pfad zur generierten Dokumentation     |
| `docs_repo`        | ja      | Ziel-Repository (GitHub Pages)         |
| `docs_branch`      | nein    | Ziel-Branch (Default: `main`)          |
| `target_base_path` | nein    | Basisverzeichnis im Docs-Repository    |
| `access_token`     | ja      | Access Token mit Schreibrechten        |

## Berechtigungen

Das übergebene `access_token` benötigt Schreibrechte (`contents: write`) auf dem Docs-Repository.

## Integrationsbeispiel

```yaml
- name: Publish documentation
  uses: d3datadevelopment/ci-actions/publish-docs@v1
  with:
    plugin_id: myplugin
    version: ${{ github.ref_name }}
    source_dir: documentation
    docs_repo: https://www.github.com/myOrga/myRepo.git
    docs_branch: main
    target_base_path: .
    access_token: ${{ secrets.DOCS_REPO_TOKEN }}
```