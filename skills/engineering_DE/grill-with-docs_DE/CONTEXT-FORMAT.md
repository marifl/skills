# CONTEXT.md-Format

## Struktur

```md
# {Context-Name}

{Ein oder zwei Sätze Beschreibung, was dieser Context ist und warum er existiert.}

## Language

**Order**:
{Eine prägnante Beschreibung des Begriffs}
_Avoid_: Purchase, transaction

**Invoice**:
A request for payment sent to a customer after delivery.
_Avoid_: Bill, payment request

**Customer**:
A person or organization that places orders.
_Avoid_: Client, buyer, account

## Relationships

- An **Order** produces one or more **Invoices**
- An **Invoice** belongs to exactly one **Customer**

## Example dialogue

> **Dev:** "When a **Customer** places an **Order**, do we create the **Invoice** immediately?"
> **Domain expert:** "No — an **Invoice** is only generated once a **Fulfillment** is confirmed."

## Flagged ambiguities

- "account" was used to mean both **Customer** and **User** — resolved: these are distinct concepts.
```

## Regeln

- **Meinungsstark sein.** Wenn mehrere Wörter für dasselbe Konzept existieren, das beste wählen und die anderen als zu vermeidende Aliases auflisten.
- **Konflikte explizit kennzeichnen.** Wenn ein Begriff mehrdeutig verwendet wird, das unter „Flagged ambiguities" mit einer klaren Auflösung hervorheben.
- **Definitionen knapp halten.** Maximal ein Satz. Definieren, was es IST, nicht was es tut.
- **Beziehungen zeigen.** Fette Begriffsnamen verwenden und Kardinalität ausdrücken, wo offensichtlich.
- **Nur Begriffe einschließen, die für den Context dieses Projekts spezifisch sind.** Allgemeine Programmierkonzepte (Timeouts, Fehlertypen, Utility-Patterns) gehören nicht dazu, auch wenn das Projekt sie intensiv nutzt. Vor dem Hinzufügen eines Begriffs fragen: Ist das ein Konzept, das einzigartig für diesen Context ist, oder ein allgemeines Programmierkonzept? Nur Ersteres gehört rein.
- **Begriffe unter Unterüberschriften gruppieren**, wenn natürliche Cluster entstehen. Wenn alle Begriffe zu einem einzigen zusammenhängenden Bereich gehören, ist eine flache Liste in Ordnung.
- **Einen Beispieldialog schreiben.** Ein Gespräch zwischen einem Entwickler und einem Domänenexperten, das demonstriert, wie die Begriffe natürlich interagieren und Grenzen zwischen verwandten Konzepten klärt.

## Single- vs. Multi-Context-Repos

**Single-Context (die meisten Repos):** Eine `CONTEXT.md` im Repo-Root.

**Mehrere Contexts:** Eine `CONTEXT-MAP.md` im Repo-Root listet die Contexts auf, wo sie leben und wie sie miteinander in Beziehung stehen:

```md
# Context Map

## Contexts

- [Ordering](./src/ordering/CONTEXT.md) — empfängt und verfolgt Kundenbestellungen
- [Billing](./src/billing/CONTEXT.md) — generiert Rechnungen und verarbeitet Zahlungen
- [Fulfillment](./src/fulfillment/CONTEXT.md) — verwaltet Lager-Kommissionierung und Versand

## Relationships

- **Ordering → Fulfillment**: Ordering emittiert `OrderPlaced`-Events; Fulfillment konsumiert sie, um die Kommissionierung zu starten
- **Fulfillment → Billing**: Fulfillment emittiert `ShipmentDispatched`-Events; Billing konsumiert sie, um Rechnungen zu generieren
- **Ordering ↔ Billing**: Gemeinsame Typen für `CustomerId` und `Money`
```

Der Skill leitet die zutreffende Struktur ab:

- Wenn `CONTEXT-MAP.md` existiert, lesen, um Contexts zu finden
- Wenn nur eine Root-`CONTEXT.md` existiert, Single-Context
- Wenn keines existiert, eine Root-`CONTEXT.md` lazy anlegen, wenn der erste Begriff aufgelöst wird

Wenn mehrere Contexts existieren, ableiten, auf welchen sich das aktuelle Thema bezieht. Wenn unklar, fragen.
