# slnm-j-mvn
Selenium Java Maven Setup on macOS

This guide sets up a Selenium test automation project with:

- Java 17
- Apache Maven
- macOS
- Visual Studio Code
- Google Chrome
- Selenium WebDriver
- JUnit Jupiter

## Prerequisites

You need:

- A Mac with an internet connection
- Administrator access for installing software
- Google Chrome
- Visual Studio Code

## 1. Install Homebrew

Check whether Homebrew is installed:

```bash
brew --version
```

If the command is unavailable, install Homebrew by following the instructions at [brew.sh](https://brew.sh/), then reopen Terminal.

## 2. Install Java 17

Install Eclipse Temurin JDK 17:

```bash
brew install --cask temurin@17
```

Configure Java 17 in the default macOS shell:

```bash
echo 'export JAVA_HOME=$(/usr/libexec/java_home -v 17)' >> ~/.zshrc
echo 'export PATH="$JAVA_HOME/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc
```

Verify the installation:

```bash
java -version
javac -version
echo $JAVA_HOME
```

The Java and Java compiler versions should begin with `17`.

## 3. Install Maven

Install Maven with Homebrew:

```bash
brew install maven
```

Verify Maven and the Java version it uses:

```bash
mvn -version
```

The output should show Apache Maven and Java 17.

## 4. Install Google Chrome

Download and install [Google Chrome](https://www.google.com/chrome/) if it is not already installed. Open Chrome at least once after installation.

Selenium Manager automatically prepares a compatible ChromeDriver when the test runs, so a separate manual ChromeDriver installation is normally unnecessary.

## 5. Install Visual Studio Code

Download and install [Visual Studio Code](https://code.visualstudio.com/Download).

Open VS Code and install the Microsoft **Extension Pack for Java**:

1. Press `Command + Shift + X`.
2. Search for `Extension Pack for Java`.
3. Select the extension published by Microsoft.
4. Click **Install**.

The extension pack includes Java language support, Java debugging, Java testing, Maven support, and Java project management.

## 6. Configure Java in VS Code

1. Press `Command + Shift + P`.
2. Run `Java: Configure Java Runtime`.
3. Confirm that Temurin JDK 17 is detected.

If necessary, select the JDK 17 installation under:

```text
/Library/Java/JavaVirtualMachines/
```

## 7. Create the Maven project

In VS Code:

1. Press `Command + Shift + P`.
2. Run `Java: Create Java Project`.
3. Select `Maven`.
4. Select `maven-archetype-quickstart`.
5. Select the latest non-SNAPSHOT archetype version.
6. Select a parent directory for the project.
7. Enter `com.example` for the Group ID.
8. Enter `selenium-vscode` for the Artifact ID.
9. Open the generated project in VS Code.
10. Wait for Java and Maven project importing to finish.

## 8. Configure `pom.xml`

Replace the generated `pom.xml` with:

```xml
<?xml version="1.0" encoding="UTF-8"?>

<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                             https://maven.apache.org/xsd/maven-4.0.0.xsd">

    <modelVersion>4.0.0</modelVersion>

    <groupId>com.example</groupId>
    <artifactId>selenium-vscode</artifactId>
    <version>1.0-SNAPSHOT</version>

    <properties>
        <maven.compiler.release>17</maven.compiler.release>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <selenium.version>4.46.0</selenium.version>
        <junit.version>6.1.1</junit.version>
    </properties>

    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.junit</groupId>
                <artifactId>junit-bom</artifactId>
                <version>${junit.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <dependencies>
        <dependency>
            <groupId>org.seleniumhq.selenium</groupId>
            <artifactId>selenium-java</artifactId>
            <version>${selenium.version}</version>
            <scope>test</scope>
        </dependency>

        <dependency>
            <groupId>org.junit.jupiter</groupId>
            <artifactId>junit-jupiter</artifactId>
            <scope>test</scope>
        </dependency>

        <dependency>
            <groupId>org.junit.platform</groupId>
            <artifactId>junit-platform-launcher</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-surefire-plugin</artifactId>
                <version>3.5.5</version>
            </plugin>
        </plugins>
    </build>

</project>
```

Save the file and allow Maven to download the dependencies.

## 9. Create a Selenium test

Delete the generated test file:

```text
src/test/java/com/example/AppTest.java
```

Create this file instead:

```text
src/test/java/com/example/SeleniumTest.java
```

Add the following code:

```java
package com.example;

import java.time.Duration;

import org.junit.jupiter.api.Test;
import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.chrome.ChromeDriver;
import org.openqa.selenium.support.ui.ExpectedConditions;
import org.openqa.selenium.support.ui.WebDriverWait;

import static org.junit.jupiter.api.Assertions.assertEquals;

class SeleniumTest {

    @Test
    void submitSeleniumWebForm() {
        WebDriver driver = new ChromeDriver();

        try {
            driver.get(
                "https://www.selenium.dev/selenium/web/web-form.html"
            );

            assertEquals("Web form", driver.getTitle());

            WebElement textBox =
                driver.findElement(By.name("my-text"));

            WebElement submitButton =
                driver.findElement(By.cssSelector("button"));

            textBox.sendKeys("Selenium with Java");
            submitButton.click();

            WebDriverWait wait =
                new WebDriverWait(driver, Duration.ofSeconds(5));

            WebElement message = wait.until(
                ExpectedConditions.visibilityOfElementLocated(
                    By.id("message")
                )
            );

            assertEquals("Received!", message.getText());
        } finally {
            driver.quit();
        }
    }
}
```

## 10. Check the project structure

The project should look similar to:

```text
selenium-vscode/
├── pom.xml
└── src/
    ├── main/
    │   └── java/
    │       └── com/
    │           └── example/
    │               └── App.java
    └── test/
        └── java/
            └── com/
                └── example/
                    └── SeleniumTest.java
```

## 11. Run the test from VS Code

1. Open `SeleniumTest.java`.
2. Find the **Run Test** link above the test method.
3. Click **Run Test**.

Chrome should open, complete the web form, verify the result, and close automatically.

You can also select the beaker icon in the VS Code Activity Bar and run the test from **Testing Explorer**.

## 12. Run the test with Maven

Open **Terminal > New Terminal** in VS Code, then run:

```bash
mvn test
```

A successful run should end with output similar to:

```text
Tests run: 1, Failures: 0, Errors: 0, Skipped: 0
BUILD SUCCESS
```

## 13. Debug the test

1. Open `SeleniumTest.java`.
2. Click beside a line number to add a breakpoint.
3. Click **Debug Test** above the test method.
4. Use the VS Code debug controls to inspect variables and continue execution.

## Optional: Run Chrome in headless mode

Headless mode runs Chrome without displaying a browser window. Add this import:

```java
import org.openqa.selenium.chrome.ChromeOptions;
```

Then replace:

```java
WebDriver driver = new ChromeDriver();
```

with:

```java
ChromeOptions options = new ChromeOptions();
options.addArguments("--headless=new");

WebDriver driver = new ChromeDriver(options);
```

## Troubleshooting

### Java imports are red

Open the VS Code Command Palette and run:

```text
Java: Clean Java Language Server Workspace
```

Select **Restart and delete**. You can also run:

```text
Maven: Reload Projects
```

### `mvn test` reports `invalid target release: 17`

Check the Java version used by Maven:

```bash
mvn -version
echo $JAVA_HOME
```

Both should point to Java 17 or newer.

### VS Code does not show Run Test

Confirm that:

- The Microsoft Extension Pack for Java is installed.
- The test is under `src/test/java`.
- The filename ends in `Test.java`.
- The method has the JUnit `@Test` annotation.
- Maven project importing has finished.

### ChromeDriver cannot be downloaded

The first test run requires internet access so Selenium Manager can locate or download a compatible driver. Check your network or proxy configuration and make sure Chrome is installed.

## References

- [Selenium documentation](https://www.selenium.dev/documentation/)
- [Selenium downloads](https://www.selenium.dev/downloads/)
- [JUnit User Guide](https://docs.junit.org/6.1.1/)
- [Maven installation](https://maven.apache.org/install.html)
- [VS Code Java documentation](https://code.visualstudio.com/docs/languages/java)
- [VS Code Java testing](https://code.visualstudio.com/docs/java/java-testing)
- [VS Code Maven support](https://code.visualstudio.com/docs/java/java-build)
