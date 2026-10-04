# 🦙 Ollama – Spickzettel & CLI-Dokumentation

Eine Übersicht zur lokalen Installation, den wichtigsten Befehlen, Modelfiles und der Nutzung in der Kommandozeile sowie in **Java**.

---

## 📥 1. Installation

Ollama lässt sich einfach für das jeweilige Betriebssystem installieren:

* **macOS:** Download von der offiziellen Website [ollama.com](https://ollama.com/download)
* **Windows:** Download des Installers (`.exe`) von [ollama.com](https://ollama.com/download)
* **Linux:** Über das Terminal per Script-Installation:
  ```bash
  curl -fsSL https://ollama.com/install.sh | sh
  ```

---

## 🚀 2. Schnelleinstieg (Quickstart)

Um ein Modell direkt herunterzuladen und im Terminal mit ihm zu chatten, führe folgenden Befehl aus:

```bash
ollama run llama3
```

---

## 🛠️ 3. Übersicht der CLI-Befehle

| Befehl | Beschreibung | Beispiel |
| :--- | :--- | :--- |
| `ollama run <modell>` | Lädt das Modell (falls nötig) und startet den Chat im Terminal | `ollama run mistral` |
| `ollama pull <modell>` | Lädt ein Modell herunter, ohne es direkt zu starten | `ollama pull codellama` |
| `ollama list` / `ollama ls` | Listet alle lokal installierten Modelle auf | `ollama list` |
| `ollama ps` | Zeigt aktuell geladene/aktive Modelle und Speicherverbrauch an | `ollama ps` |
| `ollama rm <modell>` | Löscht ein lokales Modell von der Festplatte | `ollama rm gemma:2b` |
| `ollama cp <alt> <neu>` | Erstellt eine Kopie eines bestehenden Modells | `ollama cp llama3 mein-llama` |
| `ollama show <modell>` | Zeigt Details und Metadaten zu einem Modell an | `ollama show llama3` |
| `ollama create <name> -f <file>` | Erstellt ein benutzerdefiniertes Modell aus einem Modelfile | `ollama create mein-bot -f ./Modelfile` |
| `ollama serve` | Startet den Ollama-Hintergrunddienst | `ollama serve` |

---

## 💬 4. Bedienung der Chat-Schnittstelle (REPL)

Wenn du `ollama run <modell>` ausführst, öffnet sich eine interaktive Eingabeaufforderung (`>>>`).

### Wichtige Chat-Befehle:
* `/?` oder `/help` – Zeigt eine Hilfeseite mit allen In-Chat-Befehlen an.
* `/clear` – Löscht den bisherigen Gesprächsverlauf der Sitzung.
* `/set system <text>` – Setzt eine benutzerdefinierte System-Prompt-Anweisung.
* `/show info` – Zeigt Informationen zum aktuell geladenen Modell an.
* `/bye` oder `Ctrl + D` – Beendet den Chat und kehrt ins Terminal zurück.

---

## ⚡ 5. Direktaufruf aus der Bash / Automation

Du kannst Prompts direkt über die Kommandozeile an ein Modell übergeben oder Pipelines nutzen:

```bash
# Direktes Prompting mit Ausgabe im Terminal
ollama run llama3 "Erkläre den Begriff REST-API in zwei Sätzen."

# Eingabe per Pipe aus einer Datei verarbeiten
cat main.py | ollama run codellama "Finde potenzielle Bugs in diesem Code."

# Ausgabe in eine Datei umleiten
ollama run llama3 "Schreibe ein kurzes Gedicht über Programmierer" > gedicht.txt
```

---

## 📄 6. Eigene Modelfiles erstellen (Custom Models)

Mit einem **Modelfile** kannst du eigene Modelle anpassen (z. B. System-Prompts, Temperatur oder Stop-Token definieren).

### 1. Datei namens `Modelfile` anlegen:

```dockerfile
# Basismodell festlegen
FROM llama3

# Parameter anpassen (z. B. Kreativität steuern)
PARAMETER temperature 0.7
PARAMETER top_p 0.9

# System-Prompt definieren (Persona & Verhalten)
SYSTEM """
Du bist ein erfahrener Senior Java Developer. 
Du antwortest präzise, nutzt sauberen Code nach Clean-Code-Prinzipien und antwortest immer auf Deutsch.
"""
```

### 2. Modell erstellen und ausführen:

```bash
# Modell mit dem Namen "java-expert" erstellen
ollama create java-expert -f ./Modelfile

# Eigenes Modell starten
ollama run java-expert "Wie erstelle ich einen Singleton in Java?"
```

---

## ☕ 7. Ollama mit Java nutzen

Ollama läuft lokal als REST-API unter `http://localhost:11434`. Du kannst Ollama in Java über Frameworks oder direkt per `HttpClient` anbinden.

### Option A: Direkt über den 표준 Java `HttpClient` (Java 11+)

```java
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;

public class OllamaExample {
    public static void main(String[] args) throws Exception {
        HttpClient client = HttpClient.newHttpClient();

        // JSON-Payload für die API
        String jsonPayload = """
            {
              "model": "llama3",
              "prompt": "Schreibe eine 'Hello World' Methode in Java.",
              "stream": false
            }
            """;

        HttpRequest request = HttpRequest.newBuilder()
                .uri(URI.create("http://localhost:11434/api/generate"))
                .header("Content-Type", "application/json")
                .POST(HttpRequest.BodyPublishers.ofString(jsonPayload))
                .build();

        HttpResponse<String> response = client.send(request, HttpResponse.BodyHandlers.ofString());

        System.out.println("Antwort von Ollama:");
        System.out.println(response.body());
    }
}
```

### Option B: Bekannte Java-Bibliotheken & Frameworks

Für komplexere Anwendungen empfehlen sich spezialisierte Libraries:

1. **LangChain4j:**
   * Sehr populäre Bibliothek für LLM-Integrationen in Java.
   * Hat native Unterstützung für Ollama über das `langchain4j-ollama` Modul.
2. **Spring AI:**
   * Für Spring-Boot-Anwendungen die bevorzugte Wahl.
   * Bietet das `spring-ai-ollama-spring-boot-starter`-Paket zur einfachen Konfiguration in `application.properties`.
3. **Ollama4j:**
   * Ein leichtgewichtiger, dedizierter Java-Wrapper für die Ollama REST-API.

---

## 🌐 8. Wichtigste REST-Endpoints

| Endpoint | Methode | Beschreibung |
| :--- | :--- | :--- |
| `/api/generate` | `POST` | Erzeugt eine Antwort basierend auf einem Prompt |
| `/api/chat` | `POST` | Führt einen strukturierte Chat-Verlauf fort |
| `/api/tags` | `GET` | Zeigt alle lokal verfügbaren Modelle an |
| `/api/show` | `POST` | Zeigt Details zu einem bestimmten Modell |
