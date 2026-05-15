# Issue-Tracker: GitHub

Issues und PRDs für diesen Repo leben als GitHub-Issues. Das `gh` CLI für alle Operationen verwenden.

## Konventionen

- **Issue erstellen**: `gh issue create --title "..." --body "..."`. Heredoc für mehrzeilige Bodies verwenden.
- **Issue lesen**: `gh issue view <number> --comments`, Kommentare per `jq` filtern und Labels ebenfalls abrufen.
- **Issues auflisten**: `gh issue list --state open --json number,title,body,labels,comments --jq '[.[] | {number, title, body, labels: [.labels[].name], comments: [.comments[].body]}]'` mit passenden `--label`- und `--state`-Filtern.
- **Auf Issue kommentieren**: `gh issue comment <number> --body "..."`
- **Labels anwenden / entfernen**: `gh issue edit <number> --add-label "..."` / `--remove-label "..."`
- **Schließen**: `gh issue close <number> --comment "..."`

Das Repo aus `git remote -v` ableiten — `gh` macht das automatisch, wenn es innerhalb eines Clones ausgeführt wird.

## Wenn ein Skill sagt „in den Issue-Tracker veröffentlichen"

Ein GitHub-Issue erstellen.

## Wenn ein Skill sagt „das relevante Ticket abrufen"

`gh issue view <number> --comments` ausführen.
