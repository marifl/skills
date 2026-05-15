---
name: to-issues_DE
description: Einen Plan, eine Spec oder ein PRD in unabhängig greifbare Issues im projekt-eigenen Issue-Tracker aufteilen, mithilfe von Tracer-Bullet-Vertical-Slices. Verwenden, wenn der Nutzer einen Plan in Issues umwandeln, Implementierungs-Tickets erstellen oder Arbeit in Issues aufteilen möchte.
---

# To Issues

Einen Plan in unabhängig greifbare Issues aufteilen, mithilfe von Vertical Slices (Tracer Bullets).

Der Issue-Tracker und das Triage-Label-Vokabular sollten bereitgestellt worden sein — `/setup-matt-pocock-skills` ausführen, wenn nicht.

## Prozess

### 1. Kontext sammeln

Mit dem arbeiten, was bereits im Gesprächskontext vorhanden ist. Wenn der Nutzer eine Issue-Referenz (Issue-Nummer, URL oder Pfad) als Argument übergibt, diese vom Issue-Tracker abrufen und den vollständigen Body und die Kommentare lesen.

### 2. Codebasis erkunden (optional)

Wenn die Codebasis noch nicht erkundet wurde, das tun, um den aktuellen Code-Stand zu verstehen. Issue-Titel und -Beschreibungen sollten das Domänenglossary-Vokabular des Projekts verwenden und ADRs in dem betroffenen Bereich respektieren.

### 3. Vertikale Slices entwerfen

Den Plan in **Tracer-Bullet**-Issues aufteilen. Jedes Issue ist ein dünner Vertical Slice, der ALLE Integrationsschichten end-to-end durchschneidet, KEIN horizontaler Slice einer einzelnen Schicht.

Slices können 'HITL' oder 'AFK' sein. HITL-Slices erfordern menschliche Interaktion, z. B. eine Architekturentscheidung oder einen Design-Review. AFK-Slices können implementiert und gemergt werden ohne menschliche Interaktion. AFK über HITL bevorzugen, wo möglich.

<vertical-slice-rules>
- Jeder Slice liefert einen schmalen, aber VOLLSTÄNDIGEN Pfad durch alle Schichten (Schema, API, UI, Tests)
- Ein abgeschlossener Slice ist allein demonstrierbar oder verifizierbar
- Viele dünne Slices über wenige dicke bevorzugen
</vertical-slice-rules>

### 4. Den Nutzer befragen

Den vorgeschlagenen Aufschlüsselung als nummerierte Liste präsentieren. Für jeden Slice zeigen:

- **Title**: kurzer beschreibender Name
- **Type**: HITL / AFK
- **Blocked by**: welche anderen Slices (falls vorhanden) zuerst abgeschlossen sein müssen
- **User stories covered**: welche User Stories das adressiert (wenn das Quellmaterial welche hat)

Den Nutzer fragen:

- Fühlt sich die Granularität richtig an? (zu grob / zu fein)
- Sind die Abhängigkeitsbeziehungen korrekt?
- Sollten Slices zusammengeführt oder weiter aufgeteilt werden?
- Sind die richtigen Slices als HITL und AFK markiert?

Iterieren, bis der Nutzer die Aufschlüsselung genehmigt.

### 5. Issues im Issue-Tracker veröffentlichen

Für jeden genehmigten Slice ein neues Issue im Issue-Tracker veröffentlichen. Die Issue-Body-Vorlage unten verwenden. Diese Issues gelten als bereit für AFK-Agents, daher mit dem korrekten Triage-Label veröffentlichen, sofern nicht anders angegeben.

Issues in Abhängigkeitsreihenfolge veröffentlichen (Blocker zuerst), damit echte Issue-Identifikatoren im „Blocked by"-Feld referenziert werden können.

<issue-template>
## Parent

Eine Referenz auf das übergeordnete Issue im Issue-Tracker (wenn die Quelle ein bestehendes Issue war, sonst diesen Abschnitt weglassen).

## What to build

Eine prägnante Beschreibung dieses Vertical Slices. Das end-to-end-Verhalten beschreiben, nicht schichtweise Implementierung.

Keine spezifischen Dateipfade oder Code-Snippets — sie veralten schnell. Ausnahme: Wenn ein Prototype einen Snippet erzeugt hat, der eine Entscheidung präziser enkodiert als Prosa kann (State Machine, Reducer, Schema, Typ-Shape), diesen hier einbetten und kurz notieren, dass er von einem Prototype stammt. Auf die entscheidungsreichen Teile kürzen — kein funktionierendes Demo, nur die wichtigen Bits.

## Acceptance criteria

- [ ] Kriterium 1
- [ ] Kriterium 2
- [ ] Kriterium 3

## Blocked by

- Eine Referenz auf das blockierende Ticket (falls vorhanden)

Oder „None - can start immediately", wenn keine Blocker vorhanden.

</issue-template>

Das übergeordnete Issue NICHT schließen oder ändern.
