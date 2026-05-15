# Interface-Design

Wenn der Nutzer alternative Interfaces für einen gewählten Vertiefungskandidaten erkunden möchte, dieses parallele Sub-Agent-Muster verwenden. Basiert auf „Design It Twice" (Ousterhout) — die erste Idee ist unwahrscheinlich die beste.

Verwendet das Vokabular aus [LANGUAGE.md](LANGUAGE.md) — **module**, **interface**, **seam**, **adapter**, **leverage**.

## Prozess

### 1. Den Problemraum einrahmen

Vor dem Starten von Sub-Agents eine nutzerorientierte Erklärung des Problemraums für den gewählten Kandidaten schreiben:

- Die Einschränkungen, die jedes neue Interface erfüllen müsste
- Die Abhängigkeiten, auf die es sich stützen würde, und in welche Kategorie sie fallen (siehe [DEEPENING.md](DEEPENING.md))
- Eine grobe, illustrative Code-Skizze, um die Einschränkungen zu verankern — kein Vorschlag, nur eine Möglichkeit, die Einschränkungen konkret zu machen

Dem Nutzer zeigen, dann sofort zu Schritt 2 übergehen. Der Nutzer liest und denkt nach, während die Sub-Agents parallel arbeiten.

### 2. Sub-Agents starten

3+ Sub-Agents parallel mit dem Agent-Tool starten. Jeder muss ein **radikal anderes** Interface für das vertiefte Module produzieren.

Jeden Sub-Agent mit einem separaten technischen Brief versorgen (Dateipfade, Kopplungsdetails, Abhängigkeitskategorie aus [DEEPENING.md](DEEPENING.md), was hinter dem Seam liegt). Der Brief ist unabhängig von der nutzerorientierten Problemraumdarstellung in Schritt 1. Jedem Agent eine andere Design-Einschränkung geben:

- Agent 1: „Das Interface minimieren — maximal 1–3 Einstiegspunkte anstreben. Leverage pro Einstiegspunkt maximieren."
- Agent 2: „Flexibilität maximieren — viele Use Cases und Erweiterungen unterstützen."
- Agent 3: „Für den häufigsten Caller optimieren — den Standardfall trivial machen."
- Agent 4 (falls zutreffend): „Um Ports & Adapters für seam-übergreifende Abhängigkeiten herum designen."

Sowohl [LANGUAGE.md](LANGUAGE.md)-Vokabular als auch CONTEXT.md-Vokabular in den Brief einbeziehen, damit jeder Sub-Agent Dinge konsistent mit der Architektursprache und der Domänensprache des Projekts benennt.

Jeder Sub-Agent gibt aus:

1. Interface (Typen, Methoden, Parameter — plus Invarianten, Reihenfolge, Fehlermodi)
2. Verwendungsbeispiel, das zeigt, wie Caller es nutzen
3. Was die Implementation hinter dem Seam verbirgt
4. Abhängigkeitsstrategie und Adapter (siehe [DEEPENING.md](DEEPENING.md))
5. Trade-offs — wo Leverage hoch ist, wo sie dünn ist

### 3. Präsentieren und vergleichen

Designs sequenziell präsentieren, damit der Nutzer jedes absorbieren kann, dann in Prosa vergleichen. Kontrast nach **depth** (Leverage am Interface), **locality** (wo Änderungen sich konzentrieren) und **seam placement**.

Nach dem Vergleich eine eigene Empfehlung geben: welches Design am stärksten erscheint und warum. Wenn Elemente aus verschiedenen Designs gut kombiniert würden, ein Hybrid vorschlagen. Meinungsstark sein — der Nutzer möchte eine starke Einschätzung, kein Menü.
