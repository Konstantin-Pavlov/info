### Что такое **Mockito**?

**Mockito** — это популярный фреймворк для создания mock-объектов в Java, который упрощает тестирование кода. Mock-объекты имитируют поведение реальных объектов и используются в тестах для изоляции классов от внешних зависимостей, таких как базы данных, веб-сервисы и другие компоненты.

Основные возможности Mockito:
1. Создание mock-объектов.
2. Определение поведения mock-объектов.
3. Проверка вызовов методов mock-объектов.
4. Захват аргументов методов с помощью `ArgumentCaptor`.

---

### Основные концепции Mockito

1. **Mock** — имитация реальных объектов, которая может вернуть определенные значения или сгенерировать исключения, не выполняя настоящие операции.
2. **Stubbing** — процесс настройки поведения mock-объекта.
3. **Verification** — проверка того, что определенный метод был вызван с конкретными параметрами.

---

### Пример использования Mockito

#### 1. Создание mock-объекта
Для создания mock-объекта используется метод `Mockito.mock()`.

```java
import static org.mockito.Mockito.*;
import org.junit.jupiter.api.Test;

public class UserServiceTest {

    @Test
    public void testUserService() {
        // Создание mock-объекта для UserRepository
        UserRepository userRepository = mock(UserRepository.class);
        
        // Определяем, как mock-объект должен себя вести
        when(userRepository.findByEmail("test@example.com")).thenReturn(new User("test@example.com", "Test User"));

        // Используем mock-объект в сервисе
        UserService userService = new UserService(userRepository);
        User user = userService.getUserByEmail("test@example.com");

        // Проверяем результат
        assertEquals("Test User", user.getName());
    }
}
```

#### 2. Настройка поведения mock-объекта (stubbing)
Stubbing — это настройка того, что должно произойти при вызове определенных методов mock-объекта.

```java
// Возвращаем заданный объект User при вызове findByEmail
when(userRepository.findByEmail("test@example.com")).thenReturn(new User("test@example.com", "Test User"));

// Можно настроить, чтобы метод выбросил исключение
when(userRepository.findByEmail("invalid@example.com")).thenThrow(new IllegalArgumentException("Invalid email"));
```

#### 3. Проверка вызовов методов (verification)
После выполнения теста можно проверить, что определенный метод был вызван на mock-объекте.

```java
// Проверяем, что метод findByEmail был вызван один раз с параметром "test@example.com"
verify(userRepository, times(1)).findByEmail("test@example.com");

// Проверяем, что метод не вызывался с другим email
verify(userRepository, never()).findByEmail("invalid@example.com");
```

---

### Пример более сложного теста с Mockito

#### Контроллер и его зависимость
Предположим, у нас есть контроллер, который использует сервис для получения данных о пользователе:

```java
public class UserController {

    private UserService userService;

    public UserController(UserService userService) {
        this.userService = userService;
    }

    public User getUserByEmail(String email) {
        return userService.getUserByEmail(email);
    }
}
```

#### Тестирование контроллера с помощью Mockito

```java
import static org.mockito.Mockito.*;
import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.*;

public class UserControllerTest {

    @Test
    public void testGetUserByEmail() {
        // Мокаем UserService
        UserService userService = mock(UserService.class);

        // Настраиваем, что mock должен вернуть при вызове метода getUserByEmail
        when(userService.getUserByEmail("test@example.com"))
                .thenReturn(new User("test@example.com", "Test User"));

        // Создаем контроллер, передавая mock в конструктор
        UserController userController = new UserController(userService);

        // Выполняем тест
        User user = userController.getUserByEmail("test@example.com");

        // Проверяем результат
        assertEquals("Test User", user.getName());

        // Проверяем, что метод getUserByEmail был вызван один раз с указанным параметром
        verify(userService, times(1)).getUserByEmail("test@example.com");
    }
}
```

---

### Захват аргументов с помощью `ArgumentCaptor`

Иногда необходимо проверить не только вызов метода, но и аргументы, с которыми он был вызван. Для этого используется `ArgumentCaptor`.

#### Пример использования `ArgumentCaptor`:

```java
import static org.mockito.Mockito.*;
import static org.junit.jupiter.api.Assertions.*;
import org.mockito.ArgumentCaptor;
import org.junit.jupiter.api.Test;

public class UserServiceTest {

    @Test
    public void testSaveUser() {
        // Мокаем UserRepository
        UserRepository userRepository = mock(UserRepository.class);
        UserService userService = new UserService(userRepository);

        // Захват аргумента при вызове метода
        ArgumentCaptor<User> userCaptor = ArgumentCaptor.forClass(User.class);

        // Тестируем метод, который сохраняет пользователя
        userService.saveUser(new User("test@example.com", "Test User"));

        // Проверяем, что метод save был вызван один раз
        verify(userRepository, times(1)).save(userCaptor.capture());

        // Получаем захваченный объект и проверяем его значения
        User savedUser = userCaptor.getValue();
        assertEquals("test@example.com", savedUser.getEmail());
        assertEquals("Test User", savedUser.getName());
    }
}
```

---

### Заключение

- **Mockito** позволяет изолировать классы от их зависимостей, подменяя реальные объекты их mock-объектами.
- **Stubbing** используется для настройки поведения mock-объектов, чтобы они возвращали ожидаемые результаты.
- **Verification** позволяет проверять, были ли вызваны определенные методы и с какими параметрами.
- С помощью **ArgumentCaptor** можно захватывать и проверять аргументы методов во время тестирования.

Использование Mockito упрощает написание модульных тестов, делая их более читабельными и управляемыми, что особенно полезно в проекте с множеством зависимостей.