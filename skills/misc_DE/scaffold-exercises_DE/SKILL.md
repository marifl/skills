---
name: scaffold-exercises_DE
description: Übungsverzeichnisstrukturen mit Abschnitten, Aufgaben, Lösungen und Erklärern anlegen, die das Linting bestehen. Verwenden, wenn der Nutzer Übungen aufbauen, Übungs-Stubs erstellen oder einen neuen Kursabschnitt einrichten möchte.
---

# Übungen aufbauen

Übungsverzeichnisstrukturen anlegen, die `pnpm ai-hero-cli internal lint` bestehen, dann mit `git commit` committen.

## Verzeichnisbenennung

- **Abschnitte**: `XX-section-name/` innerhalb von `exercises/` (z. B. `01-retrieval-skill-building`)
- **Übungen**: `XX.YY-exercise-name/` innerhalb eines Abschnitts (z. B. `01.03-retrieval-with-bm25`)
- Abschnittsnummer = `XX`, Übungsnummer = `XX.YY`
- Namen in dash-case (Kleinbuchstaben, Bindestriche)

## Übungsvarianten

Jede Übung benötigt mindestens einen dieser Unterordner:

- `problem/` - Arbeitsbereich des Studenten mit TODOs
- `solution/` - Referenzimplementierung
- `explainer/` - konzeptionelles Material, keine TODOs

Beim Erstellen von Stubs standardmäßig `explainer/` verwenden, es sei denn, der Plan gibt etwas anderes an.

## Erforderliche Dateien

Jeder Unterordner (`problem/`, `solution/`, `explainer/`) benötigt eine `readme.md`, die:

- **Nicht leer** ist (muss echten Inhalt haben, selbst eine einzelne Titelzeile reicht)
- Keine kaputten Links hat

Beim Erstellen von Stubs eine minimale readme mit Titel und Beschreibung anlegen:

```md
# Übungstitel

Beschreibung hier
```

Wenn der Unterordner Code enthält, benötigt er auch eine `main.ts` (>1 Zeile). Für Stubs ist eine readme-only-Übung in Ordnung.

## Workflow

1. **Plan parsen** - Abschnittsnamen, Übungsnamen und Variantentypen extrahieren
2. **Verzeichnisse anlegen** - `mkdir -p` für jeden Pfad
3. **Stub-Readmes anlegen** - eine `readme.md` pro Variantenordner mit Titel
4. **Lint ausführen** - `pnpm ai-hero-cli internal lint` zur Validierung
5. **Fehler beheben** - iterieren, bis Lint besteht

## Lint-Regeln Zusammenfassung

Der Linter (`pnpm ai-hero-cli internal lint`) prüft:

- Jede Übung hat Unterordner (`problem/`, `solution/`, `explainer/`)
- Mindestens ein `problem/`, `explainer/` oder `explainer.1/` existiert
- `readme.md` existiert und ist nicht leer im primären Unterordner
- Keine `.gitkeep`-Dateien
- Keine `speaker-notes.md`-Dateien
- Keine kaputten Links in readmes
- Keine `pnpm run exercise`-Befehle in readmes
- `main.ts` pro Unterordner erforderlich, es sei denn, es ist readme-only

## Übungen verschieben/umbenennen

Beim Umnummerieren oder Verschieben von Übungen:

1. `git mv` verwenden (nicht `mv`) zum Umbenennen von Verzeichnissen - bewahrt die Git-History
2. Das numerische Präfix aktualisieren, um die Reihenfolge zu erhalten
3. Lint nach den Verschiebungen erneut ausführen

Beispiel:

```bash
git mv exercises/01-retrieval/01.03-embeddings exercises/01-retrieval/01.04-embeddings
```

## Beispiel: Stubs aus einem Plan erstellen

Aus einem Plan wie:

```
Section 05: Memory Skill Building
- 05.01 Introduction to Memory
- 05.02 Short-term Memory (explainer + problem + solution)
- 05.03 Long-term Memory
```

Erstellen:

```bash
mkdir -p exercises/05-memory-skill-building/05.01-introduction-to-memory/explainer
mkdir -p exercises/05-memory-skill-building/05.02-short-term-memory/{explainer,problem,solution}
mkdir -p exercises/05-memory-skill-building/05.03-long-term-memory/explainer
```

Dann Readme-Stubs anlegen:

```
exercises/05-memory-skill-building/05.01-introduction-to-memory/explainer/readme.md -> "# Introduction to Memory"
exercises/05-memory-skill-building/05.02-short-term-memory/explainer/readme.md -> "# Short-term Memory"
exercises/05-memory-skill-building/05.02-short-term-memory/problem/readme.md -> "# Short-term Memory"
exercises/05-memory-skill-building/05.02-short-term-memory/solution/readme.md -> "# Short-term Memory"
exercises/05-memory-skill-building/05.03-long-term-memory/explainer/readme.md -> "# Long-term Memory"
```
