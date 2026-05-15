# Agent-Briefs schreiben

Ein Agent-Brief ist ein strukturierter Kommentar, der auf einem GitHub-Issue gepostet wird, wenn es zu `ready-for-agent` verschoben wird. Er ist die maßgebliche Spezifikation, von der ein AFK-Agent ausgehen wird. Der ursprüngliche Issue-Body und die Diskussion sind Kontext — der Agent-Brief ist der Vertrag.

## Prinzipien

### Dauerhaftigkeit über Präzision

Das Issue kann Tage oder Wochen in `ready-for-agent` liegen. Die Codebasis wird sich in der Zwischenzeit ändern. Den Brief so schreiben, dass er nützlich bleibt, auch wenn Dateien umbenannt, verschoben oder refaktoriert werden.

- **Tue** Interfaces, Typen und Verhaltensverträge beschreiben
- **Tue** spezifische Typen, Funktionssignaturen oder Config-Shapes nennen, die der Agent suchen oder ändern soll
- **Tue nicht** Dateipfade referenzieren — sie veralten
- **Tue nicht** Zeilennummern referenzieren
- **Nicht annehmen**, dass die aktuelle Implementierungsstruktur gleich bleibt

### Verhaltensorientiert, nicht prozedural

Beschreiben, **was** das System tun soll, nicht **wie** es zu implementieren ist. Der Agent erkundet die Codebasis frisch und trifft eigene Implementierungsentscheidungen.

- **Gut:** „Der `SkillConfig`-Typ sollte ein optionales `schedule`-Feld vom Typ `CronExpression` akzeptieren"
- **Schlecht:** „Öffne src/types/skill.ts und füge ein schedule-Feld in Zeile 42 hinzu"
- **Gut:** „Wenn ein Nutzer `/triage` ohne Argumente ausführt, sollte er eine Zusammenfassung der Issues sehen, die Aufmerksamkeit benötigen"
- **Schlecht:** „Füge eine switch-Anweisung in die Haupthandler-Funktion hinzu"

### Vollständige Abnahmekriterien

Der Agent muss wissen, wann er fertig ist. Jeder Agent-Brief muss konkrete, testbare Abnahmekriterien haben. Jedes Kriterium sollte unabhängig verifizierbar sein.

- **Gut:** „`gh issue list --label needs-triage` gibt Issues zurück, die durch eine erste Klassifizierung gegangen sind"
- **Schlecht:** „Triage sollte korrekt funktionieren"

### Explizite Scope-Grenzen

Festhalten, was außerhalb des Scopes liegt. Das verhindert, dass der Agent goldplattiert oder Annahmen über benachbarte Funktionen macht.

## Vorlage

```markdown
## Agent Brief

**Category:** bug / enhancement
**Summary:** einzeilige Beschreibung, was passieren muss

**Current behavior:**
Beschreibe, was jetzt passiert. Bei Bugs ist das das kaputte Verhalten.
Bei Enhancements ist das der Status quo, auf dem die Funktion aufbaut.

**Desired behavior:**
Beschreibe, was nach der Arbeit des Agents passieren soll.
Bei Edge Cases und Fehlerbedingungen spezifisch sein.

**Key interfaces:**
- `TypeName` — was geändert werden muss und warum
- `functionName()`-Rückgabetyp — was es aktuell zurückgibt vs. was es zurückgeben sollte
- Config-Shape — alle nötigen neuen Konfigurationsoptionen

**Acceptance criteria:**
- [ ] Spezifisches, testbares Kriterium 1
- [ ] Spezifisches, testbares Kriterium 2
- [ ] Spezifisches, testbares Kriterium 3

**Out of scope:**
- Sache, die in diesem Issue NICHT geändert oder adressiert werden sollte
- Benachbarte Funktion, die verwandt wirken mag, aber separat ist
```

## Beispiele

### Guter Agent-Brief (Bug)

```markdown
## Agent Brief

**Category:** bug
**Summary:** Beschreibungsabkürzung bei Skills schneidet mitten im Wort ab und erzeugt kaputten Output

**Current behavior:**
Wenn eine Skill-Beschreibung 1024 Zeichen überschreitet, wird sie genau bei
1024 Zeichen abgeschnitten, unabhängig von Wortgrenzen. Das erzeugt Beschreibungen,
die mitten in einem Wort enden (z. B. „Use when the user wants to confi").

**Desired behavior:**
Das Abschneiden sollte an der letzten Wortgrenze vor 1024 Zeichen unterbrochen
und „..." angehängt werden, um die Kürzung anzuzeigen.

**Key interfaces:**
- Das `description`-Feld des `SkillMetadata`-Typs — keine Typänderung nötig,
  aber die Validierungs-/Verarbeitungslogik, die es befüllt, muss Wortgrenzen respektieren
- Jede Funktion, die SKILL.md-Frontmatter liest und die Beschreibung extrahiert

**Acceptance criteria:**
- [ ] Beschreibungen unter 1024 Zeichen sind unverändert
- [ ] Beschreibungen über 1024 Zeichen werden an der letzten Wortgrenze
      vor 1024 Zeichen abgeschnitten
- [ ] Abgeschnittene Beschreibungen enden mit „..."
- [ ] Die Gesamtlänge einschließlich „..." überschreitet 1024 Zeichen nicht

**Out of scope:**
- Das 1024-Zeichen-Limit selbst ändern
- Mehrzeilige Beschreibungsunterstützung
```

### Guter Agent-Brief (Enhancement)

```markdown
## Agent Brief

**Category:** enhancement
**Summary:** `.out-of-scope/`-Verzeichnisunterstützung hinzufügen, um abgelehnte Feature-Anfragen zu verfolgen

**Current behavior:**
Wenn eine Feature-Anfrage abgelehnt wird, wird das Issue mit einem `wontfix`-Label
und einem Kommentar geschlossen. Es gibt keinen dauerhaften Nachweis der Entscheidung
oder Begründung. Zukünftige ähnliche Anfragen erfordern, dass der Maintainer sich an die
frühere Diskussion erinnert oder danach sucht.

**Desired behavior:**
Abgelehnte Feature-Anfragen sollten in `.out-of-scope/<concept>.md`-Dateien
dokumentiert werden, die die Entscheidung, Begründung und Links zu allen Issues
erfassen, die die Funktion angefragt haben. Beim Triagieren neuer Issues sollten
diese Dateien auf Übereinstimmungen geprüft werden.

**Key interfaces:**
- Markdown-Dateiformat in `.out-of-scope/` — jede Datei sollte eine
  `# Concept Name`-Überschrift, eine `**Decision:**`-Zeile, eine `**Reason:**`-Zeile
  und eine `**Prior requests:**`-Liste mit Issue-Links haben
- Der Triage-Workflow sollte alle `.out-of-scope/*.md`-Dateien früh lesen
  und eingehende Issues auf konzeptionelle Ähnlichkeit hin abgleichen

**Acceptance criteria:**
- [ ] Das Schließen einer Funktion als wontfix erstellt/aktualisiert eine Datei in `.out-of-scope/`
- [ ] Die Datei enthält die Entscheidung, Begründung und Link zum geschlossenen Issue
- [ ] Wenn eine passende `.out-of-scope/`-Datei bereits existiert, wird das neue Issue
      zur „Prior requests"-Liste hinzugefügt statt ein Duplikat zu erstellen
- [ ] Beim Triagieren werden vorhandene `.out-of-scope/`-Dateien geprüft und sichtbar
      gemacht, wenn ein neues Issue zu einer früheren Ablehnung passt

**Out of scope:**
- Automatisiertes Matching (Mensch bestätigt die Übereinstimmung)
- Früher abgelehnte Funktionen wieder öffnen
- Bug-Reports (nur Enhancement-Ablehnungen kommen zu `.out-of-scope/`)
```

### Schlechter Agent-Brief

```markdown
## Agent Brief

**Summary:** Den Triage-Bug beheben

**What to do:**
Das Triage-Ding ist kaputt. Sieh dir die Hauptdatei an und behebe es.
Die Funktion um Zeile 150 hat das Problem.

**Files to change:**
- src/triage/handler.ts (Zeile 150)
- src/types.ts (Zeile 42)
```

Das ist schlecht, weil:
- Keine Kategorie
- Vage Beschreibung („das Triage-Ding ist kaputt")
- Referenziert Dateipfade und Zeilennummern, die veralten werden
- Keine Abnahmekriterien
- Keine Scope-Grenzen
- Keine Beschreibung des aktuellen vs. gewünschten Verhaltens
