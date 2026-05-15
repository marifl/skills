---
name: git-guardrails-claude-code_DE
description: Claude-Code-Hooks einrichten, um gefährliche Git-Befehle (push, reset --hard, clean, branch -D, etc.) vor ihrer Ausführung zu blockieren. Verwenden, wenn der Nutzer destruktive Git-Operationen verhindern, Git-Sicherheits-Hooks hinzufügen oder git push/reset in Claude Code blockieren möchte.
---

# Git-Guardrails einrichten

Richtet einen PreToolUse-Hook ein, der gefährliche Git-Befehle abfängt und blockiert, bevor Claude sie ausführt.

## Was blockiert wird

- `git push` (alle Varianten einschließlich `--force`)
- `git reset --hard`
- `git clean -f` / `git clean -fd`
- `git branch -D`
- `git checkout .` / `git restore .`

Wenn blockiert, sieht Claude eine Nachricht, die ihm mitteilt, dass es keine Berechtigung hat, auf diese Befehle zuzugreifen.

## Schritte

### 1. Scope fragen

Den Nutzer fragen: Nur für **dieses Projekt** installieren (`.claude/settings.json`) oder für **alle Projekte** (`~/.claude/settings.json`)?

### 2. Das Hook-Script kopieren

Das mitgelieferte Script befindet sich unter: [scripts/block-dangerous-git.sh](scripts/block-dangerous-git.sh)

Es basierend auf dem Scope an den Zielort kopieren:

- **Projekt**: `.claude/hooks/block-dangerous-git.sh`
- **Global**: `~/.claude/hooks/block-dangerous-git.sh`

Mit `chmod +x` ausführbar machen.

### 3. Hook zu den Einstellungen hinzufügen

Zur entsprechenden Einstellungsdatei hinzufügen:

**Projekt** (`.claude/settings.json`):

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "\"$CLAUDE_PROJECT_DIR\"/.claude/hooks/block-dangerous-git.sh"
          }
        ]
      }
    ]
  }
}
```

**Global** (`~/.claude/settings.json`):

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "~/.claude/hooks/block-dangerous-git.sh"
          }
        ]
      }
    ]
  }
}
```

Wenn die Einstellungsdatei bereits existiert, den Hook in das bestehende `hooks.PreToolUse`-Array einmergen — andere Einstellungen nicht überschreiben.

### 4. Nach Anpassung fragen

Den Nutzer fragen, ob er Muster zur Blockliste hinzufügen oder entfernen möchte. Das kopierte Script entsprechend bearbeiten.

### 5. Prüfen

Einen Schnelltest ausführen:

```bash
echo '{"tool_input":{"command":"git push origin main"}}' | <path-to-script>
```

Sollte mit Code 2 beenden und eine BLOCKED-Nachricht auf stderr ausgeben.
