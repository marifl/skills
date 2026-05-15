---
name: diagnose_DE
description: Disziplinierter Diagnose-Loop für schwierige Bugs und Performance-Regressionen. Reproduce → minimise → hypothesise → instrument → fix → regression-test. Verwenden, wenn der Nutzer „diagnose this" / „debug this" sagt, einen Bug meldet, etwas als kaputt/werfend/fehlschlagend beschreibt oder eine Performance-Regression nennt.
---

# Diagnose

Eine Disziplin für schwierige Bugs. Phasen nur überspringen, wenn es explizit begründet ist.

Beim Erkunden der Codebasis das domänenspezifische Glossar des Projekts nutzen, um ein klares mentales Modell der relevanten Module zu bekommen, und ADRs in dem Bereich lesen, den man berührt.

## Phase 1 — Feedback Loop aufbauen

**Das ist die eigentliche Kunst.** Alles andere ist Mechanik. Wenn man ein schnelles, deterministisches, agent-ausführbares Pass/Fail-Signal für den Bug hat, findet man die Ursache — Bisection, Hypothesentests und Instrumentierung konsumieren dieses Signal einfach. Ohne dieses Signal hilft kein Codestudium.

Disproportional viel Aufwand hier investieren. **Aggressiv vorgehen. Kreativ sein. Nicht aufgeben.**

### Möglichkeiten, einen Loop zu bauen — in etwa dieser Reihenfolge ausprobieren

1. **Fehlschlagender Test** an welchem Seam auch immer den Bug erreicht — Unit, Integration, e2e.
2. **Curl / HTTP-Skript** gegen einen laufenden Dev-Server.
3. **CLI-Aufruf** mit einer Fixture-Eingabe, stdout gegen einen bekannten Snapshot vergleichen.
4. **Headless-Browser-Skript** (Playwright / Puppeteer) — treibt die UI, prüft DOM/Console/Network.
5. **Aufgezeichnete Trace wiederspielen.** Eine echte Netzwerkanfrage / Payload / Event-Log auf Disk speichern; durch den Code-Pfad in Isolation wiederspielen.
6. **Wegwerfbarer Harness.** Eine minimale Teilmenge des Systems (ein Service, gemockte Abhängigkeiten) hochfahren, die den Bug-Codepfad mit einem einzigen Funktionsaufruf ausübt.
7. **Property / Fuzz-Loop.** Wenn der Bug „manchmal falscher Output" ist, 1000 zufällige Eingaben laufen lassen und nach dem Fehlermuster suchen.
8. **Bisection-Harness.** Wenn der Bug zwischen zwei bekannten Zuständen (Commit, Datensatz, Version) aufgetreten ist, „am Zustand X starten, prüfen, wiederholen" automatisieren, damit man `git bisect run` ausführen kann.
9. **Differential-Loop.** Dieselbe Eingabe durch alte Version vs. neue Version (oder zwei Konfigurationen) laufen lassen und Outputs vergleichen.
10. **HITL-Bash-Skript.** Letztes Mittel. Wenn ein Mensch klicken muss, _diesen_ mit `scripts/hitl-loop.template.sh` führen, damit der Loop dennoch strukturiert ist. Erfasster Output fließt zurück.

Den richtigen Feedback Loop aufbauen, und der Bug ist zu 90 % behoben.

### Den Loop selbst iterieren

Den Loop wie ein Produkt behandeln. Sobald man _einen_ Loop hat, fragen:

- Kann ich ihn schneller machen? (Setup cachen, irrelevante Init überspringen, Test-Scope einengen.)
- Kann ich das Signal schärfer machen? (Genau auf das spezifische Symptom prüfen, nicht auf „ist nicht abgestürzt".)
- Kann ich ihn deterministischer machen? (Zeit pinnen, RNG seeden, Filesystem isolieren, Netzwerk einfrieren.)

Ein 30-sekündiger flakiger Loop ist kaum besser als gar kein Loop. Ein 2-sekündiger deterministischer Loop ist eine Debugging-Superkraft.

### Nicht-deterministische Bugs

Das Ziel ist keine saubere Reproduktion, sondern eine **höhere Reproduktionsrate**. Den Trigger 100× loopen, parallelisieren, Stress hinzufügen, Timing-Fenster einengen, Sleeps injizieren. Ein 50%-Flake-Bug ist debuggbar; 1% ist es nicht — die Rate erhöhen, bis er debuggbar ist.

### Wenn man keinen Loop aufbauen kann

Stoppen und das explizit sagen. Auflisten, was versucht wurde. Den Nutzer fragen nach: (a) Zugang zu welcher Umgebung auch immer das reproduziert, (b) einem erfassten Artefakt (HAR File, Log-Dump, Core Dump, Bildschirmaufnahme mit Zeitstempeln), oder (c) Erlaubnis, temporäre Produktions-Instrumentierung hinzuzufügen. **Nicht** mit Hypothesen weitermachen ohne Loop.

Nicht zu Phase 2 übergehen, bis man einen Loop hat, dem man vertraut.

## Phase 2 — Reproduzieren

Den Loop ausführen. Den Bug erscheinen sehen.

Bestätigen:

- [ ] Der Loop erzeugt den Fehlermodus, den der **Nutzer** beschrieben hat — nicht einen anderen Fehler, der zufällig in der Nähe ist. Falscher Bug = falsche Lösung.
- [ ] Der Fehler ist über mehrere Läufe hinweg reproduzierbar (oder, für nicht-deterministische Bugs, mit ausreichend hoher Rate reproduzierbar).
- [ ] Das genaue Symptom wurde erfasst (Fehlermeldung, falscher Output, langes Timing), damit spätere Phasen prüfen können, ob der Fix es tatsächlich adressiert.

Nicht weitermachen, bis der Bug reproduziert ist.

## Phase 3 — Hypothesen aufstellen

**3–5 gerankte Hypothesen** generieren, bevor irgendeine getestet wird. Einzelne Hypothesen ankern auf der ersten plausiblen Idee.

Jede Hypothese muss **falsifizierbar** sein: die Vorhersage, die sie macht, formulieren.

> Format: „Wenn <X> die Ursache ist, dann wird <Y ändern> den Bug verschwinden lassen / <Z ändern> ihn verschlimmern."

Wenn man die Vorhersage nicht formulieren kann, ist die Hypothese ein Bauchgefühl — verwerfen oder schärfen.

**Die gerankte Liste dem Nutzer zeigen, bevor man testet.** Sie haben oft Domänenwissen, das sofort umrankt („wir haben gerade eine Änderung an #3 deployed"), oder kennen Hypothesen, die sie bereits ausgeschlossen haben. Günstiger Checkpoint, große Zeitersparnis. Nicht blockieren — mit eigener Rangfolge weitermachen, wenn der Nutzer AFK ist.

## Phase 4 — Instrumentieren

Jede Sonde muss einer spezifischen Vorhersage aus Phase 3 entsprechen. **Eine Variable auf einmal ändern.**

Tool-Präferenz:

1. **Debugger / REPL-Inspektion**, wenn die Umgebung es unterstützt. Ein Breakpoint schlägt zehn Logs.
2. **Gezielte Logs** an den Grenzen, die Hypothesen unterscheiden.
3. Niemals „alles loggen und grep".

**Jeden Debug-Log** mit einem eindeutigen Präfix taggen, z. B. `[DEBUG-a4f2]`. Aufräumen am Ende ist ein einzelnes grep. Ungetaggte Logs überleben; getaggte sterben.

**Perf-Zweig.** Bei Performance-Regressionen sind Logs meist falsch. Stattdessen: eine Baseline-Messung erstellen (Timing-Harness, `performance.now()`, Profiler, Query-Plan), dann bisektieren. Erst messen, dann fixen.

## Phase 5 — Fixen + Regressionstest

Den Regressionstest **vor dem Fix** schreiben — aber nur, wenn es einen **korrekten Seam** dafür gibt.

Ein korrekter Seam ist einer, wo der Test das **echte Bug-Muster** so ausübt, wie es am Call-Site auftritt. Wenn der einzige verfügbare Seam zu flach ist (Single-Caller-Test, wenn der Bug mehrere Caller braucht; Unit-Test, der die Kette, die den Bug ausgelöst hat, nicht replizieren kann), gibt ein Regressionstest dort falsches Vertrauen.

**Wenn kein korrekter Seam existiert, ist das selbst der Befund.** Notieren. Die Codebase-Architektur verhindert, dass der Bug gesperrt wird. Das für die nächste Phase vormerken.

Wenn ein korrekter Seam existiert:

1. Die minimierte Reproduktion in einen fehlschlagenden Test an diesem Seam umwandeln.
2. Fehlschlagen beobachten.
3. Den Fix anwenden.
4. Bestehen beobachten.
5. Den Phase-1-Feedback-Loop gegen das ursprüngliche (nicht-minimierte) Szenario erneut ausführen.

## Phase 6 — Aufräumen + Post-Mortem

Pflicht vor der Fertigstellung:

- [ ] Ursprüngliche Reproduktion reproduziert sich nicht mehr (Phase-1-Loop erneut ausführen)
- [ ] Regressionstest besteht (oder fehlende Seam ist dokumentiert)
- [ ] Alle `[DEBUG-...]`-Instrumentierungen entfernt (Präfix `grep`-pen)
- [ ] Wegwerfprototypen gelöscht (oder an einen klar markierten Debug-Ort verschoben)
- [ ] Die Hypothese, die sich als richtig erwiesen hat, im Commit / PR-Kommentar festgehalten — damit der nächste Debugger lernt

**Dann fragen: Was hätte diesen Bug verhindert?** Wenn die Antwort eine Architekturänderung beinhaltet (kein guter Test-Seam, verschränkte Caller, versteckte Kopplung), an den Skill `/improve-codebase-architecture` mit den Einzelheiten übergeben. Die Empfehlung **nach** dem Fix machen, nicht davor — man hat jetzt mehr Informationen als zu Beginn.
