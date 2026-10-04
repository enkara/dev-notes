# 🤖 Claude Code CLI – Spickzettel & Dokumentation

Eine umfassende Übersicht zur Installation, Nutzung und Konfiguration von **Claude Code**, dem terminalbasierten KI-Coding-Agenten von Anthropic.

---

## 📥 1. Installation & Setup

### Voraussetzungen
* **Node.js** (Version 18 oder neuer)
* **Git** (für Versionskontroll-Integration)

### Installation
Du kannst Claude Code global über npm oder per Shell-Script installieren:

```bash
# via npm
npm install -g @anthropic-ai/claude-code

# oder per Curl Install-Script
curl -fsSL https://claude.ai/install.sh | bash
```

### Authentifizierung
Navigiere in ein beliebiges Projektverzeichnis und starte den Authentifizierungsprozess:

```bash
claude
```
Beim ersten Aufruf öffnet sich der Browser zur Anmeldung (Claude Pro / Max Account oder Anthropic Console API Key).

---

## 🚀 2. Grundlegende Nutzung

Claude Code arbeitet direkt in deinem Projekt-Repository. Es liest Dateien, führt Terminal-Befehle aus (mit deiner Bestätigung) und kann Git-Commits sowie PRs erstellen.

### Interaktiver Modus (REPL)
```bash
# In dein Projektverzeichnis wechseln
cd /pfad/zu/deinem/projekt

# Interaktive Sitzung starten
claude

# Starten mit einer konkreten Einstiegsaufgabe
claude "Analysiere das Projekt und erkläre die Architektur"
```

### Einmal-Befehle / Automation (`-p` / Print Mode)
Nutze das `-p` Flag, um Aufgaben direkt auszuführen, ohne in die interaktive Schleife einzutreten:

```bash
# Direktaufruf im Terminal
claude -p "Finde unbehandelte Exceptions im src/-Ordner"

# Pipelines mit Unix-Befehlen nutzen
tail -n 100 app.log | claude -p "Gibt es kritische Fehler in diesem Log?"

# Git-Integration
git diff | claude -p "Erstelle eine aussagekräftige Commit-Message für diese Änderungen"
```

---

## 🛠️ 3. Wichtigste CLI-Flags beim Start

| Flag | Beschreibung | Beispiel |
| :--- | :--- | :--- |
| `-p`, `--print` | Führt den Prompt aus, gibt das Ergebnis aus und beendet sich | `claude -p "Führe Tests aus"` |
| `-c`, `--continue` | Setzt die vorherige Sitzung im aktuellen Ordner fort | `claude -c` |
| `--dangerously-skip-permissions` | Übergeht Bestätigungsaufforderungen für Tool-Ausführungen | `claude --dangerously-skip-permissions` |
| `--model <modell>` | Ändert das zu verwendende Modell | `claude --model claude-3-5-sonnet` |

---

## 💬 4. In-Chat Befehle (Slash Commands)

Wenn du dich in der interaktiven Claude Code-Sitzung befindest, kannst du folgende Steuerungsbefehle nutzen:

### Kontext & Modell-Steuerung
* `/plan` – Schaltet in den Planungs-Modus um, um komplexe Änderungen vorab zu strukturieren.
* `/compact` – Komprimiert den bisherigen Gesprächsverlauf, um Kontext-Speicherplatz (Tokens) freizugeben.
* `/clear` – Leert den Chatverlauf und startet eine frische Sitzung im aktuellen Projekt.
* `/context` – Zeigt an, wie stark das Kontextfenster aktuell gefüllt ist.
* `/effort [low|medium|high]` – Stellt ein, wie viel Reasoning/Denkzeit Claude für Aufgaben aufwendet.

### Projekt & Tools
* `/init` – Generiert automatisch eine `CLAUDE.md`-Konfigurationsdatei für das aktuelle Repository.
* `/mcp` – Verwaltet und zeigt verbundene **MCP-Server** (Model Context Protocol) an.
* `/permissions` – Konfiguriert, welche Befehle/Tools automatisch ausgeführt werden dürfen.
* `/code-review` oder `/review` – Prüft den aktuellen Git-Diff auf Fehler und Optimierungspotenzial.

### Sitzungsverwaltung & Hilfe
* `/resume` – Zeigt eine Liste früherer Sitzungen an, um zu einer zurückzukehren.
* `/bug` – Meldet ein Problem oder teilt ein Fehler-Protokoll.
* `/help` – Zeigt die integrierte Hilfe mit allen verfügbaren Befehlen.
* `/exit` oder `Ctrl + D` – Beendet die aktuelle Claude Code-Sitzung.

---

## 📄 5. Projekt-Konfiguration (`CLAUDE.md`)

Durch Anlegen einer `CLAUDE.md`-Datei im Root-Verzeichnis deines Projekts kannst du Claude Code projektspezifische Anweisungen und Standards mitgeben. Claude liest diese Datei automatisch beim Start jeder Sitzung.

### Beispiel `CLAUDE.md` für ein Java-Projekt:

```markdown
# Projekt-Richtlinien für Claude Code

## Build & Test Befehle
- Bauen: `./gradlew build`
- Tests ausführen: `./gradlew test`
- Einzelnen Test ausführen: `./gradlew test --tests *UserServiceTest*`

## Architektur & Code-Style
- Nutze Java 21 Syntax (Records, Pattern Matching, Sealed Classes).
- Verwende Spring Boot 3 Best Practices.
- Vermeide Lombok, wenn Standard-Java-Records genutzt werden können.
- Alle öffentlichen API-Methoden müssen JavaDoc-Dokumentation enthalten.

## Testing Standards
- Schreiben von Unit-Tests mit JUnit 5 und AssertJ.
- Mocks mit Mockito erstellen.
```

---

## 🔌 6. Einbindung von MCP (Model Context Protocol)

Mit MCP kannst du Claude Code mit externen Systemen (wie Datenbanken, GitHub, Jira oder lokalen Tools) verbinden.

```bash
# MCP Server hinzufügen (Beispiel für GitHub)
claude mcp add github npx -y @modelcontextprotocol/server-github

# MCP Server auflisten / prüfen
# In der Claude-Sitzung:
/mcp
```

---

## 💡 7. Bewährte Workflows & Tipps

1. **Erst planen, dann coden:**
   Nutze bei größeren Refactorings erst `/plan` oder fordere Claude auf: *"Erstelle zuerst einen Schritt-für-Schritt-Plan, bevor du Dateien änderst."*
2. **Kleine Commits:**
   Lass Claude kleine, in sich geschlossene Änderungen durchführen und direkt testen: *"Implementiere X, führe `./gradlew test` aus und erstelle einen Commit, wenn alles grün ist."*
3. **Kontext schlank halten:**
   Wenn Sitzungen sehr lang werden, nutze `/compact` oder `/clear`, um Verwirrung durch zu alte Kontext-Nachrichten zu vermeiden.
