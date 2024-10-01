### Что такое JUnit?

**JUnit** — это один из самых популярных фреймворков для модульного тестирования Java-приложений. Он позволяет разработчикам писать и запускать тесты, проверяя корректность кода. JUnit помогает автоматизировать тестирование и обнаруживать ошибки в коде на ранних стадиях разработки.

Основные возможности JUnit:
1. Написание модульных тестов.
2. Упрощенное создание и выполнение тестов.
3. Поддержка аннотаций для задания поведения тестов.
4. Проведение ассертов (assert) для проверки ожидаемых результатов.
5. Возможность группировки тестов с помощью тестовых наборов (test suites).

---

### Основные аннотации в JUnit

1. **@Test** — маркирует метод как тестовый.
2. **@BeforeEach** — выполняет метод перед каждым тестом.
3. **@AfterEach** — выполняет метод после каждого теста.
4. **@BeforeAll** — выполняет метод один раз перед запуском всех тестов.
5. **@AfterAll** — выполняет метод один раз после завершения всех тестов.
6. **@Disabled** — отключает тест или набор тестов.
7. **@Tag** — используется для группировки тестов.

---

### Пример простого теста с JUnit

#### Тестируемый класс

Предположим, у нас есть класс `Calculator`, который содержит методы для базовых арифметических операций.

```java
public class Calculator {
    public int add(int a, int b) {
        return a + b;
    }

    public int subtract(int a, int b) {
        return a - b;
    }

    public int multiply(int a, int b) {
        return a * b;
    }

    public int divide(int a, int b) {
        if (b == 0) {
            throw new IllegalArgumentException("Division by zero");
        }
        return a / b;
    }
}
```

#### Тестирование класса `Calculator`

Для тестирования класса `Calculator`, мы можем создать тестовый класс с использованием JUnit.

```java
import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.*;

public class CalculatorTest {

    // Тест метода сложения
    @Test
    public void testAdd() {
        Calculator calculator = new Calculator();
        int result = calculator.add(3, 4);
        assertEquals(7, result);  // Проверяем, что результат равен 7
    }

    // Тест метода вычитания
    @Test
    public void testSubtract() {
        Calculator calculator = new Calculator();
        int result = calculator.subtract(10, 5);
        assertEquals(5, result);  // Проверяем, что результат равен 5
    }

    // Тест метода умножения
    @Test
    public void testMultiply() {
        Calculator calculator = new Calculator();
        int result = calculator.multiply(2, 3);
        assertEquals(6, result);  // Проверяем, что результат равен 6
    }

    // Тест метода деления
    @Test
    public void testDivide() {
        Calculator calculator = new Calculator();
        int result = calculator.divide(10, 2);
        assertEquals(5, result);  // Проверяем, что результат равен 5
    }

    // Тест на деление на ноль (ожидаем исключение)
    @Test
    public void testDivideByZero() {
        Calculator calculator = new Calculator();
        assertThrows(IllegalArgumentException.class, () -> {
            calculator.divide(10, 0);
        });
    }
}
```

---

### Подробности работы с JUnit

#### 1. **Ассерты (Assertions)**

Ассерты используются для проверки того, что результат выполнения теста соответствует ожиданиям. В JUnit есть несколько основных методов для проверок:

- **assertEquals(expected, actual)** — проверяет, что два значения равны.
- **assertTrue(condition)** — проверяет, что условие истинно.
- **assertFalse(condition)** — проверяет, что условие ложно.
- **assertNull(object)** — проверяет, что объект равен `null`.
- **assertNotNull(object)** — проверяет, что объект не равен `null`.
- **assertThrows(expectedType, executable)** — проверяет, что метод выбрасывает ожидаемое исключение.

#### Примеры ассертов:

```java
@Test
public void testAssertions() {
    String expected = "Hello";
    String actual = "Hello";

    assertEquals(expected, actual);  // Успешная проверка

    int value = 5;
    assertTrue(value > 0);  // Проверка на истину
    assertFalse(value < 0);  // Проверка на ложь

    Object obj = null;
    assertNull(obj);  // Проверка на null
}
```

---

### Тестирование с использованием **@BeforeEach** и **@AfterEach**

Эти аннотации используются для выполнения кода перед и после каждого теста. Это полезно для подготовки тестовой среды.

```java
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.AfterEach;

public class LifecycleTest {

    private Calculator calculator;

    @BeforeEach
    public void setUp() {
        calculator = new Calculator();
        System.out.println("Тест начинается");
    }

    @AfterEach
    public void tearDown() {
        System.out.println("Тест завершен");
    }

    @Test
    public void testAdd() {
        int result = calculator.add(1, 2);
        assertEquals(3, result);
    }

    @Test
    public void testSubtract() {
        int result = calculator.subtract(5, 3);
        assertEquals(2, result);
    }
}
```

Этот пример покажет, что метод `setUp` выполняется перед каждым тестом, а метод `tearDown` — после.

---

### Тестовые наборы (Test Suites)

JUnit позволяет группировать тесты в тестовые наборы, чтобы запускать их все вместе.

Пример тестового набора:

```java
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.Nested;

public class CalculatorTestSuite {

    @Nested
    class AddTests {
        @Test
        public void testAddPositiveNumbers() {
            Calculator calculator = new Calculator();
            assertEquals(5, calculator.add(2, 3));
        }

        @Test
        public void testAddNegativeNumbers() {
            Calculator calculator = new Calculator();
            assertEquals(-5, calculator.add(-2, -3));
        }
    }

    @Nested
    class SubtractTests {
        @Test
        public void testSubtractPositiveNumbers() {
            Calculator calculator = new Calculator();
            assertEquals(1, calculator.subtract(5, 4));
        }
    }
}
```

Этот пример показывает, как организовать несколько тестов в логические группы с помощью аннотации `@Nested`.

---

### Заключение

JUnit — это мощный инструмент для написания модульных тестов в Java. Его возможности, такие как аннотации для тестирования, использование ассертов для проверки результатов, и поддержка жизненного цикла тестов делают процесс тестирования кода простым и эффективным.

JUnit особенно полезен при написании юнит-тестов и интеграционных тестов, что позволяет выявлять ошибки на ранних этапах разработки и улучшать качество программного обеспечения.