# Logik-Prototype

Eine kleine interaktive Terminal-App, die den Nutzer ein Zustandsmodell per Hand steuern lässt. Diesen Ast verwenden, wenn die Frage **Business-Logik, Zustandsübergänge oder Daten-Shape** betrifft — die Art von Dingen, die auf Papier vernünftig klingen, aber erst falsch wirken, wenn man sie durch echte Fälle treibt.

## Wann das die richtige Form ist

- „Ich bin nicht sicher, ob diese State Machine den Edge Case behandelt, bei dem X dann Y."
- „Erlaubt dieses Datenmodell tatsächlich den Fall, bei dem..."
- „Ich möchte herausfinden, wie das API aussehen soll, bevor ich es schreibe."
- Alles, wo der Nutzer **Buttons drücken und den Zustand sich ändern sehen** möchte.

Wenn die Frage „Wie soll das aussehen?" ist — falscher Ast. [UI.md](UI.md) verwenden.

## Prozess

### 1. Die Frage formulieren

Vor dem Schreiben von Code aufschreiben, welches Zustandsmodell und welche Frage prototypisiert wird. Ein Absatz, im README des Prototypes oder als Kommentar am Anfang der Datei. Ein Logik-Prototype, der die falsche Frage beantwortet, ist purer Verschwendung — die Frage explizit machen, damit sie später geprüft werden kann, egal ob der Nutzer jetzt zuschaut oder AFK zurückkommt.

### 2. Die Sprache wählen

Das verwenden, was das Host-Projekt verwendet. Wenn das Projekt keinen offensichtlichen Runtime hat (z. B. ein Docs-Repo), fragen.

Die bestehenden Konventionen des Projekts für Tooling beachten — keinen neuen Package-Manager oder Runtime nur für den Prototype hinzufügen.

### 3. Die Logik in einem portablen Module isolieren

Die eigentliche Logik — das Bit, das die Frage beantwortet — hinter ein kleines, reines Interface stecken, das später herausgenommen und in die echte Codebasis eingebracht werden könnte. Das TUI drumherum ist Wegwurf; das Logik-Module sollte es nicht sein.

Die richtige Form hängt von der Frage ab:

- **Ein reiner Reducer** — `(state, action) => state`. Gut wenn Aktionen diskrete Events sind und der Zustand ein einzelner Wert ist.
- **Eine State Machine** — explizite Zustände und Übergänge. Gut wenn „welche Aktionen sind gerade überhaupt legal?" Teil der Frage ist.
- **Ein kleines Set reiner Funktionen** über einem einfachen Datentyp. Gut wenn kein impliziter aktueller Zustand vorhanden ist — nur Transformationen.
- **Eine Klasse oder ein Module mit einer klaren Methodenoberfläche**, wenn die Logik wirklich laufenden internen Zustand besitzt.

Die Form wählen, die am besten zur gestellten Frage passt, *nicht* diejenige, die am einfachsten an eine TUI angebunden werden kann. Rein halten: kein I/O, kein Terminal-Code, kein `console.log` für Control Flow. Die TUI importiert es und ruft es auf; nichts fließt in die andere Richtung.

Das macht den Prototype über seine eigene Lebensdauer hinaus nützlich. Wenn die Frage beantwortet wurde, kann der validierte Reducer / die Maschine / das Funktionsset in das echte Module gehoben werden — die TUI-Shell wird gelöscht.

### 4. Das kleinste TUI bauen, das den Zustand sichtbar macht

Als **leichtes TUI** bauen — bei jedem Tick den Bildschirm löschen (`console.clear()` / `print("\033[2J\033[H")` / äquivalent) und den gesamten Frame neu rendern. Der Nutzer sollte immer eine stabile Ansicht sehen, kein immer größer werdendes Scrollback.

Jeder Frame hat zwei Teile, in dieser Reihenfolge:

1. **Aktueller Zustand**, schön ausgedruckt und diff-freundlich (ein Feld pro Zeile oder formatiertes JSON). **Fett** für Feldnamen oder Abschnittsüberschriften und **gedimmt** für weniger wichtigen Kontext (Zeitstempel, IDs, abgeleitete Werte). Native ANSI-Escape-Codes sind in Ordnung — `\x1b[1m` fett, `\x1b[2m` gedimmt, `\x1b[0m` zurücksetzen. Keine Styling-Bibliothek nötig, wenn keine bereits im Projekt ist.
2. **Tastenkürzel**, am unteren Ende aufgelistet: `[a] add user  [d] delete user  [t] tick clock  [q] quit`. Die Taste fett, die Beschreibung gedimmt, oder umgekehrt — was auch immer sauber lesbar ist.

Verhalten:

1. **Zustand initialisieren** — ein einzelnes In-Memory-Objekt/Struct. Ersten Frame beim Start rendern.
2. **Einen Tastendruck (oder eine Zeile)** auf einmal lesen, an einen Handler weiterleiten, der den Zustand mutiert.
3. **Den vollständigen Frame nach jeder Aktion neu rendern** — nicht anhängen, ersetzen.
4. **Bis zum Beenden loopen.**

Der gesamte Frame sollte auf einen Bildschirm passen.

### 5. In einem Befehl ausführbar machen

Ein Script zum bestehenden Task-Runner des Projekts hinzufügen (`package.json`-Scripts, `Makefile`, `justfile`, `pyproject.toml`). Der Nutzer sollte `pnpm run <prototype-name>` oder äquivalent ausführen — niemals einen Pfad merken müssen.

Wenn das Host-Projekt keinen Task-Runner hat, den Befehl einfach oben im README des Prototypes platzieren.

### 6. Übergeben

Dem Nutzer den Ausführbefehl geben. Er wird es selbst steuern; die interessanten Momente sind, wenn er sagt „warte, das sollte nicht möglich sein" oder „hm, ich hatte angenommen, X wäre anders" — das sind die Bugs in der _Idee_, was der ganze Sinn ist. Wenn er neue Aktionen hinzugefügt haben möchte, hinzufügen. Prototypes entwickeln sich.

### 7. Die Antwort erfassen

Wenn der Prototype seine Arbeit getan hat, ist die Antwort auf die Frage das einzige Erhaltenswerte. Wenn der Nutzer verfügbar ist, fragen, was er gelernt hat. Wenn nicht, eine `NOTES.md` neben dem Prototype hinterlassen, damit die Antwort eingetragen werden kann (oder von einem selbst, wenn die Session mitverfolgt wurde), bevor der Prototype gelöscht wird.

## Anti-Patterns

- **Keine Tests hinzufügen.** Ein Prototype, der Tests braucht, ist kein Prototype mehr.
- **Nicht an die echte Datenbank anschließen.** Einen In-Memory-Store verwenden, es sei denn, die Frage betrifft speziell Persistenz.
- **Nicht verallgemeinern.** Kein „was wenn wir X später unterstützen wollten." Der Prototype beantwortet eine Frage.
- **Logik und TUI nicht verwischen.** Wenn der Reducer / die State Machine auf `console.log`, Eingabeaufforderungen oder Terminal-Escape-Codes referenziert, ist er nicht mehr portabel. Das TUI als dünne Shell über einem reinen Module halten.
- **Die TUI-Shell nicht in Produktion bringen.** Die Shell ist für das manuelle Treiben vom Terminal aus optimiert. Das Logik-Module dahinter ist das erhaltenswerte Bit.
