# Issue-Tracker: Local Markdown

Issues und PRDs für diesen Repo leben als Markdown-Dateien in `.scratch/`.

## Konventionen

- Eine Funktion pro Verzeichnis: `.scratch/<feature-slug>/`
- Das PRD ist `.scratch/<feature-slug>/PRD.md`
- Implementierungs-Issues sind `.scratch/<feature-slug>/issues/<NN>-<slug>.md`, nummeriert ab `01`
- Triage-Zustand wird als `Status:`-Zeile nahe dem Anfang jeder Issue-Datei erfasst (Rollen-Strings siehe `triage-labels.md`)
- Kommentare und Gesprächsverlauf werden am Ende der Datei unter einer `## Comments`-Überschrift angehängt

## Wenn ein Skill sagt „in den Issue-Tracker veröffentlichen"

Eine neue Datei unter `.scratch/<feature-slug>/` anlegen (das Verzeichnis erstellen, falls nötig).

## Wenn ein Skill sagt „das relevante Ticket abrufen"

Die Datei am referenzierten Pfad lesen. Der Nutzer übergibt normalerweise den Pfad oder die Issue-Nummer direkt.
