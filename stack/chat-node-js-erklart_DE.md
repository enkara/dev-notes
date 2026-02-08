# Node.js für Java-Entwickler

 Node.js im Vergleich zu Java/JVM, npm, Yarn, Builds, Repository, Cache

## 1. Was ist Node.js?

**Node.js** ist eine **Laufzeitumgebung**, die **JavaScript außerhalb des Browsers** ausführt.

- Basierend auf **V8 Engine** (Chrome)
- **Asynchron, event-basiert, non-blocking I/O**
- Ideal für: APIs, Microservices, Echtzeit-Apps (Chat, Streaming)

### Einfacher Webserver (Beispiel)

```js
const http = require("http");

http
  .createServer((req, res) => {
    res.end("Hallo von Node.js!");
  })
  .listen(3000);
```

---

## 2. Node.js = JVM für JavaScript?

| Feature              | **JVM (Java)**           | **Node.js (JS)**                 |
| -------------------- | ------------------------ | -------------------------------- |
| Sprache              | Java → Bytecode          | JavaScript (direkt)              |
| Engine               | Hot here                 | **V8 (JIT)**                     |
| Plattform            | Write once, run anywhere | **Write once, run anywhere**     |
| Garbage Collection   | Yes                      | Yes                              |
| Threading            | Echte Threads            | **Event-Loop (single-threaded)** |
| Standardbibliotheken | java.util, etc.          | `fs`, `http`, `path`, etc.       |

> **Fazit**: **Ja!** Node.js macht aus JS eine echte Backend-Sprache – wie JVM aus Java.

---

## 3. npm – Der Paketmanager (wie Maven)

- **npmjs.com**: Zentrales Repository (>2 Mio. Pakete)
- `package.json` = `pom.xml`
- `package-lock.json` = Reproduzierbarkeit
- **Globaler Cache**: `~/.npm` → spart Platz

### Alternativen

| Tool     | Vorteil                             |
| -------- | ----------------------------------- |
| **Yarn** | Schneller, `yarn.lock`, Workspaces  |
| **pnpm** | Hardlinks → minimaler Speicherplatz |

---

## 4. Build-Umgebung (wie Maven/Gradle)

### `package.json` (Manifest)

```json
{
  "name": "meine-app",
  "scripts": {
    "start": "node index.js",
    "dev": "nodemon index.js",
    "build": "vite build",
    "test": "jest"
  },
  "dependencies": {
    "express": "^4.18.0"
  },
  "devDependencies": {
    "vite": "^5.0.0"
  }
}
```

### Build-Tools (wie Maven-Plugins)

- **Vite** → Blitzschnell, zero-config
- **Webpack** → Voll konfigurierbar
- **esbuild** → Ultra-schnell
- **TypeScript** → `tsc` → JS (wie Java → Bytecode)

---

## 5. Global vs. Lokal vs. Cache

| Java (Maven)       | Node.js (npm)    |
| ------------------ | ---------------- |
| `~/.m2/repository` | `~/.npm` (Cache) |
| `pom.xml`          | `package.json`   |
| `mvn install`      | `npm install`    |
| JAR/WAR            | `dist/` (Bundle) |

---

## 6. Schnellstart-Projekt

```bash
mkdir meine-app && cd meine-app
npm init -y
npm install express
npm install --save-dev nodemon
```

`package.json` → Skript:

```json
"dev": "nodemon index.js"
```

→ `npm run dev`

---

## Fazit

> **Node.js + npm = JVM + Maven für JavaScript**  
> Einfacher, schneller, JSON-basiert, riesiges Ökosystem.

---

**Weiterführend:**

- [nodejs.org](https://nodejs.org)
- [npmjs.com](https://npmjs.com)
- Probiere: `npx create-vite@latest`

---

## 2. Wiederverwendbares Skript: `chat-to-md.js`

Speichere das als `chat-to-md.js` – dann kannst du **jeden zukünftigen Chat** in Markdown umwandeln.

```js
#!/usr/bin/env node
/**
 * chat-to-md.js
 * Wandelt kopierten Chat-Text in schönes Markdown um
 * Nutzung: node chat-to-md.js "mein-chat.txt" output.md
 */

const fs = require('fs');
const path = require('path');

const args = process.argv.slice(2);
if (args.length < 1) {
  console.log(`
  Chat zu Markdown Konverter
  ---------------------------
  Nutzung:
    node chat-to-md.js <input.txt> [output.md]

  Beispiel:
    node chat-to-md.js chat-kopie.txt node-js-erklart.md
  `);
  process.exit(1);
}

const inputFile = args[0];
const outputFile = args[1] || 'chat-output.md';

if (!fs.existsSync(inputFile)) {
  console.error(`Fehler: Datei nicht gefunden: ${inputFile}`);
  process.exit(1);
}

// Lese Chat
let raw = fs.readFileSync(inputFile, 'utf-8');

// === Formatierung ===

let lines = raw.split('\n');
let inCodeBlock = false;
let md = `# Chat mit Grok – ${new Date().toLocaleDateString('de-DE')}\n\n`;
md += `> Automatisch formatiertes Markdown\n\n---\n\n`;

// Verarbeite Zeile für Zeile
for (let line of lines) {
  line = line.trimEnd();

  // Codeblöcke erkennen
  if (line.includes('```')) {
    inCodeBlock = !inCodeBlock;
    md += `${line}\n`;
    continue;
  }

  if (inCodeBlock) {
    md += `${line}\n`;
    continue;
  }

  // User / Assistant erkennen
  if (line.startsWith('Human:') || line.startsWith('Du:')) {
    const text = line.replace(/^Human:|Du:\s*/, '').trim();
    if (text) md += `### 🙋 **Du:**\n${text}\n\n`;
  }
  else if (line.startsWith('Assistant:') || line.startsWith('Grok:')) {
    const text = line.replace(/^Assistant:|Grok:\s*/, '').trim();
    if (text) md += `### 🤖 **Grok:**\n${text}\n\n`;
  }
  else if (line.startsWith('>') || line.startsWith('|') || line.match(/^\d+\./) || line.match(/^[-*+]/)) {
    md += `${line}\n`;
  }
  else if (line.trim() === '') {
    md += `\n`;
  }
  else {
    md += `${line}\n\n`;
  }
}

md += `\n---\n*Erstellt mit [chat-to-md.js](https://gist.github.com/) – ${new Date().toISOString().split('T')[0]}*\n`;

// Schreibe Datei
fs.writeFileSync(outputFile, md.trim() + '\n', 'utf-8');
console.log(`Markdown gespeichert: ${path.resolve(outputFile)}`);
````

---

### So nutzt du das Skript

```bash
# 1. Skript speichern
curl -o chat-to-md.js https://gist.githubusercontent.com/.../chat-to-md.js
chmod +x chat-to-md.js

# 2. Chat kopieren → z. B. in chat.txt einfügen

# 3. Ausführen
node chat-to-md.js chat.txt node-js-chat.md
```

→ Fertig! Schönes Markdown mit:

- Überschriften
- Codeblöcken
- Emojis für Sprecher
- Trennlinien
- Datum

---

## Bonus: One-Liner (für Profis)

```bash
pbpaste | node chat-to-md.js /dev/stdin mein-chat.md
```

(Kopiere Chat → `Cmd+V` → automatisch in `mein-chat.md`)
