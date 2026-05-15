# ADR-Format

ADRs liegen in `docs/adr/` und verwenden sequentielle Nummerierung: `0001-slug.md`, `0002-slug.md`, usw.

Das Verzeichnis `docs/adr/` lazy anlegen — nur wenn der erste ADR gebraucht wird.

## Vorlage

```md
# {Kurzer Titel der Entscheidung}

{1–3 Sätze: Was ist der Kontext, was haben wir entschieden und warum.}
```

Das ist alles. Ein ADR kann ein einzelner Absatz sein. Der Wert liegt darin, festzuhalten, *dass* eine Entscheidung getroffen wurde und *warum* — nicht darin, Abschnitte auszufüllen.

## Optionale Abschnitte

Diese nur einbeziehen, wenn sie echten Mehrwert haben. Die meisten ADRs werden sie nicht benötigen.

- **Status**-Frontmatter (`proposed | accepted | deprecated | superseded by ADR-NNNN`) — nützlich, wenn Entscheidungen überarbeitet werden
- **Considered Options** — nur wenn die verworfenen Alternativen es wert sind, erinnert zu werden
- **Consequences** — nur wenn nicht-offensichtliche Folgewirkungen hervorgehoben werden müssen

## Nummerierung

`docs/adr/` nach der höchsten bestehenden Nummer durchsuchen und um eins erhöhen.

## Wann einen ADR anbieten

Alle drei müssen zutreffen:

1. **Schwer rückgängig zu machen** — die Kosten, seine Meinung später zu ändern, sind bedeutsam
2. **Überraschend ohne Kontext** — ein zukünftiger Leser wird den Code ansehen und sich fragen: „Warum haben sie das so gemacht?"
3. **Ergebnis eines echten Trade-offs** — es gab echte Alternativen, und man hat eine aus bestimmten Gründen gewählt

Wenn eine Entscheidung leicht rückgängig zu machen ist, weglassen — man macht sie einfach rückgängig. Wenn sie nicht überraschend ist, wird sich niemand fragen warum. Wenn es keine echte Alternative gab, gibt es nichts festzuhalten außer „wir haben das Offensichtliche getan."

### Was sich qualifiziert

- **Architektonische Form.** „Wir verwenden ein Monorepo." „Das Write-Model ist event-sourced, das Read-Model wird in Postgres projiziert."
- **Integrationsmuster zwischen Contexts.** „Ordering und Billing kommunizieren über Domain-Events, nicht synchrones HTTP."
- **Technologieentscheidungen mit Lock-in.** Datenbank, Message Bus, Auth-Provider, Deployment-Ziel. Nicht jede Bibliothek — nur die, bei denen ein Austausch ein Quartal dauern würde.
- **Grenz- und Scope-Entscheidungen.** „Customer-Daten werden vom Customer-Context besessen; andere Contexts referenzieren sie nur per ID." Die expliziten Nein-s sind so wertvoll wie die Ja-s.
- **Bewusste Abweichungen vom offensichtlichen Weg.** „Wir verwenden manuelles SQL statt eines ORM, weil X." Alles, wo ein vernünftiger Leser das Gegenteil annehmen würde. Damit verhindert man, dass der nächste Entwickler etwas „repariert", das beabsichtigt war.
- **Einschränkungen, die nicht im Code sichtbar sind.** „Wir können AWS nicht nutzen wegen Compliance-Anforderungen." „Antwortzeiten müssen unter 200ms liegen wegen des Partner-API-Vertrags."
- **Abgelehnte Alternativen, wenn die Ablehnung nicht offensichtlich ist.** Wenn GraphQL erwogen und REST aus subtilen Gründen gewählt wurde, das festhalten — sonst wird jemand in sechs Monaten wieder GraphQL vorschlagen.
