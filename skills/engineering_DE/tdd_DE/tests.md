# Gute und schlechte Tests

## Gute Tests

**Integrationsstil**: Durch echte Interfaces testen, nicht Mocks von internen Teilen.

```typescript
// GUT: Testet beobachtbares Verhalten
test("user can checkout with valid cart", async () => {
  const cart = createCart();
  cart.add(product);
  const result = await checkout(cart, paymentMethod);
  expect(result.status).toBe("confirmed");
});
```

Merkmale:

- Testet Verhalten, das Nutzer/Caller wichtig ist
- Verwendet nur die öffentliche API
- Überlebt interne Refactorings
- Beschreibt WAS, nicht WIE
- Eine logische Aussage pro Test

## Schlechte Tests

**Tests von Implementierungsdetails**: An interne Struktur gekoppelt.

```typescript
// SCHLECHT: Testet Implementierungsdetails
test("checkout calls paymentService.process", async () => {
  const mockPayment = jest.mock(paymentService);
  await checkout(cart, payment);
  expect(mockPayment.process).toHaveBeenCalledWith(cart.total);
});
```

Warnsignale:

- Interne Collaborators mocken
- Private Methoden testen
- Auf Aufrufanzahl/-reihenfolge prüfen
- Test bricht bei Refactoring ohne Verhaltensänderung
- Testname beschreibt WIE, nicht WAS
- Durch externe Mittel statt Interface verifizieren

```typescript
// SCHLECHT: Umgeht Interface zur Verifizierung
test("createUser saves to database", async () => {
  await createUser({ name: "Alice" });
  const row = await db.query("SELECT * FROM users WHERE name = ?", ["Alice"]);
  expect(row).toBeDefined();
});

// GUT: Verifiziert durch Interface
test("createUser makes user retrievable", async () => {
  const user = await createUser({ name: "Alice" });
  const retrieved = await getUser(user.id);
  expect(retrieved.name).toBe("Alice");
});
```
