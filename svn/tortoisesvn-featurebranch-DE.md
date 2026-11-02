# 🐢 TortoiseSVN – Feature-Branch Workflow

Dieser Spickzettel beschreibt den typischen **Feature-Branch-Workflow mit TortoiseSVN**.

Ziel ist, sicher durch die häufigsten SVN-Branching-Schritte zu kommen:

1. Feature-Branch aus `trunk` erstellen
2. Auf dem Branch arbeiten
3. Branch regelmäßig mit Änderungen aus `trunk` aktualisieren
4. Feature-Branch zurück nach `trunk` mergen
5. Branch optional löschen

---

## 🧠 Grundprinzip

In SVN sind Branches keine speziellen Objekte wie in Git. Ein Branch ist ein **Verzeichnis im Repository**, meistens unter:

```text
/trunk
/branches
/tags
```

Beispiel:

```text
https://svn.example.com/svn/myrepo/trunk
https://svn.example.com/svn/myrepo/branches/feature-PROJ-123-login-fix
https://svn.example.com/svn/myrepo/tags/release-1.0.0
```

---

## ⚠️ Wichtig: Quelle und Ziel beim Merge

Beim Mergen ist die Richtung entscheidend.

### trunk in Branch mergen

```text
Quelle: trunk
Ziel:   dein Feature-Branch
```

Du stehst lokal also auf dem **Branch** und mergst Änderungen aus **trunk** hinein.

Verwendung:

```text
trunk → branch
```

Das machst du regelmäßig, damit dein Branch aktuell bleibt.

---

### Branch zurück in trunk mergen

```text
Quelle: dein Feature-Branch
Ziel:   trunk
```

Du stehst lokal also auf **trunk** und mergst Änderungen aus dem **Branch** hinein.

Verwendung:

```text
branch → trunk
```

Das machst du am Ende, wenn dein Feature fertig ist.

---

## ✅ Vor jedem wichtigen Schritt prüfen

Bevor du Branches erstellst, wechselst oder mergst:

1. Rechtsklick auf deine Working Copy
2. **TortoiseSVN → Check for Modifications**
3. Prüfen, ob noch lokale Änderungen offen sind

Wenn noch lokale Änderungen vorhanden sind:

- entweder committen,
- oder revertieren,
- oder shelven,
- oder vorher bewusst sichern.

> Empfehlung: Vor einem Merge möglichst mit einer sauberen Working Copy starten.

---

## 1️⃣ Feature-Branch aus trunk erstellen

Ziel:

```text
/trunk
```

wird kopiert nach:

```text
/branches/<FEATURE_NAME>
```

Beispiel:

```text
/branches/feature-PROJ-123-login-fix
```

---

### Variante A: Branch direkt aus der trunk Working Copy erstellen

Diese Variante ist praktisch, wenn deine lokale Working Copy gerade auf `trunk` steht.

#### Schritte

1. Rechtsklick auf den Ordner deiner Working Copy
2. **TortoiseSVN → Branch/Tag...**
3. Im Dialog prüfen:

   ```text
   From WC at URL:
   .../trunk
   ```

4. Bei **To URL** den Ziel-Branch eintragen:

   ```text
   .../branches/feature-PROJ-123-login-fix
   ```

5. Commit Message eintragen:

   ```text
   Create feature branch feature-PROJ-123-login-fix
   ```

6. Optional: Checkbox aktivieren:

   ```text
   Switch working copy to new branch/tag
   ```

7. Mit **OK** bestätigen

---

### Empfehlung

Aktiviere beim Erstellen des Feature-Branches direkt:

```text
Switch working copy to new branch/tag
```

Dann befindest du dich nach dem Erstellen automatisch auf dem neuen Branch.

Falls du die Option nicht aktivierst, bleibt deine Working Copy weiterhin auf `trunk`.

---

## 2️⃣ Prüfen, ob du auf dem Branch bist

Nach dem Branch-Erstellen oder Wechseln solltest du prüfen, wo deine Working Copy steht.

#### Schritte

1. Rechtsklick auf deine Working Copy
2. **TortoiseSVN → Repo-browser**

Oder:

1. Rechtsklick auf deine Working Copy
2. **TortoiseSVN → Revision graph**

Einfacher ist meistens:

1. Rechtsklick auf deine Working Copy
2. **TortoiseSVN → Settings** ist dafür nicht nötig
3. Stattdessen im Windows Explorer:
    - Rechtsklick auf Working Copy
    - **TortoiseSVN → Check for Modifications**
    - unten bzw. über Details die URL prüfen

Noch eindeutiger:

1. Rechtsklick auf Working Copy
2. **TortoiseSVN → Show log**
3. Prüfen, ob die URL oben auf deinen Branch zeigt

Gesuchte URL:

```text
.../branches/feature-PROJ-123-login-fix
```

Nicht:

```text
.../trunk
```

---

## 3️⃣ Auf einen Branch wechseln

Wenn deine Working Copy noch auf `trunk` steht und du auf den Branch wechseln möchtest:

#### Schritte

1. Rechtsklick auf deine Working Copy
2. **TortoiseSVN → Switch...**
3. Bei **To URL** eintragen:

   ```text
   .../branches/feature-PROJ-123-login-fix
   ```

4. Revision:

   ```text
   HEAD revision
   ```

5. Mit **OK** bestätigen

Danach zeigt deine Working Copy auf den Feature-Branch.

---

## 4️⃣ Auf dem Branch arbeiten

Normales Arbeiten auf dem Branch:

1. Dateien ändern
2. Rechtsklick auf Working Copy
3. **SVN Commit...**
4. Änderungen prüfen
5. Commit Message eintragen
6. **OK**

Beispiel-Commit-Message:

```text
Implement login validation for PROJ-123
```

---

## 5️⃣ Branch mit trunk aktualisieren

Das ist der häufige Zwischen-Merge:

```text
trunk → branch
```

Ziel: Dein Branch bekommt die neuesten Änderungen aus `trunk`.

---

## ⚠️ Wichtig vor diesem Schritt

Du musst lokal auf dem **Feature-Branch** stehen.

Prüfen:

```text
Working Copy URL muss sein:
.../branches/feature-PROJ-123-login-fix
```

Nicht:

```text
.../trunk
```

---

### Schritte: trunk in Branch mergen

1. Rechtsklick auf deine Branch-Working-Copy
2. **TortoiseSVN → Update**
3. Danach erneut Rechtsklick
4. **TortoiseSVN → Merge...**
5. Im Merge-Wizard auswählen:

   ```text
   Merge a range of revisions
   ```

6. Bei **URL to merge from** eintragen:

   ```text
   .../trunk
   ```

7. Revision range leer lassen oder **all revisions** verwenden
8. Weiter
9. Optional zuerst:

   ```text
   Test merge
   ```

10. Wenn der Test gut aussieht: Merge ausführen
11. Danach Änderungen prüfen:

    ```text
    TortoiseSVN → Check for Modifications
    ```

12. Projekt bauen/testen
13. Committen:

    ```text
    SVN Commit...
    ```

Commit Message:

```text
Merge trunk into feature-PROJ-123-login-fix
```

---

## ✅ Merksatz

Wenn du deinen Branch aktualisieren willst:

```text
Du stehst auf Branch.
Du mergst von trunk.
```

Also:

```text
trunk → branch
```

---

## 6️⃣ Konflikte beim Merge lösen

Wenn TortoiseSVN Konflikte meldet:

1. Rechtsklick auf die konfliktbehaftete Datei
2. **TortoiseSVN → Edit conflicts**
3. Konflikt im Merge-Tool lösen
4. Datei speichern
5. Rechtsklick auf Datei
6. **TortoiseSVN → Resolved...**
7. Danach erneut prüfen:

   ```text
   TortoiseSVN → Check for Modifications
   ```

8. Wenn alles passt: Committen

Commit Message:

```text
Resolve merge conflicts
```

---

## 7️⃣ Feature-Branch zurück nach trunk mergen

Das ist der Abschluss-Merge:

```text
branch → trunk
```

Ziel: Die Änderungen aus deinem Feature-Branch kommen zurück nach `trunk`.

---

## ⚠️ Wichtig vor diesem Schritt

Du musst lokal auf **trunk** stehen.

Prüfen:

```text
Working Copy URL muss sein:
.../trunk
```

Nicht:

```text
.../branches/feature-PROJ-123-login-fix
```

---

### Schritt 1: Auf trunk wechseln

Falls deine Working Copy noch auf dem Branch steht:

1. Rechtsklick auf Working Copy
2. **TortoiseSVN → Switch...**
3. Bei **To URL** eintragen:

   ```text
   .../trunk
   ```

4. Revision:

   ```text
   HEAD revision
   ```

5. Mit **OK** bestätigen

---

### Schritt 2: trunk aktualisieren

1. Rechtsklick auf Working Copy
2. **TortoiseSVN → Update**

Damit ist dein lokaler `trunk` auf dem aktuellen Stand.

---

### Schritt 3: Branch in trunk mergen

1. Rechtsklick auf deine trunk Working Copy
2. **TortoiseSVN → Merge...**
3. Im Merge-Wizard auswählen:

   ```text
   Merge a range of revisions
   ```

4. Bei **URL to merge from** eintragen:

   ```text
   .../branches/feature-PROJ-123-login-fix
   ```

5. Revision range leer lassen oder **all revisions** verwenden
6. Weiter
7. Optional zuerst:

   ```text
   Test merge
   ```

8. Wenn der Test gut aussieht: Merge ausführen
9. Änderungen prüfen:

   ```text
   TortoiseSVN → Check for Modifications
   ```

10. Projekt bauen/testen
11. Committen:

    ```text
    SVN Commit...
    ```

Commit Message:

```text
Merge feature-PROJ-123-login-fix into trunk
```

---

## ✅ Merksatz

Wenn du dein Feature fertigstellen willst:

```text
Du stehst auf trunk.
Du mergst vom Branch.
```

Also:

```text
branch → trunk
```

---

## 8️⃣ Branch nach erfolgreichem Merge löschen

Nach erfolgreichem Merge kann der Feature-Branch gelöscht werden.

Das löscht den Branch nur aus der aktuellen Repository-Ansicht. Die Historie bleibt in SVN erhalten.

---

### Schritte mit Repo-browser

1. Rechtsklick auf deine Working Copy
2. **TortoiseSVN → Repo-browser**
3. Zu folgendem Pfad navigieren:

   ```text
   /branches
   ```

4. Deinen Branch auswählen:

   ```text
   feature-PROJ-123-login-fix
   ```

5. Rechtsklick auf den Branch
6. **Delete**
7. Commit Message eintragen:

   ```text
   Remove merged feature branch feature-PROJ-123-login-fix
   ```

8. Mit **OK** bestätigen

---

## 9️⃣ Optional: Tag vor dem Löschen erstellen

Wenn du vor dem Löschen des Branches einen Snapshot behalten möchtest:

1. Rechtsklick auf Working Copy oder im Repo-browser auf den Branch
2. **TortoiseSVN → Branch/Tag...**
3. Quelle:

   ```text
   .../branches/feature-PROJ-123-login-fix
   ```

4. Ziel:

   ```text
   .../tags/proj-123-before-cleanup
   ```

5. Commit Message:

   ```text
   Tag PROJ-123 before branch cleanup
   ```

6. Mit **OK** bestätigen

Für Releases wird meistens `trunk` getaggt:

```text
.../trunk
```

nach:

```text
.../tags/release-1.0.0
```

---

## 🧪 Sicherer Ablauf mit Test Merge

Wenn du unsicher bist, nutze vor echten Merges immer:

```text
Test merge
```

Vorteile:

- du siehst mögliche Konflikte vorher
- deine Working Copy wird nicht verändert
- du kannst die Merge-Richtung prüfen
- du reduzierst das Risiko eines falschen Merges

---

## 🧹 Wenn etwas schiefgeht

Solange du den Merge noch **nicht committed** hast, kannst du die lokalen Merge-Änderungen verwerfen.

### Über TortoiseSVN

1. Rechtsklick auf Working Copy
2. **TortoiseSVN → Revert...**
3. Alle Merge-Änderungen auswählen
4. Mit **OK** bestätigen

Danach ist deine Working Copy wieder im Zustand vor dem Merge.

> Wichtig: Das funktioniert nur für lokale, noch nicht committed Änderungen.

---

## ↩️ Wenn ein falscher Merge bereits committed wurde

Wenn der falsche Merge bereits committed wurde, nicht einfach lokal revertieren.

Dann brauchst du einen **Reverse Merge** bzw. einen neuen Commit, der die falsche Revision rückgängig macht.

Siehe dazu:

```text
svn-commands-DE.md
```

Abschnitt:

```text
Commit rückgängig machen
```

---

## 📋 Quick Checklist: Branch erstellen

```text
[ ] Working Copy steht auf trunk
[ ] Rechtsklick → TortoiseSVN → Branch/Tag...
[ ] From URL zeigt auf .../trunk
[ ] To URL zeigt auf .../branches/<FEATURE_NAME>
[ ] Commit Message eingetragen
[ ] Optional: Switch working copy to new branch/tag aktiviert
[ ] Danach prüfen: Working Copy zeigt auf Branch
```

---

## 📋 Quick Checklist: trunk in Branch mergen

```text
Ziel: Branch aktualisieren

[ ] Working Copy steht auf Branch
[ ] TortoiseSVN → Update
[ ] TortoiseSVN → Merge...
[ ] Merge a range of revisions
[ ] URL to merge from: .../trunk
[ ] Optional: Test merge
[ ] Merge ausführen
[ ] Check for Modifications
[ ] Projekt testen
[ ] Commit: Merge trunk into <FEATURE_NAME>
```

Merksatz:

```text
Ich stehe auf Branch und hole trunk rein.
```

---

## 📋 Quick Checklist: Branch in trunk mergen

```text
Ziel: Feature abschließen

[ ] Working Copy steht auf trunk
[ ] TortoiseSVN → Update
[ ] TortoiseSVN → Merge...
[ ] Merge a range of revisions
[ ] URL to merge from: .../branches/<FEATURE_NAME>
[ ] Optional: Test merge
[ ] Merge ausführen
[ ] Check for Modifications
[ ] Projekt testen
[ ] Commit: Merge <FEATURE_NAME> into trunk
```

Merksatz:

```text
Ich stehe auf trunk und hole den Branch rein.
```

---

## 🧭 Mini-Merksätze

```text
Branch erstellen:
trunk wird nach branches/<feature> kopiert.

Branch aktualisieren:
Ich stehe auf Branch und merge von trunk.

Feature abschließen:
Ich stehe auf trunk und merge vom Branch.

Branch löschen:
Erst nach erfolgreichem Merge nach trunk löschen.

Nicht committed?
Dann kann ich lokale Merge-Änderungen mit Revert zurücknehmen.

Bereits committed?
Dann brauche ich einen Reverse Merge.
```