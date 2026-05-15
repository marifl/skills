---
name: prototype_DE
description: Einen wegwerfbaren Prototype bauen, um ein Design auszuloten, bevor man sich daran committet. Verzweigt zwischen zwei Ästen — einer ausführbaren Terminal-App für Zustands-/Business-Logic-Fragen oder mehreren radikal verschiedenen UI-Variationen, die von einer Route umschaltbar sind. Verwenden, wenn der Nutzer prototypen, ein Datenmodell oder eine State Machine sanity-checken, eine UI mocken, Design-Optionen erkunden möchte oder „prototype this", „let me play with it", „try a few designs" sagt.
---

# Prototype

Ein Prototype ist **Wegwerfcode, der eine Frage beantwortet**. Die Frage bestimmt die Form.

## Einen Ast wählen

Identifizieren, welche Frage beantwortet wird — aus dem Prompt des Nutzers, dem umgebenden Code oder durch Fragen, wenn der Nutzer verfügbar ist:

- **„Fühlt sich diese Logik / dieses Zustandsmodell richtig an?"** → [LOGIC.md](LOGIC.md). Eine kleine interaktive Terminal-App bauen, die die State Machine durch Fälle treibt, die auf Papier schwer zu durchdenken sind.
- **„Wie soll das aussehen?"** → [UI.md](UI.md). Mehrere radikal verschiedene UI-Variationen auf einer einzigen Route generieren, umschaltbar per URL-Suchparameter und einer schwebenden unteren Leiste.

Die zwei Äste produzieren sehr unterschiedliche Artefakte — das falsch zu bekommen, verschwendet den ganzen Prototype. Wenn die Frage wirklich mehrdeutig ist und der Nutzer nicht erreichbar ist, standardmäßig auf den Ast zurückfallen, der besser zum umgebenden Code passt (ein Backend-Module → Logik; eine Seite oder Komponente → UI) und die Annahme am Anfang des Prototypes formulieren.

## Regeln, die für beide gelten

1. **Von Anfang an Wegwurf, und klar als solcher markiert.** Den Prototype-Code nah an dem platzieren, wo er tatsächlich verwendet wird (neben dem Module oder der Seite, für die er prototypisiert), damit der Kontext offensichtlich ist — aber so benennen, dass ein zufälliger Leser sieht, dass es ein Prototype ist, keine Produktion. Bei Wegwerf-UI-Routen die bereits im Projekt verwendete Routing-Konvention befolgen; keine neue Top-Level-Struktur erfinden.
2. **Ein Befehl zum Ausführen.** Was auch immer der bestehende Task-Runner des Projekts unterstützt — `pnpm <name>`, `python <path>`, `bun <path>`, etc. Der Nutzer muss in der Lage sein, ihn ohne Nachdenken zu starten.
3. **Standardmäßig keine Persistenz.** Zustand lebt im Speicher. Persistenz ist das Ding, das der Prototype _prüft_, nicht etwas, von dem er abhängen sollte. Wenn die Frage explizit eine Datenbank beinhaltet, eine Scratch-DB oder eine lokale Datei mit einem klaren „PROTOTYPE — wipe me"-Namen verwenden.
4. **Polishing überspringen.** Keine Tests, kein Error-Handling außer was den Prototype _ausführbar_ macht, keine Abstraktionen. Ziel ist es, schnell etwas zu lernen und es dann zu löschen.
5. **Den Zustand sichtbar machen.** Nach jeder Aktion (Logik) oder bei jedem Variantenwechsel (UI) den vollständigen relevanten Zustand ausgeben oder rendern, damit der Nutzer sieht, was sich geändert hat.
6. **Löschen oder absorbieren wenn fertig.** Wenn der Prototype seine Frage beantwortet hat, entweder löschen oder die validierte Entscheidung in den echten Code einfließen lassen — ihn nicht im Repo verrotten lassen.

## Wenn fertig

Die _Antwort_ ist das einzige Erhaltenswerte von einem Prototype. Sie irgendwo dauerhaft erfassen (Commit-Message, ADR, Issue oder eine `NOTES.md` neben dem Prototype) zusammen mit der Frage, die sie beantwortete. Wenn der Nutzer verfügbar ist, ist diese Erfassung ein kurzes Gespräch; wenn nicht, den Platzhalter hinterlassen, damit er (oder man selbst, beim nächsten Mal) das Urteil eintragen kann, bevor der Prototype gelöscht wird.
