---
name: improve-codebase-architecture_DE
description: Vertiefungsmöglichkeiten in einer Codebasis finden, informiert durch die Domänensprache in CONTEXT.md und die Entscheidungen in docs/adr/. Verwenden, wenn der Nutzer die Architektur verbessern, Refactoring-Möglichkeiten finden, eng gekoppelte Module konsolidieren oder eine Codebasis testbarer und AI-navigierbarer machen möchte.
---

# Codebase-Architektur verbessern

Architektonische Reibungspunkte sichtbar machen und **Vertiefungsmöglichkeiten** vorschlagen — Refactorings, die flache Module in tiefe verwandeln. Das Ziel ist Testbarkeit und AI-Navigierbarkeit.

## Glossar

Diese Begriffe exakt in jedem Vorschlag verwenden. Konsistente Sprache ist der Punkt — nicht in „component", „service", „API" oder „boundary" abdriften. Vollständige Definitionen in [LANGUAGE.md](LANGUAGE.md).

- **Module** — alles mit einem Interface und einer Implementation (Funktion, Klasse, Paket, Slice).
- **Interface** — alles, was ein Caller wissen muss, um das Module zu verwenden: Typen, Invarianten, Fehlermodi, Reihenfolge, Config. Nicht nur die Typsignatur.
- **Implementation** — der Code darin.
- **Depth** — Leverage am Interface: viel Verhalten hinter einem kleinen Interface. **Deep** = hohe Leverage. **Shallow** = Interface fast so komplex wie die Implementation.
- **Seam** — wo ein Interface lebt; ein Ort, wo Verhalten geändert werden kann, ohne direkt dort zu editieren. (Diesen Begriff verwenden, nicht „boundary".)
- **Adapter** — ein konkretes Ding, das ein Interface an einem Seam erfüllt.
- **Leverage** — was Caller von Depth bekommen.
- **Locality** — was Maintainer von Depth bekommen: Änderungen, Bugs, Wissen an einem Ort konzentriert.

Schlüsselprinzipien (vollständige Liste in [LANGUAGE.md](LANGUAGE.md)):

- **Deletion-Test**: sich vorstellen, das Module zu löschen. Wenn Komplexität verschwindet, war es ein Pass-through. Wenn Komplexität bei N Callern wieder erscheint, hat es seinen Wert gerechtfertigt.
- **Das Interface ist die Test-Oberfläche.**
- **Ein Adapter = hypothetischer Seam. Zwei Adapter = echter Seam.**

Dieser Skill wird durch das Domänenmodell des Projekts _informiert_. Die Domänensprache gibt Namen für gute Seams; ADRs halten Entscheidungen fest, die der Skill nicht neu verhandeln sollte.

## Prozess

### 1. Erkunden

Das Domänenglossary des Projekts und alle ADRs in dem Bereich lesen, den man zuerst berührt.

Dann das Agent-Tool mit `subagent_type=Explore` nutzen, um die Codebasis zu durchlaufen. Keine starren Heuristiken verfolgen — organisch erkunden und notieren, wo Reibung erlebt wird:

- Wo erfordert das Verstehen eines Konzepts das Hin-und-Herspringen zwischen vielen kleinen Modulen?
- Wo sind Module **shallow** — Interface fast so komplex wie die Implementation?
- Wo wurden pure Funktionen nur für Testbarkeit extrahiert, aber die echten Bugs verbergen sich darin, wie sie aufgerufen werden (keine **Locality**)?
- Wo lecken eng gekoppelte Module über ihre Seams?
- Welche Teile der Codebasis sind ungetestet oder schwer durch ihr aktuelles Interface zu testen?

Den **Deletion-Test** auf alles anwenden, was als shallow verdächtig erscheint: Würde das Löschen Komplexität konzentrieren oder nur verschieben? Ein „Ja, konzentriert" ist das Signal, das gesucht wird.

### 2. Kandidaten präsentieren

Eine nummerierte Liste von Vertiefungsmöglichkeiten präsentieren. Für jeden Kandidaten:

- **Files** — welche Dateien/Module beteiligt sind
- **Problem** — warum die aktuelle Architektur Reibung verursacht
- **Solution** — Klartextbeschreibung, was sich ändern würde
- **Benefits** — in Begriffen von Locality und Leverage erklärt, und auch darin, wie Tests sich verbessern würden

**Das Vokabular aus CONTEXT.md für die Domäne und das aus [LANGUAGE.md](LANGUAGE.md) für die Architektur verwenden.** Wenn `CONTEXT.md` „Order" definiert, von „dem Order-Intake-Module" sprechen — nicht von „dem FooBarHandler" und nicht von „dem Order-Service".

**ADR-Konflikte**: Wenn ein Kandidat einen bestehenden ADR widerspricht, ihn nur sichtbar machen, wenn die Reibung real genug ist, um den ADR neu zu überdenken. Klar markieren (z. B. _„widerspricht ADR-0007 — aber es lohnt sich, das neu zu öffnen, weil…"_). Nicht jeden theoretischen Refactor auflisten, den ein ADR verbietet.

Noch KEINE Interfaces vorschlagen. Den Nutzer fragen: „Welches davon möchtest du erkunden?"

### 3. Grilling-Loop

Sobald der Nutzer einen Kandidaten wählt, in ein Grilling-Gespräch einsteigen. Den Design-Baum mit ihm durchgehen — Einschränkungen, Abhängigkeiten, die Form des vertieften Moduls, was hinter dem Seam liegt, welche Tests überleben.

Seiteneffekte treten inline auf, wenn Entscheidungen kristallisieren:

- **Ein vertieftes Module nach einem Konzept benennen, das nicht in `CONTEXT.md` ist?** Den Begriff zu `CONTEXT.md` hinzufügen — gleiche Disziplin wie `/grill-with-docs` (siehe [CONTEXT-FORMAT.md](../grill-with-docs_DE/CONTEXT-FORMAT.md)). Die Datei lazy anlegen, wenn sie nicht existiert.
- **Einen vagen Begriff während des Gesprächs schärfen?** `CONTEXT.md` sofort aktualisieren.
- **Nutzer lehnt den Kandidaten mit einem tragfähigen Grund ab?** Einen ADR anbieten, formuliert als: _„Soll ich das als ADR festhalten, damit zukünftige Architektur-Reviews es nicht neu vorschlagen?"_ Nur anbieten, wenn der Grund von einem zukünftigen Erkunder tatsächlich gebraucht wird, um dasselbe nicht neu vorzuschlagen — ephemere Gründe überspringen („lohnt sich gerade nicht") und selbstverständliche. Siehe [ADR-FORMAT.md](../grill-with-docs_DE/ADR-FORMAT.md).
- **Alternative Interfaces für das vertiefte Module erkunden wollen?** Siehe [INTERFACE-DESIGN.md](INTERFACE-DESIGN.md).
