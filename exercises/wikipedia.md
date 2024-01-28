# Random Wikipedia walker

Using Selenium, create a small program that, starting from the main page https://www.wikipedia.org/, walks trough a sequence of random links and takes a snapshot of the last page.
The process is as follows:

 1. Navigate to the main page https://www.wikipedia.org/
 2. Select a random link in the page
 3. Navigate to the link
 4. Repeat steps 2 to 3 until you have visited 10 different pages
 5. Take a snapshot of the current page and save it

Include the code of the walker and the snapshot in this document.

## Answer

```java
package org.example;

import org.apache.commons.io.FileUtils;
import org.openqa.selenium.*;
import org.openqa.selenium.firefox.FirefoxDriver;
import org.openqa.selenium.support.ui.WebDriverWait;

import java.io.File;
import java.io.IOException;
import java.time.Duration;
import java.util.List;
import java.util.Random;

/**
 * This class will open wikipedia.org, and will subsequently navigate 10 links.
 * When on the last page, it will take a screenshot and save it at the root of the project under the name selenium-snapshot.png.
 */
public class App
{
    private static final int PAGES_TO_VISIT = 10;

    public static void main(String[] args) {
        // Setup
        WebDriver driver = new FirefoxDriver();
        WebDriverWait wait = new WebDriverWait(driver, Duration.ofSeconds(10));

        try {
            driver.get("https://www.wikipedia.org");

            for (int i=1; i<=PAGES_TO_VISIT; i++) {
                System.out.println("Page n°" + i + " : " + driver.getTitle());

                // Get all clickable elements by XPath
                List<WebElement> links = driver.findElements(By.xpath("//a[@href]"));

                // Find a random, visible element
                WebElement randomLink = null;
                do {
                    randomLink = links.get(new Random().nextInt(links.size()));
                }
                while (!randomLink.isDisplayed());

                // Click on the chosen element
                randomLink.click();
            }

            // Take a screenshot of page
            File scrFile = ((TakesScreenshot) driver).getScreenshotAs(OutputType.FILE);
            FileUtils.copyFile(scrFile, new File("selenium-snapshot.png"));
        } catch (IOException e) {
            throw new RuntimeException(e);
        } finally {
            driver.quit();
        }
    }
}
```

![selenium-snapshot](https://github.com/nltp-buck/VV-ESIR-TP5/assets/152283100/ef83e28f-29b8-47da-8c20-c569bc854e67)
