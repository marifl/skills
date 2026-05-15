---
name: tdd_DE
description: Test-driven Development mit Red-Green-Refactor-Loop. Verwenden, wenn der Nutzer Funktionen bauen oder Bugs mit TDD beheben möchte, „red-green-refactor" erwähnt, Integrationstests möchte oder Test-First-Development anfragt.
---

# Test-Driven Development

## Philosophie

**Kernprinzip**: Tests sollten Verhalten durch öffentliche Interfaces überprüfen, nicht Implementierungsdetails. Code kann sich vollständig ändern; Tests sollten es nicht.

**Gute Tests** sind integrationsstil: Sie üben echte Code-Pfade durch öffentliche APIs aus. Sie beschreiben _was_ das System tut, nicht _wie_ es es tut. Ein guter Test liest sich wie eine Spezifikation — „user can checkout with valid cart" sagt genau, welche Fähigkeit existiert. Diese Tests überleben Refactorings, weil sie sich nicht um die interne Struktur kümmern.

**Schlechte Tests** sind an die Implementierung gekoppelt. Sie mocken interne Collaborators, testen private Methoden oder verifizieren durch externe Mittel (z. B. Datenbank direkt abfragen statt Interface zu nutzen). Warnsignal: Der Test bricht bei Refactoring, aber das Verhalten hat sich nicht geändert. Wenn eine interne Funktion umbenannt wird und Tests fehlschlagen, testeten diese Tests Implementation, nicht Verhalten.

Beispiele und Mocking-Richtlinien in [tests.md](tests.md) und [mocking.md](mocking.md).

## Anti-Pattern: Horizontale Slices

**NICHT alle Tests zuerst schreiben, dann alle Implementierungen.** Das ist „horizontales Slicen" — RED als „alle Tests schreiben" und GREEN als „allen Code schreiben" behandeln.

Das erzeugt **schlechte Tests**:

- Tests, die massenweise geschrieben wurden, testen _vorgestelltes_ Verhalten, nicht _tatsächliches_ Verhalten
- Man testet am Ende die _Form_ der Dinge (Datenstrukturen, Funktionssignaturen) statt des nutzerorientierten Verhaltens
- Tests werden unempfindlich für echte Änderungen — sie bestehen, wenn Verhalten bricht, schlagen fehl wenn Verhalten in Ordnung ist
- Man überholt seine Scheinwerfer und committed sich auf Teststruktur, bevor man die Implementierung versteht

**Richtiger Ansatz**: Vertikale Slices via Tracer Bullets. Ein Test → eine Implementierung → wiederholen. Jeder Test reagiert auf das, was vom vorherigen Zyklus gelernt wurde. Weil der Code gerade geschrieben wurde, weiß man genau, welches Verhalten wichtig ist und wie man es verifiziert.

```
FALSCH (horizontal):
  RED:   test1, test2, test3, test4, test5
  GREEN: impl1, impl2, impl3, impl4, impl5

RICHTIG (vertikal):
  RED→GREEN: test1→impl1
  RED→GREEN: test2→impl2
  RED→GREEN: test3→impl3
  ...
```

## Workflow

### 1. Planung

Beim Erkunden der Codebasis das Domänenglossary des Projekts nutzen, damit Testnamen und Interface-Vokabular der Projektsprache entsprechen, und ADRs in dem Bereich respektieren, den man berührt.

Vor dem Schreiben von Code:

- [ ] Mit dem Nutzer bestätigen, welche Interface-Änderungen nötig sind
- [ ] Mit dem Nutzer bestätigen, welche Verhaltensweisen getestet werden (priorisieren)
- [ ] Möglichkeiten für [Deep Modules](deep-modules.md) identifizieren (kleines Interface, tiefe Implementation)
- [ ] Interfaces für [Testbarkeit](interface-design.md) designen
- [ ] Die zu testenden Verhaltensweisen auflisten (keine Implementierungsschritte)
- [ ] Nutzer-Genehmigung für den Plan einholen

Fragen: „Wie soll das öffentliche Interface aussehen? Welche Verhaltensweisen sind am wichtigsten zu testen?"

**Man kann nicht alles testen.** Mit dem Nutzer genau bestätigen, welche Verhaltensweisen am meisten zählen. Testaufwand auf kritische Pfade und komplexe Logik konzentrieren, nicht auf jeden möglichen Edge Case.

### 2. Tracer Bullet

EINEN Test schreiben, der EINE Sache über das System bestätigt:

```
RED:   Test für erstes Verhalten schreiben → Test schlägt fehl
GREEN: Minimalen Code schreiben, damit er besteht → Test besteht
```

Das ist der Tracer Bullet — beweist, dass der Pfad end-to-end funktioniert.

### 3. Inkrementeller Loop

Für jedes verbleibende Verhalten:

```
RED:   Nächsten Test schreiben → schlägt fehl
GREEN: Minimaler Code zum Bestehen → besteht
```

Regeln:

- Einen Test auf einmal
- Nur genug Code, um den aktuellen Test zu bestehen
- Keine zukünftigen Tests antizipieren
- Tests auf beobachtbares Verhalten fokussiert halten

### 4. Refactor

Nachdem alle Tests bestehen, nach [Refactoring-Kandidaten](refactoring.md) suchen:

- [ ] Duplikation extrahieren
- [ ] Module vertiefen (Komplexität hinter einfache Interfaces verschieben)
- [ ] SOLID-Prinzipien anwenden, wo natürlich
- [ ] Überlegen, was neuer Code über bestehenden Code offenbart
- [ ] Tests nach jedem Refactoring-Schritt ausführen

**Niemals refactoren, wenn RED.** Erst zu GREEN kommen.

## Checkliste pro Zyklus

```
[ ] Test beschreibt Verhalten, nicht Implementation
[ ] Test verwendet nur öffentliche Interfaces
[ ] Test würde internes Refactoring überleben
[ ] Code ist minimal für diesen Test
[ ] Keine spekulativen Funktionen hinzugefügt
```
