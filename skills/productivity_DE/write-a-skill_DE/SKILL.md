---
name: write-a-skill_DE
description: Neue Agent-Skills mit ordentlicher Struktur, progressiver Offenlegung und gebündelten Ressourcen erstellen. Verwenden, wenn der Nutzer einen neuen Skill erstellen, schreiben oder bauen möchte.
---

# Skills schreiben

## Prozess

1. **Anforderungen sammeln** - Nutzer fragen zu:
   - Welche Aufgabe/Domäne deckt der Skill ab?
   - Welche spezifischen Use Cases soll er behandeln?
   - Benötigt er ausführbare Scripts oder nur Anweisungen?
   - Gibt es Referenzmaterial einzuschließen?

2. **Den Skill entwerfen** - erstellen:
   - SKILL.md mit prägnanten Anweisungen
   - Zusätzliche Referenzdateien, wenn der Inhalt 500 Zeilen überschreitet
   - Utility-Scripts, wenn deterministische Operationen benötigt werden

3. **Mit Nutzer reviewen** - Entwurf präsentieren und fragen:
   - Deckt das deine Use Cases ab?
   - Fehlt etwas oder ist etwas unklar?
   - Sollte ein Abschnitt mehr/weniger detailliert sein?

## Skill-Struktur

```
skill-name/
├── SKILL.md           # Hauptanweisungen (erforderlich)
├── REFERENCE.md       # Detaillierte Docs (falls nötig)
├── EXAMPLES.md        # Verwendungsbeispiele (falls nötig)
└── scripts/           # Utility-Scripts (falls nötig)
    └── helper.js
```

## SKILL.md-Vorlage

```md
---
name: skill-name
description: Kurze Beschreibung der Fähigkeit. Verwenden wenn [spezifische Auslöser].
---

# Skill-Name

## Quick start

[Minimales funktionierendes Beispiel]

## Workflows

[Schrittweise Prozesse mit Checklisten für komplexe Aufgaben]

## Advanced features

[Link zu separaten Dateien: Siehe [REFERENCE.md](REFERENCE.md)]
```

## Beschreibungsanforderungen

Die Beschreibung ist **das einzige, was der Agent sieht**, wenn er entscheidet, welchen Skill er laden soll. Sie wird im System-Prompt neben allen anderen installierten Skills angezeigt. Der Agent liest diese Beschreibungen und wählt den relevanten Skill basierend auf der Nutzeranfrage.

**Ziel**: Dem Agent gerade genug Informationen geben, um zu wissen:

1. Welche Fähigkeit dieser Skill bietet
2. Wann/warum er ausgelöst werden soll (spezifische Schlüsselwörter, Kontexte, Dateitypen)

**Format**:

- Maximal 1024 Zeichen
- In dritter Person schreiben
- Erster Satz: was er tut
- Zweiter Satz: „Verwenden wenn [spezifische Auslöser]"

**Gutes Beispiel**:

```
Text und Tabellen aus PDF-Dateien extrahieren, Formulare ausfüllen, Dokumente zusammenführen. Verwenden wenn mit PDF-Dateien gearbeitet wird oder der Nutzer PDFs, Formulare oder Dokumentenextraktion erwähnt.
```

**Schlechtes Beispiel**:

```
Hilft mit Dokumenten.
```

Das schlechte Beispiel gibt dem Agent keine Möglichkeit, das von anderen Dokumenten-Skills zu unterscheiden.

## Wann Scripts hinzufügen

Utility-Scripts hinzufügen wenn:

- Operation ist deterministisch (Validierung, Formatierung)
- Derselbe Code würde wiederholt generiert werden
- Fehler brauchen explizite Behandlung

Scripts sparen Tokens und verbessern die Zuverlässigkeit gegenüber generiertem Code.

## Wann Dateien aufteilen

In separate Dateien aufteilen wenn:

- SKILL.md 100 Zeilen überschreitet
- Inhalt hat unterschiedliche Domänen (Finanz- vs. Vertriebs-Schemas)
- Erweiterte Funktionen selten benötigt werden

## Review-Checkliste

Nach dem Entwerfen prüfen:

- [ ] Beschreibung enthält Auslöser („Verwenden wenn...")
- [ ] SKILL.md unter 100 Zeilen
- [ ] Keine zeitkritischen Informationen
- [ ] Konsistente Terminologie
- [ ] Konkrete Beispiele enthalten
- [ ] Referenzen eine Ebene tief
