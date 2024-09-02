В Spring Framework существует множество аннотаций, которые упрощают разработку и конфигурацию приложений. Вот основные аннотации, которые часто используются:

### 1. **Аннотации для конфигурации и управления компонентами**
Эти аннотации помогают определить и управлять компонентами (бинами) в Spring-контейнере.

- **@Configuration**: Указывает, что класс содержит определения бинов и может быть использован контейнером Spring для генерации бинов и управления ими.

  ```java
  @Configuration
  public class AppConfig {
      @Bean
      public MyService myService() {
          return new MyServiceImpl();
      }
  }
  ```

- **@Bean**: Используется в методах класса, аннотированного `@Configuration`, для создания и настройки бина, управляемого Spring-контейнером.

- **@Component**: Общая аннотация для определения класса как Spring-компонента. Spring автоматически обнаруживает такие классы и регистрирует их в контейнере.

- **@Service**: Специализированная версия `@Component`, указывает, что класс реализует некоторую бизнес-логику. Семантически аналогична `@Component`, но используется для более четкой семантики кода.

- **@Repository**: Специализированная версия `@Component`, используется для обозначения класса как DAO (Data Access Object) или репозитория данных. Также обрабатывает исключения, связанные с доступом к данным, путем их преобразования в `DataAccessException`.

- **@Controller**: Специализированная версия `@Component`, указывает, что класс является контроллером в Spring MVC.

- **@RestController**: Специализированная версия `@Controller` для создания RESTful веб-служб. Она объединяет `@Controller` и `@ResponseBody`, автоматически сериализуя объекты в JSON или XML.

### 2. **Аннотации для инъекции зависимостей**
Эти аннотации используются для автоматического внедрения зависимостей.

- **@Autowired**: Используется для автоматического внедрения зависимости по типу. Может быть применена к конструкторам, методам и полям.

  ```java
  @Service
  public class MyService {
      @Autowired
      private MyRepository myRepository;
  }
  ```

- **@Qualifier**: Используется вместе с `@Autowired`, чтобы указать конкретный бин, если существует несколько кандидатов для внедрения.

  ```java
  @Autowired
  @Qualifier("specificBean")
  private MyRepository myRepository;
  ```

- **@Value**: Позволяет внедрять значения из свойств (например, `application.properties`) или других внешних источников.

  ```java
  @Value("${app.name}")
  private String appName;
  ```

### 3. **Аннотации для конфигурации Spring MVC**
Эти аннотации используются в веб-приложениях для обработки HTTP-запросов.

- **@RequestMapping**: Используется для сопоставления HTTP-запросов с методами контроллера. Поддерживает все типы запросов (GET, POST, PUT, DELETE и т.д.).

  ```java
  @Controller
  public class MyController {
      @RequestMapping("/home")
      public String home() {
          return "home";
      }
  }
  ```

- **@GetMapping, @PostMapping, @PutMapping, @DeleteMapping, @PatchMapping**: Упрощенные версии `@RequestMapping`, используемые для обработки запросов конкретного типа (GET, POST и т.д.).

- **@PathVariable**: Используется для извлечения значений из URI-шаблона.

  ```java
  @GetMapping("/user/{id}")
  public String getUser(@PathVariable String id) {
      return "User ID: " + id;
  }
  ```

- **@RequestParam**: Используется для извлечения параметров запроса.

  ```java
  @GetMapping("/search")
  public String search(@RequestParam String query) {
      return "Search Query: " + query;
  }
  ```

- **@RequestBody**: Используется для привязки тела запроса к объекту метода контроллера.

  ```java
  @PostMapping("/users")
  public void addUser(@RequestBody User user) {
      // Save user
  }
  ```

- **@ResponseBody**: Указывает, что результат метода должен быть непосредственно записан в тело HTTP-ответа. Сериализует объект в формат JSON или XML.

- **@ExceptionHandler**: Используется для обработки исключений, возникающих в контроллерах.

  ```java
  @Controller
  public class MyController {
      @ExceptionHandler(Exception.class)
      public String handleException() {
          return "error";
      }
  }
  ```

### 4. **Аннотации для управления транзакциями**
Эти аннотации помогают управлять транзакциями в приложении.

- **@Transactional**: Используется для управления транзакциями на уровне метода или класса. Позволяет определять поведение транзакции, например, уровень изоляции, правила отката и т.д.

  ```java
  @Service
  public class MyService {
      @Transactional
      public void performTransaction() {
          // Code that requires a transaction
      }
  }
  ```

### 5. **Прочие полезные аннотации**

- **@EnableAutoConfiguration**: Автоматически конфигурирует приложение Spring на основе зависимостей, которые присутствуют в пути классов. Часто используется в приложениях Spring Boot.

- **@SpringBootApplication**: Содержит `@Configuration`, `@EnableAutoConfiguration`, и `@ComponentScan`, упрощая конфигурацию Spring Boot приложений.

- **@ComponentScan**: Сканирует указанные пакеты на наличие компонентов (бинов), которые должны быть зарегистрированы в контейнере Spring.

  ```java
  @Configuration
  @ComponentScan(basePackages = "com.example.myapp")
  public class AppConfig {
  }
  ```

- **@Scheduled**: Используется для задания расписания выполнения методов. Требует включения планировщика задач с помощью аннотации `@EnableScheduling`.

  ```java
  @Scheduled(cron = "0 0 * * * *")
  public void performTask() {
      // Code to run every hour
  }
  ```

Эти аннотации представляют собой ключевые инструменты в Spring Framework, которые позволяют создавать мощные, гибкие и масштабируемые приложения с минимальными усилиями.
