---
title: Browser Automation
nav: docs
renderer: Cucumber::Website::Reference
---

# Browser Automation

Cucumber is not a Browser Automation tool, but it works well with Browser
Automation tools such as:

* [Selenium Webdriver](http://docs.seleniumhq.org/projects/webdriver/)
* [Capybara](https://jnicklas.github.io/capybara/)
* [Watir](http://watir.com/)

## Selenium WebDriver

Let's convert the [Selenium-Webdriver by Example tutorial](http://docs.seleniumhq.org/docs/03_webdriver.jsp#introducing-the-selenium-webdriver-api-by-example) to use Cucumber.

We can express the example as the following Scenario:

```gherkin
Scenario: Finding some cheese
  Given I am on the Google search page
  When I search for "Cheese!"
  Then the page title should start with "cheese"
```

Here are the accompanying Step Definitions:

[carousel]

```ruby
# TODO! See the Java example for now.
```

```java
package com.example;

public class ExampleSteps {
    private final WebDriver driver = new FirefoxDriver();

    @Given("^I am on the Google search page$")
    public void I_visit_google() {
        driver.get("https://www.google.com");
    }

    @When("^I search for \"(.*)\"$")
    public void search_for(String query) {
        WebElement element = browser.findElement(By.name("q"));
        // Enter something to search for
        element.sendKeys(query);
        // Now submit the form. WebDriver will find the form for us from the element
        element.submit();
    }

    @Then("^the page title should start with \"(.*)\"$")
    public void checkTitle() {
        // Google's search is rendered dynamically with JavaScript.
        // Wait for the page to load, timeout after 10 seconds
        (new WebDriverWait(driver, 10)).until(new ExpectedCondition<Boolean>() {
          public Boolean apply(WebDriver d) {
          return d.getTitle().toLowerCase().startsWith("cheese");
          }
        });
        assertThat(driver.getTitle(), startsWith("cheese"));
        // Should see: "cheese! - Google Search"
    }

    @After()
    public void closeBrowser() {
        driver.quit();
    }
}
```

[/carousel]

## Watir

TODO

## Tips and Tricks

### Multiple Browsers

Cucumber can run your scenarios with different browsers. Simply select the browser
to use based on a configuration property that is loaded at runtime:

[carousel]

```ruby
Capybara.register_driver :selenium do |app|
  browser = (ENV['browser'] || 'firefox').to_sym
  Capybara::Selenium::Driver.new(app, :browser => browser)
end
```

```java
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.firefox.FirefoxDriver;
import org.openqa.selenium.chrome.ChromeDriver;

public class WebDriverFactory {
    public static WebDriver createWebDriver() {
        String webdriver = System.getProperty("browser", "firefox");
        switch(webdriver) {
            case "firefox":
                return new FirefoxDriver();
            case "chrome":
                return new ChromeDriver();
            default:
                throw new RuntimeException("Unsupported webdriver: " + webdriver);
        }
    }
}
```
[/carousel]

Then, simply define the browser property when you run Cucumber:

[carousel]

```
browser=chrome cucumber
```

```
mvn test -Dbrowser=chrome
```

[/carousel]

### Re-using the browser window

Closing and re-opening the browser window between your scenarios will slow them down.

To re-use them you can use the [SharedDriver](https://github.com/cucumber/cucumber-jvm/blob/master/examples/java-webbit-websockets-selenium/src/test/java/cucumber/examples/java/websockets/SharedDriver.java) wrapper rather than calling WebDriver directly.

### Embedding Screenshots

See the [Report Attachments / Screenshots](/docs/reference#screenshots).

### Example Projects

* [java-webbit-websockets-selenium](https://github.com/cucumber/cucumber-jvm/tree/master/examples/java-webbit-websockets-selenium)
