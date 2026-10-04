# ♊ Gemini CLI – Spickzettel & Dokumentation

Eine umfassende Übersicht zur Installation, Nutzung, Konfiguration und Nutzung von **MCP** mit dem offiziellen **Gemini CLI** Agenten von Google.

## 💡 Was ist MCP (Model Context Protocol)?

**MCP** ist ein universeller, von Anthropic entwickelter Open-Source-Standard. Er fungiert wie ein **"USB-C-Anschluss für KI-Modelle"**:
* Anstatt für jedes Tool (wie PostgreSQL, GitHub, Jira, Slack) eine eigene KI-Integration zu schreiben, stellt das Zielsystem einen **MCP-Server** bereit.
* Jeder kompatible **MCP-Client** (wie Gemini CLI, Claude Code oder Cursor) kann sich sofort verbinden und auf diese Daten/Tools zugreifen.

---

## 📥 1. Installation & Setup

### Voraussetzungen

* **Node.js** (Version 18 oder neuer)

### Installation

Das Gemini CLI kann global installiert oder direkt per `npx` ausgeführt werden:

```bash
# Empfohlen: Globale Installation via npm
npm install -g @google/gemini-cli

# Alternativ über Homebrew (macOS/Linux)
brew install gemini-cli

# Einmalige Ausführung ohne Installation
npx @google/gemini-cli
```

### Authentifizierung & Login

Navigiere in ein beliebiges Projektverzeichnis und führe den Befehl aus:

```bash
gemini
```

Beim ersten Start wird ein Browserfenster geöffnet, um dich mit deinem Google-Konto anzumelden (Standardmäßig stehen dir **60 Anfragen pro Minute** und **1.000 Anfragen pro Tag kostenlos** zur Verfügung).

---

## 🚀 2. Grundlegende Nutzung

Das Gemini CLI analysiert dein Projektverzeichnis, kann Dateien lesen, Vorschläge machen und Terminal-Befehle (nach deiner Bestätigung) ausführen.

### Interaktiver Modus (REPL)

```bash
# Wechsel in dein Projektverzeichnis
cd /pfad/zu/deinem/projekt

# Interaktive Sitzung starten
gemini

# Start mit direktem Auftrag
gemini "Erkläre mir die Architektur dieses Repositorys"
```

### Einmal-Befehle & Automation (`-p` / Print Mode)

Nutze das `-p` Flag für die Ausführung in Skripten oder CI/CD-Pipelines:

```bash
# Direktaufruf im Terminal
gemini -p "Finde unbehandelte Exceptions im src/-Ordner"

# Antworten im JSON-Format ausgeben
gemini -p "Finde Sicherheitslücken im Code" --output-format json

# Pipelines mit Unix-Befehlen nutzen
cat app.log | gemini -p "Gibt es kritische Fehler in diesem Log?"

# Spezifische Verzeichnisse miteinbeziehen
gemini --include-directories ../shared-lib,../docs
```

---

## 🛠️ 3. Wichtigste CLI-Flags beim Start

| Flag / Argument | Beschreibung | Beispiel |
| :--- | :--- | :--- |
| `-p`, `--print` | Führt den Prompt aus, gibt das Ergebnis im Terminal aus und beendet sich | `gemini -p "Führe Tests aus"` |
| `-m`, `--model` | Erzwingt die Nutzung eines bestimmten Gemini-Modells | `gemini -m gemini-2.5-flash` |
| `--output-format` | Formatiert das Ergebnis (z. B. `json` oder `text`) | `gemini -p "Analysiere" --output-format json` |
| `--include-directories` | Bindet zusätzliche Pfade außerhalb des aktuellen Ordners ein | `gemini --include-directories ../lib` |

---

## 💬 4. In-Chat Befehle (Slash Commands)

In einer laufenden interaktiven Sitzung stehen dir folgende Steuerungskommandos zur Verfügung:

| Command | Beschreibung |
| :--- | :--- |
| `/help` | Zeigt alle verfügbaren In-Chat-Befehle und Optionen an. |
| `/chat` | Verwalte oder betrachte deine bisherigen Chat-Sitzungen. |
| `/clear` | Leert den aktuellen Gesprächsverlauf und gibt Tokens frei. |
| `/checkpoint` | Speichert den aktuellen Stand der Konversation als Speicherpunkt ab. |
| `/resume` | Lädt eine zuvor gespeicherte Checkpoint-Sitzung. |

---

## 📄 5. Projekt-Konfiguration (`GEMINI.md`)

Lege eine `GEMINI.md`-Datei im Wurzelverzeichnis deines Projekts an. Gemini liest diese Datei bei jedem Start automatisch ein, um projektspezifische Konventionen, Architekturregeln und Befehle zu berücksichtigen.

### Beispiel `GEMINI.md` für ein Java / Spring Boot Projekt:

```markdown
# Projekt-Richtlinien für Gemini CLI

## Tech Stack
- Java 21, Spring Boot 3.x, PostgreSQL, Docker
- Build-Tool: Maven

## Build & Test Befehle
- Kompilieren: `./mvnw clean compile`
- Tests ausführen: `./mvnw test`
- Einzelnen Test ausführen: `./mvnw test -Dtest=UserServiceTest`

## Architecture & Code Rules
- Nutze Constructor Injection für Spring Beans (@Autowired an Feldern vermeiden).
- Nutze Java Records für unveränderliche DTOs.
- Schreibe Unit-Tests mit JUnit 5 und AssertJ.
- Alle öffentlichen Controller-Endpunkte müssen eine OpenAPI/Swagger-Dokumentation enthalten.
```

---

## 🔌 6. MCP (Model Context Protocol) Integration

Gemini CLI unterstützt MCP-Server, um externe Systeme (Datenbanken, GitHub, Jira, etc.) direkt im Terminal einzubinden.

### Konfiguration (`~/.gemini/settings.json`)

Füge deine MCP-Server in der Konfigurationsdatei deines Benutzerverzeichnisses ein:

```json
{
  "mcpServers": {
    "postgres": {
      "command": "npx",
      "args": [
        "-y",
        "@modelcontextprotocol/server-postgres",
        "postgresql://user:password@localhost:5432/mydb"
      ]
    },
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "dein-github-token"
      }
    }
  }
}
```

### Nutzung in der Prompt-Eingabe

Du kannst registrierte MCP-Server direkt per `@` ansprechen:

```bash
# Abfrage an eine Datenbank über den Postgres-MCP-Server
gemini > @postgres Zeige mir die Tabellenstruktur der Tabelle "users"

# Abfrage an GitHub über den GitHub-MCP-Server
gemini > @github Zeige mir alle offenen Pull Requests für dieses Repository
```
