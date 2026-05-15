---
name: setup-matt-pocock-skills_DE
description: Richtet einen `## Agent skills`-Block in AGENTS.md/CLAUDE.md und `docs/agents/` ein, damit die Engineering-Skills den Issue-Tracker dieses Repos (GitHub oder lokales Markdown), das Triage-Label-Vokabular und das Domain-Doc-Layout kennen. Vor der ersten Verwendung von `to-issues`, `to-prd`, `triage`, `diagnose`, `tdd`, `improve-codebase-architecture` oder `zoom-out` ausführen — oder wenn diese Skills offenbar keinen Kontext über den Issue-Tracker, Triage-Labels oder Domain-Docs haben.
disable-model-invocation: true
---

# Matt Pocock's Skills einrichten

Die Repo-spezifische Konfiguration aufbauen, die die Engineering-Skills voraussetzen:

- **Issue-Tracker** — wo Issues leben (standardmäßig GitHub; lokales Markdown wird out-of-the-box ebenfalls unterstützt)
- **Triage-Labels** — die Strings für die fünf kanonischen Triage-Rollen
- **Domain-Docs** — wo `CONTEXT.md` und ADRs leben und die Konsumregeln für deren Nutzung

Das ist ein prompt-gesteuerter Skill, kein deterministisches Skript. Erkunden, präsentieren was gefunden wurde, mit dem Nutzer bestätigen, dann schreiben.

## Prozess

### 1. Erkunden

Den aktuellen Repo-Stand verstehen. Was vorhanden ist lesen; nichts annehmen:

- `git remote -v` und `.git/config` — ist das ein GitHub-Repo? Welches?
- `AGENTS.md` und `CLAUDE.md` im Repo-Root — existiert eines davon? Gibt es bereits einen `## Agent skills`-Abschnitt in einem davon?
- `CONTEXT.md` und `CONTEXT-MAP.md` im Repo-Root
- `docs/adr/` und alle `src/*/docs/adr/`-Verzeichnisse
- `docs/agents/` — existiert der frühere Output dieses Skills bereits?
- `.scratch/` — Zeichen, dass eine Local-Markdown-Issue-Tracker-Konvention bereits in Verwendung ist

### 2. Befunde präsentieren und fragen

Zusammenfassen, was vorhanden ist und was fehlt. Dann den Nutzer durch die drei Entscheidungen **einzeln** führen — einen Abschnitt präsentieren, die Antwort des Nutzers einholen, dann zum nächsten übergehen. Nicht alle drei auf einmal ausgeben.

Annehmen, dass der Nutzer nicht weiß, was diese Begriffe bedeuten. Jeder Abschnitt beginnt mit einer kurzen Erklärung (was es ist, warum diese Skills es brauchen, was sich ändert, wenn sie anders wählen). Dann die Optionen und die Voreinstellung zeigen.

**Abschnitt A — Issue-Tracker.**

> Erklärung: Der „Issue-Tracker" ist, wo Issues für diesen Repo leben. Skills wie `to-issues`, `triage`, `to-prd` und `qa` lesen davon und schreiben dorthin — sie müssen wissen, ob sie `gh issue create` aufrufen, eine Markdown-Datei unter `.scratch/` schreiben oder einem anderen Workflow folgen sollen. Den Ort wählen, an dem für dieses Repo tatsächlich Arbeit verfolgt wird.

Voreingestellte Haltung: Diese Skills wurden für GitHub designed. Wenn ein `git remote` auf GitHub zeigt, das vorschlagen. Wenn ein `git remote` auf GitLab zeigt (`gitlab.com` oder ein selbst gehosteter Host), GitLab vorschlagen. Sonst (oder wenn der Nutzer es bevorzugt) anbieten:

- **GitHub** — Issues leben in den GitHub Issues des Repos (verwendet das `gh` CLI)
- **GitLab** — Issues leben in den GitLab Issues des Repos (verwendet das [`glab`](https://gitlab.com/gitlab-org/cli) CLI)
- **Local markdown** — Issues leben als Dateien unter `.scratch/<feature>/` in diesem Repo (gut für Einzelprojekte oder Repos ohne Remote)
- **Other** (Jira, Linear, etc.) — den Nutzer bitten, den Workflow in einem Absatz zu beschreiben; der Skill zeichnet ihn als Freitext auf

**Abschnitt B — Triage-Label-Vokabular.**

> Erklärung: Wenn der `triage`-Skill ein eingehendes Issue verarbeitet, bewegt er es durch eine State Machine — muss bewertet werden, wartet auf Melder, bereit für einen AFK-Agent zum Aufgreifen, bereit für einen Menschen, oder wird nicht umgesetzt. Dafür müssen Labels (oder das Äquivalent im Issue-Tracker) angewendet werden, die Strings entsprechen, die *tatsächlich konfiguriert* sind. Wenn das Repo bereits andere Label-Namen verwendet (z. B. `bug:triage` statt `needs-triage`), diese hier mappen, damit der Skill die richtigen anwendet statt Duplikate zu erstellen.

Die fünf kanonischen Rollen:

- `needs-triage` — Maintainer muss evaluieren
- `needs-info` — wartet auf Melder
- `ready-for-agent` — vollständig spezifiziert, AFK-ready (ein Agent kann es ohne menschlichen Kontext aufgreifen)
- `ready-for-human` — benötigt menschliche Implementierung
- `wontfix` — wird nicht umgesetzt

Voreinstellung: jeder Rollen-String entspricht seinem Namen. Den Nutzer fragen, ob er welche überschreiben möchte. Wenn der Issue-Tracker keine bestehenden Labels hat, sind die Voreinstellungen in Ordnung.

**Abschnitt C — Domain-Docs.**

> Erklärung: Einige Skills (`improve-codebase-architecture`, `diagnose`, `tdd`) lesen eine `CONTEXT.md`-Datei, um die Domänensprache des Projekts zu lernen, und `docs/adr/` für vergangene Architekturentscheidungen. Sie müssen wissen, ob das Repo einen globalen Context hat oder mehrere (z. B. ein Monorepo mit separaten Frontend/Backend-Contexts), damit sie an der richtigen Stelle suchen.

Das Layout bestätigen:

- **Single-context** — eine `CONTEXT.md` + `docs/adr/` im Repo-Root. Die meisten Repos sind das.
- **Multi-context** — `CONTEXT-MAP.md` im Root, das auf per-Context-`CONTEXT.md`-Dateien zeigt (typischerweise ein Monorepo).

### 3. Bestätigen und bearbeiten

Dem Nutzer einen Entwurf zeigen von:

- Dem `## Agent skills`-Block, der zu whichever von `CLAUDE.md` / `AGENTS.md` hinzugefügt wird (Auswahlregeln siehe Schritt 4)
- Den Inhalten von `docs/agents/issue-tracker.md`, `docs/agents/triage-labels.md`, `docs/agents/domain.md`

Vor dem Schreiben bearbeiten lassen.

### 4. Schreiben

**Die zu bearbeitende Datei auswählen:**

- Wenn `CLAUDE.md` existiert, diese bearbeiten.
- Sonst wenn `AGENTS.md` existiert, diese bearbeiten.
- Wenn keine existiert, den Nutzer fragen, welche angelegt werden soll — nicht selbst entscheiden.

Niemals `AGENTS.md` anlegen, wenn `CLAUDE.md` bereits existiert (oder umgekehrt) — immer die bearbeiten, die bereits da ist.

Wenn ein `## Agent skills`-Block bereits in der gewählten Datei existiert, seinen Inhalt an Ort und Stelle aktualisieren statt ein Duplikat anzufügen. Nutzer-Edits an den umliegenden Abschnitten nicht überschreiben.

Der Block:

```markdown
## Agent skills

### Issue tracker

[einzeilige Zusammenfassung, wo Issues verfolgt werden]. Siehe `docs/agents/issue-tracker.md`.

### Triage labels

[einzeilige Zusammenfassung des Label-Vokabulars]. Siehe `docs/agents/triage-labels.md`.

### Domain docs

[einzeilige Zusammenfassung des Layouts — „single-context" oder „multi-context"]. Siehe `docs/agents/domain.md`.
```

Dann die drei Docs-Dateien schreiben, mithilfe der Seed-Templates in diesem Skill-Ordner als Ausgangspunkt:

- [issue-tracker-github.md](./issue-tracker-github.md) — GitHub Issue-Tracker
- [issue-tracker-gitlab.md](./issue-tracker-gitlab.md) — GitLab Issue-Tracker
- [issue-tracker-local.md](./issue-tracker-local.md) — Local-Markdown Issue-Tracker
- [triage-labels.md](./triage-labels.md) — Label-Mapping
- [domain.md](./domain.md) — Domain-Doc-Konsumregeln + Layout

Für „other" Issue-Tracker `docs/agents/issue-tracker.md` von Grund auf schreiben, basierend auf der Nutzerbeschreibung.

### 5. Fertig

Dem Nutzer mitteilen, dass die Einrichtung abgeschlossen ist und welche Engineering-Skills nun von diesen Dateien lesen werden. Erwähnen, dass sie `docs/agents/*.md` später direkt bearbeiten können — dieser Skill muss nur erneut ausgeführt werden, wenn sie den Issue-Tracker wechseln oder von Grund auf neu starten möchten.
