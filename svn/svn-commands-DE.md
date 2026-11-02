# 🧭 SVN Commands – Cheat Sheet

Eine kompakte Sammlung nützlicher **Subversion (SVN)** Befehle für den täglichen Entwicklungsalltag.

---

## 📚 Referenzen

- [Apache Subversion – Official Documentation](https://subversion.apache.org/docs/)
- [Version Control with Subversion – SVN Book](https://svnbook.red-bean.com/)

---

## 🧠 Grundbegriffe

Typische SVN-Repository-Struktur:

```text
trunk/      # Hauptentwicklung
branches/  # Feature-, Release- oder Wartungs-Branches
tags/       # Snapshots, z. B. Releases
```

Wichtige Begriffe:

- **Repository**: zentrale Ablage auf dem Server
- **Working Copy**: lokale Arbeitskopie auf deinem Rechner
- **Revision**: eindeutige Nummer eines Repository-Zustands, z. B. `r1234`
- **trunk**: Hauptentwicklungslinie
- **branch**: Kopie eines Repository-Pfads, z. B. für Feature-Arbeit
- **tag**: Snapshot, häufig für Releases

---

## 🔍 Hilfe & Informationen

```bash
# Allgemeine Hilfe anzeigen
svn help

# Hilfe für einen bestimmten Befehl anzeigen
svn help <command>

# SVN-Version anzeigen
svn --version

# Informationen zur aktuellen Working Copy anzeigen
svn info
```

Nützliche Informationen aus `svn info`:

- `URL:` zeigt, auf welchem Repository-Pfad deine Working Copy steht
- `Repository Root:` zeigt die Root-URL des Repositories
- `Revision:` zeigt die aktuelle Revision der Working Copy

---

## 📥 Checkout, Update & Switch

```bash
# Repository oder Repository-Pfad initial auschecken
svn checkout <REPO_URL> .

# Beispiel: trunk auschecken
svn checkout <REPO_URL>/trunk .

# Working Copy auf den neuesten Stand bringen
svn update

# Working Copy auf einen anderen Branch oder trunk umschalten
svn switch <BRANCH_URL>

# Beispiel: auf trunk zurückwechseln
svn switch <REPO_URL>/trunk
```

---

## 🧭 Repository-Root mit `^/`

Innerhalb einer SVN Working Copy kannst du `^/` als Kurzschreibweise für die Repository-Root-URL verwenden.

```bash
# Repository-Root anzeigen
svn info --show-item repos-root-url

# Branches anzeigen
svn list ^/branches

# Auf trunk wechseln
svn switch ^/trunk

# Auf einen Branch wechseln
svn switch ^/branches/<BRANCH_NAME>
```

> Voraussetzung: Die Repository-Struktur muss zu den relativen Pfaden passen, z. B. `trunk`, `branches` und `tags` direkt unterhalb der
> Repository-Root.

---

## 📊 Status & Diff

```bash
# Kurzstatus der Working Copy anzeigen
svn status

# Auch ignorierte Dateien anzeigen
svn status --no-ignore

# Lokale Änderungen anzeigen
svn diff

# Unterschied zwischen zwei Revisionen anzeigen
svn diff -r <REV1>:<REV2>

# Unterschied einer bestimmten Revision anzeigen
svn diff -c <REV>
```

Häufige Statuscodes:

| Code | Bedeutung   | Erklärung                                    |
|------|-------------|----------------------------------------------|
| `A`  | Added       | Neu hinzugefügt, aber noch nicht committed   |
| `M`  | Modified    | Lokal geändert                               |
| `D`  | Deleted     | Lokal gelöscht, Commit steht aus             |
| `?`  | Unversioned | Nicht versioniert                            |
| `!`  | Missing     | Datei fehlt lokal, obwohl SVN sie erwartet   |
| `I`  | Ignored     | Wird durch Ignore-Regeln ignoriert           |
| `C`  | Conflicted  | Konflikt nach Update oder Merge              |
| `R`  | Replaced    | Gelöscht und durch neue Datei ersetzt        |
| `X`  | Externals   | Über `svn:externals` eingebunden             |
| `~`  | Obstructed  | Pfad ist blockiert oder hat unerwarteten Typ |

Für eine ausführlichere Erklärung siehe:

```text
svn-status-meaning-DE.md
```

---

## ➕ Dateien hinzufügen, löschen, verschieben

```bash
# Neue Datei oder neuen Ordner zur Versionskontrolle hinzufügen
svn add <path>

# Datei oder Ordner löschen
svn delete <path>

# Datei oder Ordner verschieben/umbenennen
svn move <old_path> <new_path>

# Datei oder Ordner kopieren
svn copy <source> <destination>
```

Beispiel:

```bash
svn add src/NewFile.java
svn delete src/OldFile.java
svn move src/OldName.java src/NewName.java
```

---

## 💾 Änderungen committen

```bash
# Alle lokalen Änderungen committen
svn commit -m "Kurzbeschreibung der Änderung"

# Nur bestimmte Dateien oder Ordner committen
svn commit <path1> <path2> -m "Kurzbeschreibung der Änderung"
```

Gute Commit Messages:

```bash
svn commit -m "Fix login validation"
svn commit -m "Add customer export"
svn commit -m "Merge trunk into feature branch"
```

---

## 🔁 Lokale Änderungen verwerfen

```bash
# Änderungen an einer Datei oder einem Ordner verwerfen
svn revert <path>

# Alle lokalen Änderungen rekursiv verwerfen
svn revert -R .
```

> Achtung: `svn revert` verwirft lokale Änderungen dauerhaft. Nicht verwechseln mit dem Rückgängigmachen eines bereits committed Commits.

---

## 🧾 Historie & Analyse

```bash
# Commit-Historie anzeigen
svn log

# Letzte 10 Log-Einträge anzeigen
svn log -l 10

# Log für einen bestimmten Pfad anzeigen
svn log <path>

# Details einer bestimmten Revision anzeigen
svn log -v -r <REV>

# Anzeigen, wer welche Zeile zuletzt geändert hat
svn blame <file>
```

Beispiel:

```bash
svn log -l 5
svn log -v -r 1234
svn blame src/App.java
```

---

## 🧹 Cleanup & Working-Copy-Probleme

```bash
# Working Copy reparieren, z. B. nach abgebrochenem Update oder Commit
svn cleanup

# Danach erneut aktualisieren
svn update
```

Typische Fälle für `svn cleanup`:

- Update wurde abgebrochen
- Commit wurde unterbrochen
- Working Copy ist gesperrt
- SVN meldet `working copy locked`

---

## 🔒 Locking & Unlocking

Manche SVN-Repositories verwenden Locks, z. B. für Binärdateien oder Dokumente.

```bash
# Datei sperren
svn lock <path> -m "Reason"

# Datei entsperren
svn unlock <path>

# Lock erzwingen, falls nötig
svn lock --force <path>
```

> `--force` nur verwenden, wenn klar ist, dass der bestehende Lock nicht mehr gebraucht wird.

---

## 🌿 Branches anzeigen und wechseln

```bash
# Branches anzeigen
svn list <REPO_URL>/branches

# Branches mit Details anzeigen
svn list -v <REPO_URL>/branches

# Innerhalb einer Working Copy: Branches relativ zur Repository-Root anzeigen
svn list ^/branches

# Auf Branch wechseln
svn switch <REPO_URL>/branches/<BRANCH_NAME>

# Auf trunk zurückwechseln
svn switch <REPO_URL>/trunk
```

Aktuellen Pfad prüfen:

```bash
svn info
```

Achte auf die Zeile:

```text
URL:
```

Sie zeigt, ob du gerade auf `trunk`, einem Branch oder einem Tag arbeitest.

---

## 🏷️ Tags erstellen

In SVN sind Tags normalerweise günstige Kopien unter `/tags`.

```bash
# Tag von trunk erstellen
svn copy <REPO_URL>/trunk <REPO_URL>/tags/<TAG_NAME> -m "Tag <TAG_NAME>"

# Tag von einem Branch erstellen
svn copy <REPO_URL>/branches/<BRANCH_NAME> <REPO_URL>/tags/<TAG_NAME> -m "Tag <TAG_NAME>"
```

Beispiel:

```bash
svn copy ^/trunk ^/tags/release-1.0.0 -m "Tag release-1.0.0"
```

> Tags sollten nach dem Erstellen nicht mehr verändert werden.

---

## 🔀 Merge-Grundlagen

### Branch in aktuelle Working Copy mergen

```bash
# Änderungen aus einem Branch in die aktuelle Working Copy mergen
svn merge <SOURCE_BRANCH_URL>
```

Beispiel:

```bash
# trunk in aktuellen Branch mergen
svn merge ^/trunk

# Feature-Branch in trunk mergen
svn merge ^/branches/<BRANCH_NAME>
```

Nach jedem Merge:

```bash
# Änderungen prüfen
svn status
svn diff

# Konflikte lösen, testen und committen
svn commit -m "Merge <source> into <target>"
```

> Hinweis: `svn merge --reintegrate` war in älteren SVN-Workflows relevant. In aktuellen SVN-Versionen ist normalerweise ein reguläres
> `svn merge <branch_url>` ausreichend.

---

## ⚔️ Konflikte lösen

```bash
# Konflikte anzeigen
svn status

# Nach manueller Lösung als gelöst markieren
svn resolve --accept=working <path>

# Eigene Version vollständig übernehmen
svn resolve --accept=mine-full <path>

# Fremde Version vollständig übernehmen
svn resolve --accept=theirs-full <path>
```

Typischer Ablauf:

```bash
svn status
# Datei manuell bearbeiten
svn resolve --accept=working <path>
svn status
svn commit -m "Resolve merge conflict"
```

---

## 🧰 Properties & Ignore-Regeln

```bash
# Ignore-Regel auf einem Ordner setzen
svn propset svn:ignore "node_modules" .

# Ignore-Regeln anzeigen
svn propget svn:ignore .

# Alle Properties anzeigen
svn proplist -v .
```

Mehrere Ignore-Einträge setzen:

```bash
svn propset svn:ignore "node_modules
dist
.env
.idea" .
```

Danach committen:

```bash
svn commit -m "Update svn ignore rules"
```

---

## ↩️ Commit rückgängig machen

Ein bereits committed Commit wird in SVN durch einen **Reverse Merge** rückgängig gemacht.

### 1. Revision finden

```bash
svn log -v
```

Beispiel: Die falsche Revision ist `r1234`.

### 2. Reverse Merge ausführen

Im Zielpfad, z. B. im `trunk`:

```bash
svn merge -c -1234 .
```

Das Minus vor der Revision bedeutet: Änderung rückgängig machen.

### 3. Änderungen prüfen

```bash
svn status
svn diff
```

### 4. Revert committen

```bash
svn commit -m "Revert r1234"
```

> Wichtig: `svn revert` macht nur lokale Änderungen rückgängig. Für bereits committed Änderungen wird ein Reverse Merge verwendet.

---

## 📦 Patch als Stash-Ersatz

SVN hat keinen eingebauten `stash` wie Git. Eine einfache Alternative ist ein Patch.

```bash
# Lokale Änderungen in Patch-Datei speichern
svn diff > my-changes.patch

# Working Copy bereinigen
svn revert -R .

# Patch später wieder anwenden
svn patch my-changes.patch
```

> In JetBrains IDEs wie WebStorm oder IntelliJ kann alternativ die Funktion **Shelve / Unshelve** genutzt werden.

---

## ✅ Quick Reference

```bash
svn status
svn update
svn diff
svn add <path>
svn delete <path>
svn move <old_path> <new_path>
svn commit -m "Message"
svn log -l 10
svn info
svn cleanup
svn switch ^/trunk
svn switch ^/branches/<BRANCH_NAME>
svn merge ^/trunk
svn resolve --accept=working <path>
svn revert <path>
```

```


---

```markdown
# 🌿 Arbeiten mit Feature-Branches in SVN

Dieser Guide beschreibt einen typischen **Feature-Branch-Workflow mit Subversion (SVN)**.

SVN hat keine Branches wie Git im technischen Sinn. In SVN sind Branches einfach **Verzeichnisse im Repository**, die per `svn copy` erstellt werden.

---

## 🧱 Typische Repository-Struktur

Viele SVN-Repositories verwenden diese Struktur:
```

text
trunk/
branches/
tags/

```
Bedeutung:

- `trunk/` – Hauptentwicklung
- `branches/` – Feature-, Release- oder Wartungs-Branches
- `tags/` – Snapshots, z. B. Releases

Beispiel:
```

text
myrepo/
├── trunk/
├── branches/
│ └── feature-PROJ-123-login-fix/
└── tags/
└── release-1.0.0/

```
---

## 🧭 Tipp: `^/` als Repository-Root

Innerhalb einer SVN Working Copy kannst du `^/` als Kurzschreibweise für die Repository-Root verwenden.
```

bash
svn info --show-item repos-root-url

```
Wenn die Repository-Root z. B. so aussieht:
```

text
https://svn.example.com/svn/myrepo

```
Dann entspricht:
```

bash
svn merge ^/trunk

```
diesem Befehl:
```

bash
svn merge https://svn.example.com/svn/myrepo/trunk

```
> Voraussetzung: `trunk`, `branches` und `tags` liegen direkt unterhalb der Repository-Root.

---

## 📛 Namenskonvention für Feature-Branches

Empfohlen:
```

text
feature-<ticket>-<kurzname>

```
Beispiele:
```

text
feature-PROJ-123-login-fix
feature-PROJ-456-customer-export
feature-PROJ-789-cleanup-validation

```
Alternativ möglich:
```

text
feature/<ticket>-<kurzname>

```
Beispiel:
```

text
feature/PROJ-123-login-fix

```
> Hinweis: Ein Slash im Branch-Namen erzeugt in SVN tatsächlich eine Ordnerstruktur unter `/branches`. Für einfache Branch-Listen ist `feature-PROJ-123-login-fix` oft unkomplizierter.

---

## ✅ Ziel des Workflows

Der typische Ablauf:

1. Feature-Branch aus `trunk` erstellen
2. Auf dem Branch arbeiten
3. Regelmäßig Änderungen aus `trunk` in den Branch übernehmen
4. Feature-Branch zurück nach `trunk` mergen
5. Branch nach erfolgreichem Merge optional löschen

---

## 1️⃣ Feature-Branch aus `trunk` erstellen

Ein Branch wird in SVN per `svn copy` erstellt.
```

bash
svn copy ^/trunk ^/branches/<FEATURE_NAME> -m "Create feature branch <FEATURE_NAME>"

```
Beispiel:
```

bash
svn copy ^/trunk ^/branches/feature-PROJ-123-login-fix -m "Create feature branch feature-PROJ-123-login-fix"

```
Alternativ mit vollständiger Repository-URL:
```

bash
svn copy <REPO_URL>/trunk <REPO_URL>/branches/<FEATURE_NAME> -m "Create feature branch <FEATURE_NAME>"

```
> Der Parameter `-m` ist notwendig, weil `svn copy` bei URL-zu-URL-Kopien direkt im Repository committed wird.

---

## 2️⃣ Lokal auf den Branch wechseln

### Option A: Bestehende Working Copy umschalten
```

bash
svn switch ^/branches/<FEATURE_NAME>

```
Beispiel:
```

bash
svn switch ^/branches/feature-PROJ-123-login-fix

```
### Option B: Branch neu auschecken
```

bash
svn checkout ^/branches/<FEATURE_NAME> .

```
Oder mit vollständiger URL:
```

bash
svn checkout <REPO_URL>/branches/<FEATURE_NAME> .

```
---

## 3️⃣ Prüfen, wo die Working Copy steht
```

bash
svn info

```
Achte auf die Zeile:
```

text
URL:

```
Beispiel:
```

text
URL: https://svn.example.com/svn/myrepo/branches/feature-PROJ-123-login-fix

```
Damit siehst du, ob du gerade auf `trunk` oder auf einem Branch arbeitest.

---

## 4️⃣ Auf dem Branch arbeiten

Normale SVN-Arbeit auf dem Branch:
```

bash

# Status prüfen

svn status

# Änderungen anzeigen

svn diff

# Neue Dateien hinzufügen

svn add <path>

# Änderungen committen

svn commit -m "Implement feature PROJ-123"

```
Beispiel:
```

bash
svn status
svn add src/LoginValidator.java
svn commit -m "Add login validation for PROJ-123"

```
---

## 5️⃣ Branch regelmäßig mit `trunk` aktualisieren

Wenn andere Entwickler weiter auf `trunk` arbeiten, solltest du regelmäßig Änderungen aus `trunk` in deinen Branch mergen.

### 5.1 Sicherstellen, dass du auf dem Branch bist
```

bash
svn info

```
Die `URL:` sollte auf deinen Branch zeigen:
```

text
.../branches/<FEATURE_NAME>

```
### 5.2 Working Copy aktualisieren
```

bash
svn update

```
### 5.3 `trunk` in den Branch mergen
```

bash
svn merge ^/trunk

```
Oder mit vollständiger URL:
```

bash
svn merge <REPO_URL>/trunk

```
### 5.4 Prüfen, testen und committen
```

bash
svn status
svn diff
svn commit -m "Merge trunk into <FEATURE_NAME>"

```
> Empfehlung: Lieber regelmäßig kleine Merges aus `trunk` in den Branch machen, statt einen großen Merge am Ende.

---

## 6️⃣ Konflikte beim Merge lösen

Nach einem Merge können Konflikte entstehen.
```

bash
svn status

```
Konflikte werden häufig mit `C` angezeigt.

Typischer Ablauf:
```

bash

# Datei manuell bearbeiten und Konflikt lösen

# Konflikt als gelöst markieren

svn resolve --accept=working <path>

# Status erneut prüfen

svn status

# Merge committen

svn commit -m "Resolve merge conflicts"

```
Falls du vollständig deine Version übernehmen willst:
```

bash
svn resolve --accept=mine-full <path>

```
Falls du vollständig die fremde Version übernehmen willst:
```

bash
svn resolve --accept=theirs-full <path>

```
> `mine-full` und `theirs-full` mit Vorsicht verwenden, da dabei eine Seite vollständig übernommen wird.

---

## 7️⃣ Feature-Branch zurück nach `trunk` mergen

Wenn das Feature fertig ist, wird der Branch zurück nach `trunk` gemerged.

### 7.1 Auf `trunk` wechseln
```

bash
svn switch ^/trunk

```
Oder mit vollständiger URL:
```

bash
svn switch <REPO_URL>/trunk

```
### 7.2 `trunk` aktualisieren
```

bash
svn update

```
### 7.3 Feature-Branch in `trunk` mergen
```

bash
svn merge ^/branches/<FEATURE_NAME>

```
Beispiel:
```

bash
svn merge ^/branches/feature-PROJ-123-login-fix

```
### 7.4 Änderungen prüfen
```

bash
svn status
svn diff

```
### 7.5 Testen

Führe je nach Projekt passende Tests aus, z. B.:
```

bash
mvn test

```
oder:
```

bash
npm test

```
### 7.6 Merge committen
```

bash
svn commit -m "Merge feature <FEATURE_NAME> into trunk"

```
Beispiel:
```

bash
svn commit -m "Merge feature PROJ-123 login fix into trunk"

```
---

## 8️⃣ Branch nach dem Merge löschen

Nach erfolgreichem Merge kann der Feature-Branch gelöscht werden.
```

bash
svn delete ^/branches/<FEATURE_NAME> -m "Remove merged feature branch <FEATURE_NAME>"

```
Beispiel:
```

bash
svn delete ^/branches/feature-PROJ-123-login-fix -m "Remove merged feature branch feature-PROJ-123-login-fix"

```
Das Löschen entfernt den Branch nur aus der aktuellen Repository-Ansicht. Die Historie bleibt in SVN erhalten.

---

## 🏷️ Optional: Vor dem Löschen einen Tag erstellen

Wenn du vor dem Löschen des Branches einen Snapshot behalten möchtest:
```

bash
svn copy ^/branches/<FEATURE_NAME> ^/tags/<TAG_NAME> -m "Tag <TAG_NAME>"

```
Beispiel:
```

bash
svn copy ^/branches/feature-PROJ-123-login-fix ^/tags/proj-123-before-cleanup -m "Tag PROJ-123 before branch cleanup"

```
Für Releases wird meistens `trunk` getaggt:
```

bash
svn copy ^/trunk ^/tags/release-1.0.0 -m "Tag release-1.0.0"

```
---

## 📋 Branches anzeigen
```

bash

# Branches anzeigen

svn list ^/branches

# Branches mit Details anzeigen

svn list -v ^/branches

```
Mit vollständiger URL:
```

bash
svn list <REPO_URL>/branches
svn list -v <REPO_URL>/branches

```
---

## 🔁 Auf Branch oder trunk wechseln
```

bash

# Auf einen Branch wechseln

svn switch ^/branches/<BRANCH_NAME>

# Zurück auf trunk wechseln

svn switch ^/trunk

```
Prüfen:
```

bash
svn info

```
---

## 🧹 Lokale Änderungen vor dem Wechsel prüfen

Vor einem `svn switch` ist es sinnvoll, lokale Änderungen zu prüfen:
```

bash
svn status

```
Falls noch lokale Änderungen vorhanden sind:

- committen,
- verwerfen,
- oder als Patch sichern.

Patch als Stash-Ersatz:
```

bash
svn diff > my-changes.patch
svn revert -R .
svn switch ^/branches/<BRANCH_NAME>
svn patch my-changes.patch

```
---

## ✅ Mini-Checkliste

- [ ] Aktuellen Stand von `trunk` prüfen
- [ ] Feature-Branch aus `trunk` erstellen
- [ ] Working Copy auf Branch wechseln
- [ ] Auf dem Branch arbeiten und committen
- [ ] Regelmäßig `trunk` in den Branch mergen
- [ ] Konflikte lösen und testen
- [ ] Branch zurück nach `trunk` mergen
- [ ] Merge auf `trunk` testen
- [ ] Merge committen
- [ ] Feature-Branch optional löschen

---

## 🚀 Kompletter Beispielablauf
```

bash

# 1. Branch aus trunk erstellen

svn copy ^/trunk ^/branches/feature-PROJ-123-login-fix -m "Create feature branch feature-PROJ-123-login-fix"

# 2. Auf Branch wechseln

svn switch ^/branches/feature-PROJ-123-login-fix

# 3. Arbeiten

svn status
svn add src/LoginValidator.java
svn commit -m "Implement login validation for PROJ-123"

# 4. trunk in Branch mergen

svn update
svn merge ^/trunk
svn status
svn commit -m "Merge trunk into feature-PROJ-123-login-fix"

# 5. Zurück auf trunk wechseln

svn switch ^/trunk
svn update

# 6. Branch in trunk mergen

svn merge ^/branches/feature-PROJ-123-login-fix
svn status
svn diff
svn commit -m "Merge feature PROJ-123 login fix into trunk"

# 7. Branch löschen

svn delete ^/branches/feature-PROJ-123-login-fix -m "Remove merged feature branch feature-PROJ-123-login-fix"
