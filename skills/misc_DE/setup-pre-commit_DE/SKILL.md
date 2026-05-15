---
name: setup-pre-commit_DE
description: Husky-Pre-Commit-Hooks mit lint-staged (Prettier), Typprüfung und Tests im aktuellen Repo einrichten. Verwenden, wenn der Nutzer Pre-Commit-Hooks hinzufügen, Husky einrichten, lint-staged konfigurieren oder Commit-Zeit-Formatierung/Typprüfung/Testen hinzufügen möchte.
---

# Pre-Commit-Hooks einrichten

## Was eingerichtet wird

- **Husky** Pre-Commit-Hook
- **lint-staged** mit Prettier für alle gestagten Dateien
- **Prettier**-Konfiguration (falls fehlend)
- **typecheck**- und **test**-Scripts im Pre-Commit-Hook

## Schritte

### 1. Package-Manager erkennen

Nach `package-lock.json` (npm), `pnpm-lock.yaml` (pnpm), `yarn.lock` (yarn), `bun.lockb` (bun) suchen. Den vorhandenen verwenden. Bei Unklarheit auf npm zurückfallen.

### 2. Abhängigkeiten installieren

Als devDependencies installieren:

```
husky lint-staged prettier
```

### 3. Husky initialisieren

```bash
npx husky init
```

Das legt das `.husky/`-Verzeichnis an und fügt `prepare: "husky"` zu package.json hinzu.

### 4. `.husky/pre-commit` anlegen

Diese Datei schreiben (kein Shebang nötig für Husky v9+):

```
npx lint-staged
npm run typecheck
npm run test
```

**Anpassen**: `npm` durch den erkannten Package-Manager ersetzen. Wenn das Repo kein `typecheck`- oder `test`-Script in package.json hat, diese Zeilen weglassen und den Nutzer informieren.

### 5. `.lintstagedrc` anlegen

```json
{
  "*": "prettier --ignore-unknown --write"
}
```

### 6. `.prettierrc` anlegen (falls fehlend)

Nur anlegen, wenn keine Prettier-Konfiguration existiert. Diese Voreinstellungen verwenden:

```json
{
  "useTabs": false,
  "tabWidth": 2,
  "printWidth": 80,
  "singleQuote": false,
  "trailingComma": "es5",
  "semi": true,
  "arrowParens": "always"
}
```

### 7. Prüfen

- [ ] `.husky/pre-commit` existiert und ist ausführbar
- [ ] `.lintstagedrc` existiert
- [ ] `prepare`-Script in package.json ist `"husky"`
- [ ] Prettier-Konfiguration existiert
- [ ] `npx lint-staged` ausführen, um zu prüfen, ob es funktioniert

### 8. Committen

Alle geänderten/erstellten Dateien stagen und mit der Nachricht committen: `Add pre-commit hooks (husky + lint-staged + prettier)`

Das wird durch die neuen Pre-Commit-Hooks laufen — ein guter Smoke-Test, dass alles funktioniert.

## Hinweise

- Husky v9+ benötigt keine Shebangs in Hook-Dateien
- `prettier --ignore-unknown` überspringt Dateien, die Prettier nicht parsen kann (Bilder, etc.)
- Der Pre-Commit führt zuerst lint-staged aus (schnell, nur gestagt), dann vollständige Typprüfung und Tests
