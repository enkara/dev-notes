# 🌿 Arbeiten mit Feature-Branches in SVN

Dieser Guide beschreibt einen typischen **Feature-Branch-Workflow mit Subversion (SVN)**.

SVN hat keine Branches wie Git im technischen Sinn. In SVN sind Branches einfach **Verzeichnisse im Repository**, die per `svn copy` erstellt werden.

---

## 🧱 Typische Repository-Struktur

Viele SVN-Repositories verwenden diese Struktur:

```text
trunk/
branches/
tags/
```

Bedeutung:

- `trunk/` – Hauptentwicklung
- `branches/` – Feature-, Release- oder Wartungs-Branches
- `tags/` – Snapshots, z. B. Releases

Beispiel:

```text
myrepo/
├── trunk/
├── branches/
│   └── feature-PROJ-123-login-fix/
└── tags/
└── release-1.0.0/
```

---

## 🧭 Tipp: `^/` als Repository-Root

Innerhalb einer SVN Working Copy kannst du `^/` als Kurzschreibweise für die Repository-Root verwenden.

```bashsvn info --show-item repos-root-url
```

Wenn die Repository-Root z. B. so aussieht:

```text
https://svn.example.com/svn/myrepo
```

Dann entspricht:

```bashsvn merge ^/trunk
```

diesem Befehl:

```bashsvn merge https://svn.example.com/svn/myrepo/trunk
```

> Voraussetzung: `trunk`, `branches` und `tags` liegen direkt unterhalb der Repository-Root.

---

## 📛 Namenskonvention für Feature-Branches

Empfohlen:

```text
feature-<ticket>-<kurzname>
```

Beispiele:

```text
feature-PROJ-123-login-fix
feature-PROJ-456-customer-export
feature-PROJ-789-cleanup-validation
```

Alternativ möglich:

```text
feature/<ticket>-<kurzname>
```

Beispiel:

```text
feature/PROJ-123-login-fix
```

> Hinweis: Ein Slash im Branch-Namen erzeugt in SVN tatsächlich eine Ordnerstruktur unter `/branches`. Für einfache Branch-Listen ist
> `feature-PROJ-123-login-fix` oft unkomplizierter.

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

```bash
svn copy ^/trunk ^/branches/<FEATURE_NAME> -m "Create feature branch <FEATURE_NAME>"
```

Beispiel:

```bash
svn copy ^/trunk ^/branches/feature-PROJ-123-login-fix -m "Create feature branch feature-PROJ-123-login-fix"
```

Alternativ mit vollständiger Repository-URL:

```bashsvn copy <REPO_URL>/trunk <REPO_URL>/branches/<FEATURE_NAME> -m "Create feature branch <FEATURE_NAME>"
```

> Der Parameter `-m` ist notwendig, weil `svn copy` bei URL-zu-URL-Kopien direkt im Repository committed wird.

---

## 2️⃣ Lokal auf den Branch wechseln

### Option A: Bestehende Working Copy umschalten

```bash
svn switch ^/branches/<FEATURE_NAME>
```

Beispiel:

```bash
svn switch ^/branches/feature-PROJ-123-login-fix
```

### Option B: Branch neu auschecken

```bash
svn checkout ^/branches/<FEATURE_NAME> .
```

Oder mit vollständiger URL:

```bash
svn checkout <REPO_URL>/branches/<FEATURE_NAME> .
```

---

## 3️⃣ Prüfen, wo die Working Copy steht

```bashsvn info
```

Achte auf die Zeile:

```text
URL:
```

Beispiel:

```text
URL: https://svn.example.com/svn/myrepo/branches/feature-PROJ-123-login-fix
```

Damit siehst du, ob du gerade auf `trunk` oder auf einem Branch arbeitest.

---

## 4️⃣ Auf dem Branch arbeiten

Normale SVN-Arbeit auf dem Branch:

```bash
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

```bash
svn status
svn add src/LoginValidator.java
svn commit -m "Add login validation for PROJ-123"
```

---

## 5️⃣ Branch regelmäßig mit `trunk` aktualisieren

Wenn andere Entwickler weiter auf `trunk` arbeiten, solltest du regelmäßig Änderungen aus `trunk` in deinen Branch mergen.

### 5.1 Sicherstellen, dass du auf dem Branch bist

```bash
svn info
```

Die `URL:` sollte auf deinen Branch zeigen:

```text
.../branches/<FEATURE_NAME>
```

### 5.2 Working Copy aktualisieren

```bash
svn update
```

### 5.3 `trunk` in den Branch mergen

```bash
svn merge ^/trunk
```

Oder mit vollständiger URL:

```bash
svn merge <REPO_URL>/trunk
```

### 5.4 Prüfen, testen und committen

```bash
svn status
svn diff
svn commit -m "Merge trunk into <FEATURE_NAME>"
```

> Empfehlung: Lieber regelmäßig kleine Merges aus `trunk` in den Branch machen, statt einen großen Merge am Ende.

---

## 6️⃣ Konflikte beim Merge lösen

Nach einem Merge können Konflikte entstehen.

```bash
svn status
```

Konflikte werden häufig mit `C` angezeigt.

Typischer Ablauf:

```bash
# Datei manuell bearbeiten und Konflikt lösen

# Konflikt als gelöst markieren
svn resolve --accept=working <path>

# Status erneut prüfen
svn status

# Merge committen
svn commit -m "Resolve merge conflicts"
```

Falls du vollständig deine Version übernehmen willst:

```bash
svn resolve --accept=mine-full <path>
```

Falls du vollständig die fremde Version übernehmen willst:

```bash
svn resolve --accept=theirs-full <path>
```

> `mine-full` und `theirs-full` mit Vorsicht verwenden, da dabei eine Seite vollständig übernommen wird.

---

## 7️⃣ Feature-Branch zurück nach `trunk` mergen

Wenn das Feature fertig ist, wird der Branch zurück nach `trunk` gemerged.

### 7.1 Auf `trunk` wechseln

```bash
svn switch ^/trunk
```

Oder mit vollständiger URL:

```bash
svn switch <REPO_URL>/trunk
```

### 7.2 `trunk` aktualisieren

```bash
svn update
```

### 7.3 Feature-Branch in `trunk` mergen

```bash
svn merge ^/branches/<FEATURE_NAME>
```

Beispiel:

```bash
svn merge ^/branches/feature-PROJ-123-login-fix 
```

### 7.4 Änderungen prüfen

```bash
svn status
svn diff
```

### 7.5 Testen

Führe je nach Projekt passende Tests aus, z. B.:

```bash
mvn test
```

oder:

```bash
npm test
```

### 7.6 Merge committen

```bash
svn commit -m "Merge feature <FEATURE_NAME> into trunk"
```

Beispiel:

```bash
svn commit -m "Merge feature PROJ-123 login fix into trunk"
```

---

## 8️⃣ Branch nach dem Merge löschen

Nach erfolgreichem Merge kann der Feature-Branch gelöscht werden.

```bash
svn delete ^/branches/<FEATURE_NAME> -m "Remove merged feature branch <FEATURE_NAME>"
```

Beispiel:

```bash
svn delete ^/branches/feature-PROJ-123-login-fix -m "Remove merged feature branch feature-PROJ-123-login-fix"
```

Das Löschen entfernt den Branch nur aus der aktuellen Repository-Ansicht. Die Historie bleibt in SVN erhalten.

---

## 🏷️ Optional: Vor dem Löschen einen Tag erstellen

Wenn du vor dem Löschen des Branches einen Snapshot behalten möchtest:

```bash
svn copy ^/branches/<FEATURE_NAME> ^/tags/<TAG_NAME> -m "Tag <TAG_NAME>"
```

Beispiel:

```bash
svn copy ^/branches/feature-PROJ-123-login-fix ^/tags/proj-123-before-cleanup -m "Tag PROJ-123 before branch cleanup"
```

Für Releases wird meistens `trunk` getaggt:

```bash
svn copy ^/trunk ^/tags/release-1.0.0 -m "Tag release-1.0.0"
```

---

## 📋 Branches anzeigen

```bash
# Branches anzeigen
svn list ^/branches

# Branches mit Details anzeigen
svn list -v ^/branches
```

Mit vollständiger URL:

```bash
svn list <REPO_URL>/branches
svn list -v <REPO_URL>/branches
```

---

## 🔁 Auf Branch oder trunk wechseln

```bash
# Auf einen Branch wechseln
svn switch ^/branches/<BRANCH_NAME>

# Zurück auf trunk wechseln
svn switch ^/trunk
```

Prüfen:

```bash
svn info
```

---

## 🧹 Lokale Änderungen vor dem Wechsel prüfen

Vor einem `svn switch` ist es sinnvoll, lokale Änderungen zu prüfen:

```bash
svn status
```

Falls noch lokale Änderungen vorhanden sind:

- committen,
- verwerfen,
- oder als Patch sichern.

Patch als Stash-Ersatz:

```bash
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

```bash
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
```
