# Gradle Commands ⚙️

A curated list of useful Gradle commands, mirroring common Maven workflows.

---

## 📚 References

- [Gradle – Official Documentation](https://docs.gradle.org/)
- [Gradle Java Testing](https://docs.gradle.org/current/userguide/java_testing.html)

---

## 🧠 Basics

Using the **Gradle Wrapper** is recommended (`./gradlew` on Linux/macOS, `gradlew.bat` on Windows):
```
bash
# Show available tasks
./gradlew tasks

# Compile and run tests
./gradlew test

# Full build (compile, test, assemble)
./gradlew build

# Clean build (delete build/ first)
./gradlew clean build
```
---

## 🧪 Running Tests

### Run All Tests
```
bash
# All unit tests
./gradlew test
```
### Run a Specific Test Class
```
bash
# Run tests in a specific class
./gradlew test --tests "com.example.E2EVornamenAendernTest"
```
### Run a Specific Test Method
```
bash
# Run a single test method in a class
./gradlew test --tests "com.example.E2EVornamenAendernTest.bei_Fortschreibung_des_Vornamens_des_Kindes_wird_eine_Fortschreibungsnachricht_an_Nebenwohnung_ausserhalb_geschickt"
```
Wildcards are supported:
```
bash
# All tests in classes matching *E2E*
./gradlew test --tests "*E2E*"

# All methods called *Fortschreibung* in a class
./gradlew test --tests "com.example.E2EVornamenAendernTest.*Fortschreibung*"
```
---

## 🧩 Profiles / Environments (Gradle Properties)

Gradle kennt keine „Profiles“ wie Maven, aber du kannst **Properties** verwenden:
```
bash
# Pass a system property (similar to -D in Maven)
./gradlew test -Denv=dev
./gradlew test -Denv=e2e
```
In `build.gradle` or tests you can read it via:
```
groovy
System.getProperty("env")
```
You can also use **project properties**:
```
bash
# Project property (accessed as project.propertyName)
./gradlew build -Penv=e2e
```
In `build.gradle`:
```
groovy
if (project.hasProperty('env') && project.env == 'e2e') {
// configure e2e-specific settings
}
```
---

## 🎯 E2E / Integration Tests (Gradle)

A common pattern is to define a separate test task, e.g. `e2eTest`:
```
bash
# Run integration / E2E tests (if such a task exists)
./gradlew e2eTest

# Run both unit tests and E2E tests
./gradlew test e2eTest
```
If you map Maven-like setups:

- Maven `mvn install -Pe2e,...` → `./gradlew e2eTest -Penv=e2e`
- Maven `-Dtest=MyTest#method` → `./gradlew test --tests "pkg.MyTest.method"`

---

## ⚙️ Configuring an `e2eTest` Task (Groovy DSL)

Minimal example for `build.gradle` (Groovy DSL) to mirror Maven-style E2E/Failsafe tests:
```
groovy
plugins {
id 'java'
}

repositories {
mavenCentral()
}

dependencies {
// Normal unit test dependencies
testImplementation 'org.junit.jupiter:junit-jupiter:5.10.0'

    // E2E / integration test dependencies
    e2eTestImplementation 'org.junit.jupiter:junit-jupiter:5.10.0'
}

// 1) Extra source set for E2E tests (src/e2eTest/java, src/e2eTest/resources)
sourceSets {
e2eTest {
java {
compileClasspath += sourceSets.main.output + configurations.testRuntimeClasspath
runtimeClasspath += output + compileClasspath
}
resources {
// default: src/e2eTest/resources
}
}
}

// 2) Configurations so e2eTestImplementation reuses test dependencies
configurations {
e2eTestImplementation.extendsFrom testImplementation
e2eTestRuntimeOnly.extendsFrom testRuntimeOnly
}

// 3) E2E test task
task e2eTest(type: Test) {
description = 'Runs end-to-end tests.'
group = 'verification'

    testClassesDirs = sourceSets.e2eTest.output.classesDirs
    classpath       = sourceSets.e2eTest.runtimeClasspath

    useJUnitPlatform()

    // Example system properties (can be read in tests)
    systemProperty 'env', project.findProperty('env') ?: 'local'
}

// 4) Run e2eTest automatically with the standard verification phase
check.dependsOn e2eTest
```
Typical folder structure:
```
text
src/
main/java/...
test/java/...         # unit tests
e2eTest/java/...      # E2E / integration tests
e2eTest/resources/...
```
Run commands (analog zu Maven `-Dtest=...`):
```
bash
# All E2E tests
./gradlew e2eTest

# Single E2E test class
./gradlew e2eTest --tests "de.example.e2e.E2EVornamenAendernTest"

# Single E2E test method
./gradlew e2eTest --tests "de.example.e2e.E2EVornamenAendernTest.bei_Fortschreibung_des_Vornamens_des_Kindes_wird_eine_Fortschreibungsnachricht_an_Nebenwohnung_ausserhalb_geschickt"

# E2E tests for env=e2e (similar to profiles)
./gradlew e2eTest -Penv=e2e
```
---

## 🚦 Skipping Tests
```
bash
# Build without running tests
./gradlew build -x test

# Skip a custom test task (e.g. e2eTest)
./gradlew build -x e2eTest
```
---

## 🧰 Dependencies & Insight
```
bash
# Show dependency tree for main source set
./gradlew dependencies

# Show dependency tree for a specific configuration
./gradlew dependencies --configuration runtimeClasspath

# Inspect why a certain dependency is on the classpath
./gradlew dependencyInsight --dependency junit
```
---

## 🔍 Useful Diagnostics
```
bash
# Show Gradle version
./gradlew -v

# Run with more logging
./gradlew build --info
./gradlew build --debug

# Show build scans if enabled (performance, issues, etc.)
./gradlew build --scan
```
---

## 🔄 Wrapper

Make sure your project uses the wrapper so everyone uses the same Gradle version:
```
bash
# Generate / update Gradle Wrapper files
gradle wrapper --gradle-version 8.9
```
Then commit:

- `gradlew`
- `gradlew.bat`
- `gradle/wrapper/gradle-wrapper.jar`
- `gradle/wrapper/gradle-wrapper.properties`

---
