Паттерны автоматизации тестирования помогают организовать тесты таким образом, чтобы они были поддерживаемыми, масштабируемыми и понятными. Использование этих паттернов снижает вероятность технического долга и упрощает разработку и поддержку автоматизированных тестов. Рассмотрим основные паттерны автоматизации.

---

### **1. Page Object Model (POM)**

**Суть:**  
Вынос логики взаимодействия с веб-страницей в отдельные классы, называемые "объектами страниц" (Page Objects). Каждый класс представляет одну веб-страницу или компонент страницы.  

**Преимущества:**  
- Повторное использование кода.
- Упрощение тестов за счет отделения логики взаимодействия с UI от сценариев.
- Легкость сопровождения: при изменении UI требуется обновить только Page Object.

**Пример:**
```java
public class LoginPage {
    private WebDriver driver;

    public LoginPage(WebDriver driver) {
        this.driver = driver;
    }

    private By usernameField = By.id("username");
    private By passwordField = By.id("password");
    private By loginButton = By.id("loginButton");

    public void login(String username, String password) {
        driver.findElement(usernameField).sendKeys(username);
        driver.findElement(passwordField).sendKeys(password);
        driver.findElement(loginButton).click();
    }
}
```

**Тест:**
```java
public class LoginTest {
    @Test
    void loginTest() {
        WebDriver driver = new ChromeDriver();
        LoginPage loginPage = new LoginPage(driver);

        driver.get("https://example.com/login");
        loginPage.login("user", "password");
        
        // Assertions
    }
}
```

---

### **2. Screenplay Pattern**

**Суть:**  
Более продвинутый подход к автоматизации, основанный на разделении ответственности между актерами (Actors), задачами (Tasks) и вопросами (Questions). Используется в BDD и более сложных сценариях.

**Преимущества:**  
- Читаемость сценариев.
- Масштабируемость для больших проектов.
- Упрощение работы с разными уровнями взаимодействия (UI, API, базы данных).

**Пример:**
- Актор выполняет задачу:
  ```java
  Actor user = Actor.named("User");
  user.attemptsTo(
      Login.withCredentials("username", "password")
  );
  ```
- Задача:
  ```java
  public class Login implements Task {
      private final String username;
      private final String password;

      public static Login withCredentials(String username, String password) {
          return new Login(username, password);
      }

      @Override
      public <T extends Actor> void performAs(T actor) {
          // Логика для выполнения логина
      }
  }
  ```

---

### **3. Data-Driven Testing**

**Суть:**  
Тестовые данные выносятся в отдельные файлы (CSV, Excel, JSON, XML) или хранятся в базе данных. Тесты повторяются с разными наборами данных.

**Преимущества:**  
- Возможность тестирования с множеством входных данных.
- Легкость изменения данных без изменения кода теста.

**Пример:**  
Использование JUnit с параметрами:
```java
@ParameterizedTest
@CsvSource({
    "user1, password1",
    "user2, password2"
})
void loginTest(String username, String password) {
    LoginPage loginPage = new LoginPage(driver);
    loginPage.login(username, password);
    // Assertions
}
```

---

### **4. Keyword-Driven Testing**

**Суть:**  
Логика тестов представлена в виде ключевых слов, которые описывают действия, а сами ключевые слова реализованы в виде функций или методов.

**Преимущества:**  
- Нечитаемым для бизнеса тестам добавляется смысл.
- Облегчение участия нетехнических специалистов.

**Пример:**
- Файл со сценариями:
  ```
  Open URL    https://example.com
  Enter Text  usernameField    admin
  Enter Text  passwordField    admin123
  Click       loginButton
  ```
- Реализация:
  ```java
  public void executeKeyword(String action, String target, String value) {
      switch (action) {
          case "Open URL":
              driver.get(value);
              break;
          case "Enter Text":
              driver.findElement(By.id(target)).sendKeys(value);
              break;
          case "Click":
              driver.findElement(By.id(target)).click();
              break;
      }
  }
  ```

---

### **5. Behavior-Driven Development (BDD)**

**Суть:**  
Написание тестов в формате "человекопонятного языка", например, Gherkin, который описывает поведение системы.

**Преимущества:**  
- Совместная работа тестировщиков, разработчиков и бизнес-аналитиков.
- Читаемость сценариев даже для нетехнических специалистов.

**Пример на Gherkin:**
```gherkin
Feature: Login feature

  Scenario: Successful login
    Given I am on the login page
    When I enter valid credentials
    Then I should see the dashboard
```

**Реализация на Java с Cucumber:**
```java
@Given("I am on the login page")
public void openLoginPage() {
    driver.get("https://example.com/login");
}

@When("I enter valid credentials")
public void enterCredentials() {
    driver.findElement(By.id("username")).sendKeys("user");
    driver.findElement(By.id("password")).sendKeys("password");
    driver.findElement(By.id("loginButton")).click();
}

@Then("I should see the dashboard")
public void verifyDashboard() {
    Assert.assertTrue(driver.findElement(By.id("dashboard")).isDisplayed());
}
```

---

### **6. Hybrid Framework**

**Суть:**  
Сочетание различных паттернов, таких как Page Object Model, Data-Driven Testing и BDD. Используется в крупных проектах для обеспечения гибкости и масштабируемости.

**Преимущества:**  
- Возможность комбинировать лучшие практики.
- Подходит для сложных систем с разнообразными требованиями.

---

### **Заключение**
Выбор паттерна автоматизации зависит от размера проекта, его сложности и требований команды.  
- **POM** отлично подходит для UI-тестов.  
- **Data-Driven** упрощает тестирование с большими объемами данных.  
- **BDD** улучшает коммуникацию между командами.  
- **Screenplay** идеально для сложных сценариев с различными типами взаимодействий.  

Использование правильного паттерна сделает тесты проще в разработке, читабельнее и менее затратными в сопровождении.
