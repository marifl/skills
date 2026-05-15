---
name: migrate-to-shoehorn_DE
description: Test-Dateien von `as`-Typ-Assertions zu @total-typescript/shoehorn migrieren. Verwenden, wenn der Nutzer shoehorn erwähnt, `as` in Tests ersetzen möchte oder partielle Testdaten benötigt.
---

# Zu Shoehorn migrieren

## Warum shoehorn?

`shoehorn` ermöglicht es, partielle Daten in Tests zu übergeben, während TypeScript zufrieden bleibt. Es ersetzt `as`-Assertions durch typsichere Alternativen.

**Nur in Test-Code.** Shoehorn niemals in Produktionscode verwenden.

Probleme mit `as` in Tests:

- Angewiesen, es nicht zu verwenden
- Muss Zieltyp manuell angeben
- Double-as (`as unknown as Type`) für absichtlich falsche Daten

## Installation

```bash
npm i @total-typescript/shoehorn
```

## Migrationsmuster

### Große Objekte mit wenigen benötigten Eigenschaften

Vorher:

```ts
type Request = {
  body: { id: string };
  headers: Record<string, string>;
  cookies: Record<string, string>;
  // ...20 weitere Eigenschaften
};

it("gets user by id", () => {
  // Benötigt nur body.id, muss aber gesamten Request simulieren
  getUser({
    body: { id: "123" },
    headers: {},
    cookies: {},
    // ...alle 20 Eigenschaften simulieren
  });
});
```

Nachher:

```ts
import { fromPartial } from "@total-typescript/shoehorn";

it("gets user by id", () => {
  getUser(
    fromPartial({
      body: { id: "123" },
    }),
  );
});
```

### `as Type` → `fromPartial()`

Vorher:

```ts
getUser({ body: { id: "123" } } as Request);
```

Nachher:

```ts
import { fromPartial } from "@total-typescript/shoehorn";

getUser(fromPartial({ body: { id: "123" } }));
```

### `as unknown as Type` → `fromAny()`

Vorher:

```ts
getUser({ body: { id: 123 } } as unknown as Request); // absichtlich falscher Typ
```

Nachher:

```ts
import { fromAny } from "@total-typescript/shoehorn";

getUser(fromAny({ body: { id: 123 } }));
```

## Wann was verwenden

| Funktion        | Verwendungsfall                                           |
| --------------- | --------------------------------------------------------- |
| `fromPartial()` | Partielle Daten übergeben, die noch typchecken           |
| `fromAny()`     | Absichtlich falsche Daten übergeben (behält Autovervollständigung) |
| `fromExact()`   | Volles Objekt erzwingen (später mit fromPartial tauschen) |

## Workflow

1. **Anforderungen sammeln** - Nutzer fragen:
   - Welche Test-Dateien haben `as`-Assertions, die Probleme machen?
   - Haben sie große Objekte, bei denen nur einige Eigenschaften wichtig sind?
   - Müssen sie absichtlich falsche Daten für Fehlertests übergeben?

2. **Installieren und migrieren**:
   - [ ] Installieren: `npm i @total-typescript/shoehorn`
   - [ ] Test-Dateien mit `as`-Assertions finden: `grep -r " as [A-Z]" --include="*.test.ts" --include="*.spec.ts"`
   - [ ] `as Type` durch `fromPartial()` ersetzen
   - [ ] `as unknown as Type` durch `fromAny()` ersetzen
   - [ ] Imports von `@total-typescript/shoehorn` hinzufügen
   - [ ] Typprüfung ausführen zur Verifizierung
