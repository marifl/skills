# Refactoring-Kandidaten

Nach dem TDD-Zyklus nach folgendem suchen:

- **Duplikation** → Funktion/Klasse extrahieren
- **Lange Methoden** → In private Helpers aufteilen (Tests auf öffentlichem Interface halten)
- **Shallow Modules** → Kombinieren oder vertiefen
- **Feature Envy** → Logik dorthin verschieben, wo die Daten leben
- **Primitive Obsession** → Value Objects einführen
- **Bestehender Code**, den der neue Code als problematisch offenbart
