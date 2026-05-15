# Wann mocken

Nur an **Systemgrenzen** mocken:

- Externe APIs (Zahlung, E-Mail, etc.)
- Datenbanken (manchmal — Test-DB bevorzugen)
- Zeit/Zufälligkeit
- Filesystem (manchmal)

Nicht mocken:

- Eigene Klassen/Module
- Interne Collaborators
- Alles, was man kontrolliert

## Für Mockability designen

An Systemgrenzen Interfaces designen, die einfach zu mocken sind:

**1. Dependency Injection verwenden**

Externe Abhängigkeiten reinreichen statt intern erstellen:

```typescript
// Einfach zu mocken
function processPayment(order, paymentClient) {
  return paymentClient.charge(order.total);
}

// Schwer zu mocken
function processPayment(order) {
  const client = new StripeClient(process.env.STRIPE_KEY);
  return client.charge(order.total);
}
```

**2. SDK-style Interfaces über generische Fetcher bevorzugen**

Spezifische Funktionen für jede externe Operation erstellen statt einer generischen Funktion mit konditionaler Logik:

```typescript
// GUT: Jede Funktion ist unabhängig mockbar
const api = {
  getUser: (id) => fetch(`/users/${id}`),
  getOrders: (userId) => fetch(`/users/${userId}/orders`),
  createOrder: (data) => fetch('/orders', { method: 'POST', body: data }),
};

// SCHLECHT: Mocken erfordert konditionaleLogik innerhalb des Mocks
const api = {
  fetch: (endpoint, options) => fetch(endpoint, options),
};
```

Der SDK-Ansatz bedeutet:
- Jeder Mock gibt eine spezifische Form zurück
- Keine konditionaleLogik im Test-Setup
- Leichter zu sehen, welche Endpoints ein Test ausübt
- Typsicherheit pro Endpoint
