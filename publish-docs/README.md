# Versionierte Dokumentation aus CI heraus veröffentlichen

**Publish Documentation** ist eine Composite GitHub Action zur kontrollierten Übergabe versionierter Dokumentation an einen zentralen Dokumentations-Workflow.

Die Action übernimmt **keine Generierung**, sondern ausschließlich:

- Validierung der Eingaben
- Normalisierung der Versionsnummer
- Upload der generierten Dokumentation als Artifact
- Auslösen eines zentralen `repository_dispatch` Events

Die eigentliche Veröffentlichung (Server-Deployment, Übersichtsseiten-Generierung etc.) erfolgt im zentralen Docs-Repository.

---

## Architekturüberblick

```text
Modul-Repository
   │
   │ mkdocs build
   │
   ▼
Publish-Docs Action
   │
   │ upload-artifact
   │ repository_dispatch
   ▼
Zentrales Docs-Repository
   │
   │ Artifact herunterladen
   │ Server-Deployment
   ▼
Server
```

Die Action ist damit bewusst ein **reiner Übergabemechanismus** zwischen Modul-Workflow und zentraler Orchestrierung.

---

## Typische Einsatzszenarien

- Versionierte Modul- oder Plugin-Dokumentation
- Mehrere Projekte mit zentraler Doku-Instanz
- Automatisches Publizieren bei Release-Tags
- Strikte Trennung zwischen Modul-Repo und Infrastruktur
- Reproduzierbare Doku-Veröffentlichung ohne Artefakt-Commits

---

## Enthaltene Schritte

### 1. Initialisierung & Validierung

- Ausgabe aller relevanten Parameter
- Strikte Prüfung der Pflichtfelder
- Validierung von:
  - `plugin_id` (ordnerfreundliche Zeichen)
  - `version` (keine Leerzeichen oder Slashes)
  - `source_dir` (muss existieren)
- Frühes Abbrechen bei Konfigurationsfehlern

---

### 2. Versionsnormalisierung

Die übergebene Versionsnummer wird intern normalisiert:

- optionales führendes `v` wird entfernt
- 3-Level-Versionen (`1.2.3`) werden direkt übernommen
- 4-Level-Versionen (`1.2.3.400`) werden organisationskonform reduziert

Ungültige Formate führen zu einem Abbruch des Workflows.

Die normalisierte Version wird für:

- Artifact-Namen
- Dispatch-Payload

verwendet.

---

### 3. Artifact-Upload

Die generierte Dokumentation wird als GitHub Artifact hochgeladen.

Artifact-Name:

```
module-docs-{normalized_version}
```

Inhalt:

```
{source_dir}/
```

Die Action selbst führt keinen Server-Upload durch.

---

### 4. Triggern des zentralen Docs-Repositories

Per `repository_dispatch` wird das zentrale Docs-Repository informiert.

Übertragene Payload:

```json
{
  "plugin_id": "...",
  "module_version": "...",
  "artifact_name": "...",
  "source_repo": "...",
  "source_run_id": "..."
}
```

Das zentrale Repository übernimmt:

- Download des Artifacts
- Deployment auf den Zielserver
- vollständige Neugenerierung der Übersichtsseiten

---

## Inputs

| Name                  | Pflicht | Beschreibung                                         |
|-----------------------|---------|------------------------------------------------------|
| `plugin_id`           | ja      | Eindeutige Plugin-ID (wird als Ordnername verwendet) |
| `version`             | ja      | Versionskennung der Dokumentation                    |
| `source_dir`          | ja      | Pfad zur bereits generierten Dokumentation           |
| `dispatch_repository` | ja      | Ziel-Repository (z. B. `org/docs-repo`)              |
| `dispatch_token`      | ja      | Token mit Schreibrechten für `repository_dispatch`   |

---

## Berechtigungen

Das übergebene `dispatch_token` benötigt:

- `contents: read`
- `contents: write`

Empfohlen wird ein Fine-grained Personal Access Token mit Zugriff ausschließlich auf das zentrale Docs-Repository.

---

## Integrationsbeispiel

```yaml
- name: Publish documentation
  uses: d3datadevelopment/ci-documentation-actions/publish-docs@v1.0.0
  with:
    plugin_id: myplugin
    version: ${{ github.ref_name }}
    source_dir: documentation
    dispatch_repository: ${{ vars.DOCS_REPOSITORY }}
    dispatch_token: ${{ secrets.DOCS_DISPATCH_TOKEN }}
```

---

## Designprinzipien

- Keine direkte Serverkommunikation aus Modul-Repositories
- Keine Artefakt-Commits in Git
- Keine GitHub Pages Abhängigkeit
- Zentrale Orchestrierung im Docs-Repository
- Klare Trennung zwischen Generierung und Veröffentlichung

---

## Abgrenzung

Diese Action:

- baut keine Dokumentation
- deployt nichts direkt
- verwaltet keine Server-Zugänge

Sie ist ausschließlich der standardisierte Übergabemechanismus in einer mehrstufigen Doku-Architektur.
