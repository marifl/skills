# Issue-Tracker: GitLab

Issues und PRDs für diesen Repo leben als GitLab-Issues. Das [`glab`](https://gitlab.com/gitlab-org/cli) CLI für alle Operationen verwenden.

## Konventionen

- **Issue erstellen**: `glab issue create --title "..." --description "..."`. Heredoc für mehrzeilige Descriptions verwenden. `--description -` übergeben, um einen Editor zu öffnen.
- **Issue lesen**: `glab issue view <number> --comments`. `-F json` für maschinenlesbaren Output verwenden.
- **Issues auflisten**: `glab issue list -F json` mit passenden `--label`-Filtern.
- **Auf Issue kommentieren**: `glab issue note <number> --message "..."`. GitLab nennt Kommentare „Notes".
- **Labels anwenden / entfernen**: `glab issue update <number> --label "..."` / `--unlabel "..."`. Mehrere Labels können kommagetrennt oder durch Wiederholung des Flags angegeben werden.
- **Schließen**: `glab issue close <number>`. `glab issue close` akzeptiert keinen schließenden Kommentar, daher zuerst die Erklärung mit `glab issue note <number> --message "..."` posten, dann schließen.
- **Merge Requests**: GitLab nennt PRs „Merge Requests". `glab mr create`, `glab mr view`, `glab mr note` usw. verwenden — gleiche Form wie `gh pr ...` mit `mr` statt `pr` und `note`/`--message` statt `comment`/`--body`.

Das Repo aus `git remote -v` ableiten — `glab` macht das automatisch, wenn es innerhalb eines Clones ausgeführt wird.

## Wenn ein Skill sagt „in den Issue-Tracker veröffentlichen"

Ein GitLab-Issue erstellen.

## Wenn ein Skill sagt „das relevante Ticket abrufen"

`glab issue view <number> --comments` ausführen.
