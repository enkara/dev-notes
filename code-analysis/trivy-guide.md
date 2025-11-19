# Security Analysis with Trivy

## 1\. Overview

**Trivy** (by Aqua Security) is a comprehensive, open-source security scanner. Unlike static analysis tools (SAST) like Fortify that look for logic errors in code, Trivy focuses on **vulnerabilities (CVEs)** in dependencies and configuration issues.

- **Type:** SCA (Software Composition Analysis) & Misconfiguration Scanner.
- **License:** Apache 2.0 (Open Source).
- **Supports:** Container Images, Filesystems, Git Repositories.
- **Languages:** Java (Maven/Gradle), Node.js/React (npm/yarn/pnpm), Python, Go, etc.

---

## 2\. Installation

### Windows

Using Winget (Recommended):

```powershell
winget install -e --id AquaSecurity.Trivy
```

Or using Chocolatey:

```powershell
choco install trivy
```

### macOS

Using Homebrew:

```bash
brew install trivy
```

### Linux (Ubuntu/Debian)

```bash
sudo apt-get install wget apt-transport-https gnupg lsb-release
wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | sudo apt-key add -
echo deb https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -sc) main | sudo tee -a /etc/apt/sources.list.d/trivy.list
sudo apt-get update
sudo apt-get install trivy
```

### Docker (No Installation)

Run Trivy as a container scanning the current directory (`$PWD`):

```bash
docker run --rm -v $PWD:/root/.cache/ -v $PWD:/app -w /app aquasec/trivy fs .
```

---

## 3\. Basic Usage

To scan your project (filesystem scan), navigate to the project root and run:

```bash
trivy fs .
```

### Language Specifics

- **Java:** Trivy looks for `pom.xml`, `build.gradle`, or compiled `.jar` files.
- **Node.js / React:** Trivy looks for `package-lock.json`, `yarn.lock`, or `pnpm-lock.yaml`.
  - _Note:_ Ensure lock-files are present for the most accurate results.

---

## 4\. Filtering & Best Practices

Scanning everything often results in too much noise. Use these flags to focus on what matters.

### Filter by Severity

Only show **Critical** and **High** vulnerabilities:

```bash
trivy fs --severity CRITICAL,HIGH .
```

### Ignore Unfixed Vulnerabilities

Don't show vulnerabilities that have no vendor fix available yet:

```bash
trivy fs --ignore-unfixed .
```

### Skip Directories (e.g., Tests)

It is common practice to exclude test folders or documentation, as vulnerabilities there do not affect the production build.

```bash
# Example: Skip test folder and node_modules
trivy fs --skip-dirs src/test,node_modules .
```

### Ignore Specific False Positives (`.trivyignore`)

If Trivy reports a vulnerability that does not affect your usage context, you can ignore it using a `.trivyignore` file in your project root.

1.  Create a file named `.trivyignore`.
2.  Add the CVE IDs to ignore.

**Example `.trivyignore`:**

```text
# Ignored because we do not use the affected function of this library
CVE-2023-1234
CVE-2022-5678
```

---

## 5\. Reporting (HTML Output)

For a better overview or to share results with the team, you can generate an HTML report instead of the terminal output.

```bash
trivy fs --format template --template "@contrib/html.tpl" -o report.html .
```

- `--format template`: Tells Trivy to use a template.
- `--template "@contrib/html.tpl"`: Uses the built-in HTML template.
- `-o report.html`: Saves the output to a file.

---

## 6\. Configuration File (`trivy.yaml`)

Instead of typing long commands, you can store your configuration in a `trivy.yaml` file in the project root.

**Example `trivy.yaml`:**

```yaml
scan:
  subcommand: fs

format: table # or 'template' for reports
severity:
  - CRITICAL
  - HIGH

skip-dirs:
  - src/test
  - docs
  - node_modules
  - .git
# Auto-detects .trivyignore file if present
```

**Usage with config:**

```bash
trivy fs .
```

---

## 7\. Summary Cheat Sheet

| Goal                | Command                                                                      |
| :------------------ | :--------------------------------------------------------------------------- |
| **Quick Scan**      | `trivy fs .`                                                                 |
| **Production Scan** | `trivy fs --severity CRITICAL,HIGH --ignore-unfixed .`                       |
| **Skip Tests**      | `trivy fs --skip-dirs src/test .`                                            |
| **Generate HTML**   | `trivy fs --format template --template "@contrib/html.tpl" -o report.html .` |
