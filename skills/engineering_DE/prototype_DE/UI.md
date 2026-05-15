# UI-Prototype

**Mehrere radikal verschiedene UI-Variationen** auf einer einzigen Route generieren, von einer schwebenden unteren Leiste umschaltbar. Der Nutzer blättert im Browser zwischen Varianten, wählt eine (oder klaut Bits aus jeder), und wirft den Rest weg.

Wenn die Frage Logik/Zustand betrifft anstatt wie etwas aussieht — falscher Ast. [LOGIC.md](LOGIC.md) verwenden.

## Wann das die richtige Form ist

- „Wie soll diese Seite aussehen?"
- „Ich möchte ein paar Optionen für dieses Dashboard sehen, bevor ich mich festlege."
- „Probiere ein anderes Layout für den Einstellungsbildschirm."
- Jedes Mal, wenn der Nutzer sonst einen Tag damit verbringen würde, zwischen drei vagen Mockups in seinem Kopf zu wählen.

## Zwei Unterformen — stark Unterform A bevorzugen

Ein UI-Prototype ist viel einfacher zu beurteilen, wenn er **direkt neben dem Rest der App** ist — echter Header, echte Sidebar, echte Daten, echte Dichte. Eine eigenständige Route für sich ist ein Vakuum: Jede Variante sieht in Isolation gut aus. Standardmäßig Unterform A verwenden, wenn es eine plausible bestehende Seite gibt, die die Varianten beherbergen kann. Nur zu Unterform B greifen, wenn der Prototype wirklich keine nahegelegene Heimat hat.

### Unterform A — Anpassung einer bestehenden Seite (bevorzugt)

Die Route existiert bereits. Varianten werden **auf derselben Route** gerendert, gesteuert durch einen `?variant=`-URL-Suchparameter. Das bestehende Data-Fetching, Params und Auth bleiben — nur das Rendering wechselt. Das ist der Standard; diesen wählen, es sei denn, es gibt einen spezifischen Grund dagegen.

Wenn der Prototype für etwas ist, das noch keine Seite hat, aber *natürlich innerhalb einer leben würde* (ein neuer Abschnitt des Dashboards, eine neue Karte auf dem Einstellungsbildschirm, ein neuer Schritt in einem bestehenden Flow) — das ist immer noch Unterform A. Die Varianten innerhalb der Host-Seite einbetten.

### Unterform B — eine neue Seite (letzter Ausweg)

Nur verwenden, wenn das Prototypisierte wirklich keine bestehende Seite hat, in der es leben kann — z. B. eine völlig neue Top-Level-Oberfläche oder ein Flow, der nirgendwo sinnvoll eingebettet werden kann.

Eine **Wegwurf-Route** anlegen, die der bereits im Projekt verwendeten Routing-Konvention folgt — keine neue Top-Level-Struktur erfinden. So benennen, dass es offensichtlich ein Prototype ist (z. B. das Wort `prototype` in Pfad oder Dateiname einbeziehen). Gleiches `?variant=`-Muster.

Vor dem Committen zu Unterform B sanity-checken: Gibt es wirklich keine bestehende Seite, in die das eingebettet werden könnte? Eine leere Route versteckt Design-Probleme, die eine befüllte aufdecken würde.

In beiden Unterformen ist die schwebende untere Leiste identisch.

## Prozess

### 1. Die Frage formulieren und N wählen

Standardmäßig **3 Varianten**. Mehr als 5 hören auf, radikal verschieden zu sein, und werden zu Lärm — dort deckeln.

Den Plan in einer Zeile aufschreiben, im Ort des Prototypes oder als Kommentar am Dateianfang:

> „Drei Varianten der Einstellungsseite, umschaltbar via `?variant=`, auf der bestehenden `/settings`-Route."

Das funktioniert, ob der Nutzer jetzt zum Zurückdrängen da ist oder nicht.

### 2. Radikal verschiedene Varianten generieren

Jede Variante entwerfen. Jede an diesen halten:

- Dem Zweck der Seite und den Daten, auf die sie Zugriff hat.
- Der Komponentenbibliothek / dem Styling-System des Projekts (TailwindCSS, shadcn, MUI, plain CSS, was auch immer).
- Einem klaren exportierten Komponentennamen, z. B. `VariantA`, `VariantB`, `VariantC`.

Varianten müssen **strukturell unterschiedlich** sein — unterschiedliches Layout, unterschiedliche Informationshierarchie, unterschiedliche primäre Affordance, nicht nur unterschiedliche Farben. Drei leicht angepasste Karten-Grids ist kein UI-Prototype, das ist Tapete. Wenn zwei Entwürfe zu ähnlich herauskommen, einen mit expliziter „Kein Karten-Grid verwenden"-Anleitung neu machen.

### 3. Zusammenschalten

Eine einzelne Switcher-Komponente auf der Route erstellen:

```tsx
// Pseudo-Code — ans Framework des Projekts anpassen
const variant = searchParams.get('variant') ?? 'A';
return (
  <>
    {variant === 'A' && <VariantA {...data} />}
    {variant === 'B' && <VariantB {...data} />}
    {variant === 'C' && <VariantC {...data} />}
    <PrototypeSwitcher variants={['A','B','C']} current={variant} />
  </>
);
```

Für Unterform A (bestehende Seite): alles bestehende Data-Fetching über dem Switcher behalten; nur der gerenderte Teilbaum ändert sich pro Variante.

Für Unterform B (neue Seite): die Wegwurf-Route unter `/prototype/<name>` bindet denselben Switcher ein.

### 4. Die schwebende Leiste bauen

Eine kleine, fest positionierte Leiste am unteren Mitte des Bildschirms mit drei Teilen:

- **Linker Pfeil** — zur vorherigen Variante wechseln (läuft um).
- **Varianten-Label** — zeigt den aktuellen Variantenschlüssel und, wenn die Variante einen Namen exportiert, diesen ebenfalls. Z. B. `B — Sidebar layout`.
- **Rechter Pfeil** — vorwärts wechseln (läuft um).

Verhalten:

- Das Klicken auf einen Pfeil aktualisiert den URL-Suchparameter (den Router des Frameworks verwenden — `router.replace` bei Next, `navigate` bei React Router, etc.), damit die Variante teilbar und reload-stabil ist.
- Tastatur: `←`- und `→`-Pfeiltasten wechseln ebenfalls. Pfeiltasten nicht abfangen, wenn ein `<input>`, `<textarea>` oder `[contenteditable]` fokussiert ist.
- Visuell vom Seiteninhalt unterscheidbar (z. B. hochkontrastiger Pill, dezenter Schatten), damit es offensichtlich nicht Teil des bewerteten Designs ist.
- In Produktions-Builds ausgeblendet — mit `process.env.NODE_ENV !== 'production'` oder einem äquivalenten Check absichern, damit ein versehentlicher Prototype-Merge die Leiste nicht an Nutzer ausliefern kann.

Den Switcher in eine einzelne gemeinsame Komponente stecken, damit beide Unterformen ihn wiederverwenden können. An dem Ort ablegen, wo die gemeinsame UI im Projekt lebt.

### 5. Übergeben

Die URL (und die `?variant=`-Schlüssel) sichtbar machen. Der Nutzer wird blättern, wann immer er dazu kommt. Das interessante Feedback ist normalerweise **„Ich möchte den Header von B mit der Sidebar von C"** — das ist das tatsächliche Design, das er möchte.

### 6. Die Antwort erfassen und aufräumen

Sobald eine Variante gewonnen hat, aufschreiben welche und warum (Commit-Message, ADR, Issue oder eine `NOTES.md` neben dem Prototype, wenn AFK und der Nutzer noch nicht geantwortet hat). Dann:

- **Unterform A** — die verlierenden Varianten und den Switcher löschen; den Gewinner in die bestehende Seite einbauen.
- **Unterform B** — die gewinnende Variante zu einer echten Route promoten, die Wegwurf-Route und den Switcher löschen.

Keine Varianten-Komponenten oder den Switcher herumliegen lassen. Sie veralten schnell und verwirren den nächsten Leser.

## Anti-Patterns

- **Varianten, die sich nur in Farbe oder Text unterscheiden.** Das ist eine Anpassung, kein Prototype. Echte Varianten sind sich über Struktur uneinig.
- **Zu viel Code zwischen Varianten teilen.** Ein gemeinsamer `<Header>` ist in Ordnung; ein gemeinsames `<Layout>` verfehlt den Punkt. Jede Variante sollte das Layout frei verwerfen können.
- **Varianten an echte Mutationen anschließen.** Read-only-Prototypes sind in Ordnung. Wenn eine Variante mutieren muss, auf einen Stub zeigen — die Frage ist „wie soll das aussehen", nicht „funktioniert das Backend".
- **Den Prototype direkt in Produktion bringen.** Der Variantencode wurde unter Prototype-Bedingungen geschrieben (keine Tests, minimales Error-Handling). Beim Einbauen ordentlich neu schreiben.
