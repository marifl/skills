# Interface-Design für Testbarkeit

Gute Interfaces machen Testen natürlich:

1. **Abhängigkeiten akzeptieren, nicht erstellen**

   ```typescript
   // Testbar
   function processOrder(order, paymentGateway) {}

   // Schwer zu testen
   function processOrder(order) {
     const gateway = new StripeGateway();
   }
   ```

2. **Ergebnisse zurückgeben, keine Seiteneffekte erzeugen**

   ```typescript
   // Testbar
   function calculateDiscount(cart): Discount {}

   // Schwer zu testen
   function applyDiscount(cart): void {
     cart.total -= discount;
   }
   ```

3. **Kleine Oberfläche**
   - Weniger Methoden = weniger Tests nötig
   - Weniger Parameter = einfacheres Test-Setup
