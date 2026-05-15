# Out-of-Scope-Wissensbasis

Das `.out-of-scope/`-Verzeichnis in einem Repo speichert dauerhafte Nachweise über abgelehnte Feature-Anfragen. Es dient zwei Zwecken:

1. **Institutionelles Gedächtnis** — warum eine Funktion abgelehnt wurde, damit die Begründung nicht verloren geht, wenn das Issue geschlossen wird
2. **Deduplizierung** — wenn ein neues Issue reinkommt, das einer früheren Ablehnung entspricht, kann der Skill die frühere Entscheidung sichtbar machen, anstatt sie neu zu verhandeln

## Verzeichnisstruktur

```
.out-of-scope/
├── dark-mode.md
├── plugin-system.md
└── graphql-api.md
```

Eine Datei pro **Konzept**, nicht pro Issue. Mehrere Issues, die dasselbe beantragen, werden unter einer Datei gruppiert.

## Dateiformat

Die Datei sollte in einem entspannten, lesbaren Stil verfasst sein — eher wie ein kurzes Design-Dokument als ein Datenbankeinträg. Absätze, Code-Beispiele und Beispiele verwenden, um die Begründung klar und für jemanden nützlich zu machen, der sie zum ersten Mal liest.

```markdown
# Dark Mode

This project does not support dark mode or user-facing theming.

## Why this is out of scope

The rendering pipeline assumes a single color palette defined in
`ThemeConfig`. Supporting multiple themes would require:

- A theme context provider wrapping the entire component tree
- Per-component theme-aware style resolution
- A persistence layer for user theme preferences

This is a significant architectural change that doesn't align with the
project's focus on content authoring. Theming is a concern for downstream
consumers who embed or redistribute the output.

```ts
// The current ThemeConfig interface is not designed for runtime switching:
interface ThemeConfig {
  colors: ColorPalette; // single palette, resolved at build time
  fonts: FontStack;
}
```

## Prior requests

- #42 — "Add dark mode support"
- #87 — "Night theme for accessibility"
- #134 — "Dark theme option"
```

### Die Datei benennen

Einen kurzen, beschreibenden kebab-case-Namen für das Konzept verwenden: `dark-mode.md`, `plugin-system.md`, `graphql-api.md`. Der Name sollte erkennbar genug sein, dass jemand, der das Verzeichnis durchsieht, versteht, was abgelehnt wurde, ohne die Datei öffnen zu müssen.

### Die Begründung schreiben

Die Begründung sollte substanziell sein — nicht „wir wollen das nicht", sondern warum. Gute Begründungen referenzieren:

- Projektscope oder -philosophie („Dieses Projekt fokussiert sich auf X; Theming ist ein nachgelagertes Anliegen")
- Technische Einschränkungen („Das zu unterstützen würde Y erfordern, was mit unserer Z-Architektur in Konflikt steht")
- Strategische Entscheidungen („Wir haben uns für A statt B entschieden, weil...")

Die Begründung sollte dauerhaft sein. Keine temporären Umstände referenzieren („wir haben gerade keine Zeit") — das sind keine echten Ablehnungen, das sind Verschiebungen.

## Wann `.out-of-scope/` prüfen

Während der Triage (Schritt 1: Kontext sammeln) alle Dateien in `.out-of-scope/` lesen. Beim Evaluieren eines neuen Issues:

- Prüfen, ob die Anfrage einem bestehenden Out-of-Scope-Konzept entspricht
- Matching erfolgt nach konzeptioneller Ähnlichkeit, nicht nach Schlüsselwörtern — „night theme" passt zu `dark-mode.md`
- Bei einer Übereinstimmung dem Maintainer gegenüber sichtbar machen: „Das ähnelt `.out-of-scope/dark-mode.md` — wir haben das zuvor abgelehnt, weil [Grund]. Siehst du das immer noch so?"

Der Maintainer kann:

- **Bestätigen** — das neue Issue wird zur „Prior requests"-Liste der bestehenden Datei hinzugefügt, dann geschlossen
- **Überdenken** — die Out-of-Scope-Datei wird gelöscht oder aktualisiert, und das Issue durchläuft die normale Triage
- **Widersprechen** — die Issues sind verwandt, aber unterschiedlich; mit normaler Triage fortfahren

## Wann in `.out-of-scope/` schreiben

Nur wenn ein **Enhancement** (kein Bug) als `wontfix` abgelehnt wird. Der Ablauf:

1. Maintainer entscheidet, dass eine Feature-Anfrage außerhalb des Scopes liegt
2. Prüfen, ob eine passende `.out-of-scope/`-Datei bereits existiert
3. Wenn ja: das neue Issue zur „Prior requests"-Liste hinzufügen
4. Wenn nein: eine neue Datei mit dem Konzeptnamen, der Entscheidung, Begründung und dem ersten Prior-Request anlegen
5. Einen Kommentar zum Issue posten, der die Entscheidung erklärt und die `.out-of-scope/`-Datei erwähnt
6. Das Issue mit dem `wontfix`-Label schließen

## Out-of-Scope-Dateien aktualisieren oder entfernen

Wenn der Maintainer seine Meinung zu einem früher abgelehnten Konzept ändert:

- Die `.out-of-scope/`-Datei löschen
- Der Skill muss alte Issues nicht wieder öffnen — sie sind historische Nachweise
- Das neue Issue, das die Überprüfung ausgelöst hat, durchläuft die normale Triage
