# Maven Commands 📦

A curated list of useful Maven commands and options for everyday Java development.

---

## 📚 References

- [Maven – Official Documentation](https://maven.apache.org/guides/index.html)
- [Maven Surefire Plugin (unit tests)](https://maven.apache.org/surefire/maven-surefire-plugin/)
- [Maven Failsafe Plugin (integration tests)](https://maven.apache.org/surefire/maven-failsafe-plugin/)

---

## 🧠 Basics
``` bash

# Compile the project
mvn compile

# Run tests and build artifacts (e.g. JAR/WAR) and install to local repo
mvn install

# Run a clean build (delete target/ first)
mvn clean install
```
Typical lifecycle phases (in order):

- `clean` – remove `target/`  
- `compile` – compile main sources  
- `test` – run unit tests  
- `package` – build JAR/WAR  
- `verify` – integration test phase (often via Failsafe)  
- `install` – install artifact to local `.m2` repository  
- `deploy` – deploy to remote repository  

---

## 🧪 Running Tests

### Run All Tests (Default)
``` bash

# Run unit tests during the build (default behaviour for mvn test / mvn install)
mvn test
mvn install
```
### Run Tests with Profiles (e.g. E2E / Integration)
``` bash

# Run build with specific profiles (example: e2e and integration-test-nb)
mvn install -Pe2e,integration-test-nb
```
- `-P` activates Maven profiles defined in `pom.xml`.
- Multiple profiles are comma-separated.

---

## 🎯 Run a Specific Test Class / Method

Maven uses the **Surefire** (for unit tests) and **Failsafe** plugins (for integration tests).

### Run a Single Test Class
``` bash

# Run only E2EVornamenAendernTest
mvn test -Dtest=E2EVornamenAendernTest

# or with install goal
mvn install -Dtest=E2EVornamenAendernTest
```
### Run a Single Test Method
``` bash

# Run a specific method in E2EVornamenAendernTest
mvn test -Dtest=E2EVornamenAendernTest#bei_Fortschreibung_des_Vornamens_des_Kindes_wird_eine_Fortschreibungsnachricht_an_Nebenwohnung_ausserhalb_geschickt
```
Combine with profiles (your Beispiel):
``` bash

mvn install \
-Pe2e,bis-integration-trunk-nb \
-Dtest=E2EVornamenAendernTest#bei_Fortschreibung_des_Vornamens_des_Kindes_wird_eine_Fortschreibungsnachricht_an_Nebenwohnung_ausserhalb_geschickt
```
Further patterns:
``` bash

# Run multiple test classes
mvn test -Dtest=MyTest1,MyTest2

# Run tests matching a pattern (wildcards)
mvn test -Dtest=My*Test
```
---

## 🚦 Skipping Tests
``` bash

# Skip tests during build (NOT recommended for CI, but useful locally)
mvn install -DskipTests

# Skip compiling tests as well (faster, but more drastic)
mvn install -Dmaven.test.skip=true
```
---

## 🧩 Profiles & Environments
``` bash

# Activate a profile (e.g. dev, qa, prod)
mvn clean install -Pdev
mvn clean install -Pqa

# Activate multiple profiles
mvn clean install -Pdev,feature-x
```
Check which profiles are active:
``` bash

mvn help:active-profiles
```
List all available profiles:
``` bash 

mvn help:all-profiles
```
---

## 🧰 Dependency Management
``` bash

# Show dependency tree
mvn dependency:tree

# Show dependency tree including conflict resolution details
mvn dependency:tree -Dverbose

# Download all dependencies (offline preparation)
mvn dependency:go-offline
```
---

## 🔍 Useful Diagnostics
``` bash

# Show Maven version and environment
mvn -v

# Run Maven in debug mode (very verbose)
mvn clean install -X
```
---

## 🧪 Integration Tests Only (Failsafe)

If you use **Failsafe** with `integration-test` and `verify`:
``` bash

# Run only integration tests (if configured this way)
mvn verify -Pintegration-test-nb

# Skip unit tests but run integration tests
mvn verify -DskipTests -Pintegration-test-nb
```
---
