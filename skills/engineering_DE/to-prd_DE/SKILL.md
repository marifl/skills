---
name: to-prd_DE
description: Den aktuellen Gesprächskontext in ein PRD umwandeln und es im projekt-eigenen Issue-Tracker veröffentlichen. Verwenden, wenn der Nutzer ein PRD aus dem aktuellen Kontext erstellen möchte.
---

Dieser Skill nimmt den aktuellen Gesprächskontext und das Codebasis-Verständnis und erzeugt ein PRD. Den Nutzer NICHT interviewen — einfach aus dem Bekannten synthetisieren.

Der Issue-Tracker und das Triage-Label-Vokabular sollten bereitgestellt worden sein — `/setup-matt-pocock-skills` ausführen, wenn nicht.

## Prozess

1. Den Repo erkunden, um den aktuellen Stand der Codebasis zu verstehen, wenn das noch nicht getan wurde. Das Domänenglossary-Vokabular des Projekts im gesamten PRD verwenden und alle ADRs in dem betroffenen Bereich respektieren.

2. Die wichtigsten Module skizzieren, die gebaut oder modifiziert werden müssen, um die Implementierung abzuschließen. Aktiv nach Möglichkeiten suchen, Deep Modules zu extrahieren, die in Isolation getestet werden können.

Ein Deep Module (im Gegensatz zu einem Shallow Module) ist eines, das eine Menge Funktionalität in einem einfachen, testbaren Interface kapselt, das sich selten ändert.

Mit dem Nutzer prüfen, ob diese Module seinen Erwartungen entsprechen. Mit dem Nutzer prüfen, für welche Module er Tests geschrieben haben möchte.

3. Das PRD mithilfe der Vorlage unten schreiben, dann im projekt-eigenen Issue-Tracker veröffentlichen. Das `ready-for-agent`-Triage-Label anwenden — keine weitere Triage nötig.

<prd-template>

## Problem Statement

Das Problem, mit dem der Nutzer konfrontiert ist, aus der Nutzerperspektive.

## Solution

Die Lösung für das Problem, aus der Nutzerperspektive.

## User Stories

Eine LANGE, nummerierte Liste von User Stories. Jede User Story sollte im Format sein:

1. As an <actor>, I want a <feature>, so that <benefit>

<user-story-example>
1. As a mobile bank customer, I want to see balance on my accounts, so that I can make better informed decisions about my spending
</user-story-example>

Diese Liste von User Stories sollte äußerst umfangreich sein und alle Aspekte des Features abdecken.

## Implementation Decisions

Eine Liste von Implementierungsentscheidungen, die getroffen wurden. Diese kann umfassen:

- Die Module, die gebaut/modifiziert werden
- Die Interfaces dieser Module, die modifiziert werden
- Technische Klärungen vom Entwickler
- Architekturentscheidungen
- Schema-Änderungen
- API-Verträge
- Spezifische Interaktionen

KEINE spezifischen Dateipfade oder Code-Snippets einschließen. Sie können schnell veralten.

Ausnahme: Wenn ein Prototype einen Snippet erzeugt hat, der eine Entscheidung präziser enkodiert als Prosa kann (State Machine, Reducer, Schema, Typ-Shape), diesen innerhalb der relevanten Entscheidung einbetten und kurz notieren, dass er von einem Prototype stammt. Auf die entscheidungsreichen Teile kürzen — kein funktionierendes Demo, nur die wichtigen Bits.

## Testing Decisions

Eine Liste von Test-Entscheidungen, die getroffen wurden. Einschließen:

- Eine Beschreibung, was einen guten Test ausmacht (nur externes Verhalten testen, nicht Implementierungsdetails)
- Welche Module getestet werden
- Prior Art für die Tests (d. h. ähnliche Typen von Tests in der Codebasis)

## Out of Scope

Eine Beschreibung der Dinge, die außerhalb des Scopes dieses PRDs liegen.

## Further Notes

Weitere Notizen zum Feature.

</prd-template>
