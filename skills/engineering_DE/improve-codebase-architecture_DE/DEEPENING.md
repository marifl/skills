# Deepening

Wie man ein Cluster flacher Module sicher vertieft, angesichts seiner Abhängigkeiten. Setzt das Vokabular aus [LANGUAGE.md](LANGUAGE.md) voraus — **module**, **interface**, **seam**, **adapter**.

## Abhängigkeitskategorien

Beim Beurteilen eines Kandidaten für Deepening seine Abhängigkeiten klassifizieren. Die Kategorie bestimmt, wie das vertiefte Module über seinen Seam hinweg getestet wird.

### 1. In-process

Pure Berechnung, In-Memory-Zustand, kein I/O. Immer vertiefbar — die Module zusammenführen und direkt durch das neue Interface testen. Kein Adapter nötig.

### 2. Lokal substituierbar

Abhängigkeiten, die lokale Test-Standbys haben (PGLite für Postgres, In-Memory-Filesystem). Vertiefbar, wenn der Standb existiert. Das vertiefte Module wird mit dem laufenden Standb im Test-Suite getestet. Der Seam ist intern; kein Port am externen Interface des Moduls.

### 3. Remote aber owned (Ports & Adapters)

Eigene Services über eine Netzwerkgrenze hinweg (Microservices, interne APIs). Einen **Port** (Interface) am Seam definieren. Das tiefe Module besitzt die Logik; der Transport wird als **Adapter** injiziert. Tests verwenden einen In-Memory-Adapter. Produktion verwendet einen HTTP/gRPC/Queue-Adapter.

Empfehlungsform: *„Einen Port am Seam definieren, einen HTTP-Adapter für Produktion und einen In-Memory-Adapter für Tests implementieren, damit die Logik in einem tiefen Module sitzt, auch wenn es über ein Netzwerk deployed wird."*

### 4. Wirklich extern (Mock)

Drittanbieter-Services (Stripe, Twilio, etc.), die man nicht kontrolliert. Das vertiefte Module nimmt die externe Abhängigkeit als injizierten Port; Tests stellen einen Mock-Adapter bereit.

## Seam-Disziplin

- **Ein Adapter bedeutet einen hypothetischen Seam. Zwei Adapter bedeuten einen echten.** Keinen Port einführen, es sei denn, mindestens zwei Adapter sind gerechtfertigt (typischerweise Produktion + Test). Ein Single-Adapter-Seam ist nur Indirektion.
- **Interne Seams vs. externe Seams.** Ein tiefes Module kann interne Seams haben (privat für seine Implementation, von seinen eigenen Tests verwendet) sowie den externen Seam an seinem Interface. Interne Seams nicht durch das Interface exponieren, nur weil Tests sie verwenden.

## Test-Strategie: ersetzen, nicht schichten

- Alte Unit-Tests auf flachen Modulen werden überflüssig, sobald Tests am Interface des vertieften Moduls existieren — löschen.
- Neue Tests am Interface des vertieften Moduls schreiben. **Das Interface ist die Test-Oberfläche.**
- Tests auf beobachtbare Ergebnisse durch das Interface prüfen, nicht auf internen Zustand.
- Tests sollten interne Refactorings überleben — sie beschreiben Verhalten, nicht Implementation. Wenn ein Test bei Änderung der Implementation geändert werden muss, testet er am Interface vorbei.
