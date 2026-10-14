# Git & GitHub lernen – Branch, Commit, Push, Pull Request

Dieses Dokument begleitet die Übungen im Repository `CopilotTest` und erklärt
jeden verwendeten Git-/GitHub-Befehl.

## 1. Remote prüfen

```powershell
git remote -v
```

- Zeigt die konfigurierten Remote-Repositories (`fetch`- und `push`-URL).
- `origin` ist der Standardname für das Remote, von dem geklont wurde bzw.
  wohin gepusht wird.

## 2. Status und Branches prüfen

```powershell
git fetch --prune
git status --short --branch
git branch --all
git log --oneline --graph --all -n 20
```

- `git fetch --prune`: Holt neue Infos vom Remote und entfernt lokale
  Referenzen auf bereits gelöschte Remote-Branches.
- `git status --short --branch`: Kompakter Status inkl. aktuellem Branch und
  Tracking-Zustand gegenüber dem Remote.
- `git branch --all`: Listet lokale und Remote-Branches (`remotes/origin/...`).
- `git log --oneline --graph --all`: Zeigt die Commit-Historie als Graph,
  inklusive Merges und Branch-Verzweigungen.

## 3. Neuen Branch erstellen

```powershell
git switch -c feature/demo-branch
```

- `git switch`: Wechselt zwischen Branches.
- `-c` (`--create`): Erstellt den Branch neu, abgezweigt vom aktuellen Stand
  (hier `main`).
- `feature/demo-branch`: Name des neuen, rein lokalen Branches.

## 4. Datei ändern/anlegen

Normale Dateisystem-Änderung (kein Git-Befehl), z. B. eine neue Markdown-Datei
mit Testinhalt anlegen.

## 5. Änderung stagen

```powershell
git add demo-branch.md
```

- `git add`: Nimmt die Datei in die **Staging Area** auf. Nur gestagte
  Änderungen werden Teil des nächsten Commits.

## 6. Commit erstellen

```powershell
git commit -m "Add demo-branch.md"
```

- `git commit`: Erstellt einen neuen Commit aus allem Gestageten.
- `-m "..."`: Commit-Nachricht direkt inline, ohne Editor zu öffnen.

## 7. Branch veröffentlichen (Push)

```powershell
git push -u origin feature/demo-branch
```

- `git push`: Überträgt lokale Commits zum Remote.
- `origin`: Ziel-Remote.
- `feature/demo-branch`: Der zu pushende Branch.
- `-u` (`--set-upstream`): Verknüpft den lokalen Branch mit
  `origin/feature/demo-branch`. Danach genügen einfache `git push` /
  `git pull` ohne weitere Angaben.

GitHub gibt nach dem ersten Push meist direkt einen Link zum Anlegen eines
Pull Requests aus, z. B.:

```text
https://github.com/pjo99/CopilotTest/pull/new/feature/demo-branch
```

## 8. Pull Request erstellen (GitHub CLI)

```powershell
gh pr create --repo pjo99/CopilotTest --base main --head feature/demo-branch --title "Add demo-branch.md" --body "Demo PR for practicing the branch/PR workflow."
```

- `gh pr create`: Erstellt einen Pull Request über die GitHub CLI.
- `--repo pjo99/CopilotTest`: Ziel-Repository.
- `--base main`: Zielbranch, in den gemergt werden soll.
- `--head feature/demo-branch`: Quellbranch mit den Änderungen.
- `--title` / `--body`: Titel und Beschreibung des PRs.

### Voraussetzung: GitHub CLI Login

```powershell
gh auth login
gh auth status
```

- `gh auth login`: Meldet die CLI interaktiv bei GitHub an (Browser- oder
  Token-basiert).
- `gh auth status`: Zeigt, mit welchem Account die CLI aktuell angemeldet ist.
  Wichtig bei der Meldung `must be a collaborator` – dann ist evtl. der
  falsche Account angemeldet.

## 9. Nach dem Merge aufräumen

Wenn der PR auf GitHub gemerged wurde:

```powershell
git switch main
git pull --ff-only
git branch -d feature/demo-branch
git push origin --delete feature/demo-branch
git fetch --prune
```

- `git switch main`: Zurück zum Hauptbranch wechseln.
- `git pull --ff-only`: Holt den gemergten Stand von GitHub. `--ff-only`
  verhindert einen unerwarteten Merge-Commit lokal, falls die Historien
  divergieren.
- `git branch -d feature/demo-branch`: Löscht den **lokalen** Feature-Branch
  (nur möglich, wenn er bereits gemerged ist).
- `git push origin --delete feature/demo-branch`: Löscht den Branch zusätzlich
  auf GitHub – lokales Löschen entfernt den Remote-Branch NICHT automatisch.
- `git fetch --prune`: Bereinigt lokale Referenzen auf inzwischen gelöschte
  Remote-Branches.

### Sonderfall: Branch ist Standardbranch

```powershell
gh repo edit pjo99/CopilotTest --default-branch main
```

- Falls GitHub das Löschen eines Branches mit
  `refusing to delete the current branch` ablehnt, ist dieser Branch als
  **Default Branch** des Repositories eingestellt. Erst `main` als
  Standardbranch setzen, danach lässt sich der alte Branch löschen.

## 10. Git Worktree (paralleles Arbeiten)

```powershell
git worktree add ../CopilotTest-worktree feature/demo-worktree
```

- `git worktree add`: Legt einen zusätzlichen Arbeitsordner an, der auf einen
  eigenen Branch ausgecheckt ist – ohne dass der Hauptordner den Branch
  wechseln muss. Praktisch, um z. B. gleichzeitig an `main` und an einem
  Feature zu arbeiten.

## Kurzübersicht der wichtigsten Befehle

| Befehl | Zweck |
| --- | --- |
| `git remote -v` | Remotes anzeigen |
| `git status --short --branch` | Kompakter Status |
| `git switch -c <branch>` | Branch erstellen und wechseln |
| `git add <datei>` | Änderung stagen |
| `git commit -m "<msg>"` | Commit erstellen |
| `git push -u origin <branch>` | Branch veröffentlichen |
| `gh pr create` | Pull Request erstellen |
| `git pull --ff-only` | Änderungen holen, nur Fast-Forward |
| `git branch -d <branch>` | Lokalen Branch löschen |
| `git push origin --delete <branch>` | Remote-Branch löschen |
| `git worktree add <pfad> <branch>` | Zusätzlichen Arbeitsordner anlegen |
