---
name: grill-with-docs_DE
description: Grilling-Session, die den Plan gegen das bestehende Domänenmodell herausfordert, die Terminologie schärft und `CONTEXT.md` sowie ADRs inline aktualisiert, wenn Entscheidungen kristallisieren. Verwenden, wenn der Nutzer einen Plan gegen die Sprache und dokumentierten Entscheidungen seines Projekts stress-testen möchte.
---

<what-to-do>

Befrage mich schonungslos zu jedem Aspekt dieses Plans, bis wir ein gemeinsames Verständnis erreicht haben. Gehe jeden Zweig des Design-Baums durch und löse Abhängigkeiten zwischen Entscheidungen Schritt für Schritt. Für jede Frage gib deine empfohlene Antwort.

Stelle die Fragen einzeln und warte auf Feedback zur jeweiligen Frage, bevor du weitermachst.

Wenn eine Frage durch Erkunden der Codebasis beantwortet werden kann, erkunde stattdessen die Codebasis.

</what-to-do>

<supporting-info>

## Domänenbewusstsein

Beim Erkunden der Codebasis auch nach bestehender Dokumentation suchen:

### Dateistruktur

Die meisten Repos haben einen einzigen Context:

```
/
├── CONTEXT.md
├── docs/
│   └── adr/
│       ├── 0001-event-sourced-orders.md
│       └── 0002-postgres-for-write-model.md
└── src/
```

Wenn eine `CONTEXT-MAP.md` im Root existiert, hat das Repo mehrere Contexts. Die Map zeigt, wo jeder einzelne lebt:

```
/
├── CONTEXT-MAP.md
├── docs/
│   └── adr/                          ← systemweite Entscheidungen
├── src/
│   ├── ordering/
│   │   ├── CONTEXT.md
│   │   └── docs/adr/                 ← context-spezifische Entscheidungen
│   └── billing/
│       ├── CONTEXT.md
│       └── docs/adr/
```

Dateien lazy erstellen — nur wenn es etwas zu schreiben gibt. Wenn keine `CONTEXT.md` existiert, eine anlegen, wenn der erste Begriff aufgelöst wird. Wenn kein `docs/adr/` existiert, es anlegen, wenn der erste ADR gebraucht wird.

## Während der Session

### Gegen das Glossar herausfordern

Wenn der Nutzer einen Begriff verwendet, der mit der bestehenden Sprache in `CONTEXT.md` kollidiert, sofort darauf hinweisen. „Dein Glossar definiert 'Stornierung' als X, aber du scheinst Y zu meinen — was stimmt?"

### Vage Sprache schärfen

Wenn der Nutzer vage oder überladene Begriffe verwendet, einen präzisen Kanonbegriff vorschlagen. „Du sagst 'Account' — meinst du den Customer oder den User? Das sind verschiedene Dinge."

### Konkrete Szenarien besprechen

Wenn Domänenbeziehungen diskutiert werden, diese mit spezifischen Szenarien stress-testen. Szenarien erfinden, die Edge Cases prüfen und den Nutzer zwingen, bei den Grenzen zwischen Konzepten präzise zu sein.

### Mit dem Code gegenprüfen

Wenn der Nutzer beschreibt, wie etwas funktioniert, prüfen, ob der Code zustimmt. Wenn ein Widerspruch gefunden wird, ihn sichtbar machen: „Dein Code storniert ganze Orders, aber du hast gerade gesagt, dass Teilstornierungen möglich sind — was stimmt?"

### CONTEXT.md inline aktualisieren

Wenn ein Begriff aufgelöst ist, `CONTEXT.md` sofort aktualisieren. Nicht stapeln — sofort erfassen. Format aus [CONTEXT-FORMAT.md](./CONTEXT-FORMAT.md) verwenden.

`CONTEXT.md` sollte vollständig frei von Implementierungsdetails sein. `CONTEXT.md` nicht als Spec, Notizblock oder Repository für Implementierungsentscheidungen behandeln. Es ist nur ein Glossar.

### ADRs sparsam anbieten

Einen ADR nur anbieten, wenn alle drei Bedingungen erfüllt sind:

1. **Schwer rückgängig zu machen** — die Kosten, seine Meinung später zu ändern, sind bedeutsam
2. **Überraschend ohne Kontext** — ein zukünftiger Leser wird sich fragen: „Warum haben sie das so gemacht?"
3. **Ergebnis eines echten Trade-offs** — es gab echte Alternativen, und man hat eine aus bestimmten Gründen gewählt

Wenn eines der drei fehlt, den ADR weglassen. Format aus [ADR-FORMAT.md](./ADR-FORMAT.md) verwenden.

</supporting-info>
