# Domain-Docs

Wie die Engineering-Skills die Domänendokumentation dieses Repos beim Erkunden der Codebasis konsumieren sollen.

## Vor dem Erkunden, diese lesen

- **`CONTEXT.md`** im Repo-Root, oder
- **`CONTEXT-MAP.md`** im Repo-Root, wenn sie existiert — sie zeigt auf eine `CONTEXT.md` pro Context. Jede lesen, die für das Thema relevant ist.
- **`docs/adr/`** — ADRs lesen, die den Bereich betreffen, in dem gerade gearbeitet wird. In Multi-Context-Repos auch `src/<context>/docs/adr/` für context-spezifische Entscheidungen prüfen.

Wenn eine dieser Dateien nicht existiert, **still fortfahren**. Ihr Fehlen nicht melden; nicht vorschlagen, sie im Voraus anzulegen. Der Producer-Skill (`/grill-with-docs`) legt sie lazy an, wenn Begriffe oder Entscheidungen tatsächlich aufgelöst werden.

## Dateistruktur

Single-Context-Repo (die meisten Repos):

```
/
├── CONTEXT.md
├── docs/adr/
│   ├── 0001-event-sourced-orders.md
│   └── 0002-postgres-for-write-model.md
└── src/
```

Multi-Context-Repo (Anwesenheit von `CONTEXT-MAP.md` im Root):

```
/
├── CONTEXT-MAP.md
├── docs/adr/                          ← systemweite Entscheidungen
└── src/
    ├── ordering/
    │   ├── CONTEXT.md
    │   └── docs/adr/                  ← context-spezifische Entscheidungen
    └── billing/
        ├── CONTEXT.md
        └── docs/adr/
```

## Das Glossar-Vokabular verwenden

Wenn der Output ein Domänenkonzept benennt (in einem Issue-Titel, einem Refactoring-Vorschlag, einer Hypothese, einem Testnamen), den Begriff so verwenden, wie er in `CONTEXT.md` definiert ist. Nicht zu Synonymen abdriften, die das Glossar explizit vermeidet.

Wenn das benötigte Konzept noch nicht im Glossar ist, ist das ein Signal — entweder wird Sprache erfunden, die das Projekt nicht verwendet (überdenken), oder es gibt eine echte Lücke (für `/grill-with-docs` vormerken).

## ADR-Konflikte markieren

Wenn der Output einem bestehenden ADR widerspricht, das explizit sichtbar machen statt stillschweigend zu überschreiben:

> _Widerspricht ADR-0007 (event-sourced orders) — aber es lohnt sich, das neu zu öffnen, weil…_
