**JDBC (Java Database Connectivity)** — это стандартный API в Java для взаимодействия с реляционными базами данных. В JDBC существует несколько ключевых классов и интерфейсов, которые обеспечивают соединение с базой данных, выполнение SQL-запросов и управление результатами.

### Основные классы и интерфейсы JDBC:

1. **`DriverManager`**:
    - Это класс, который управляет набором драйверов баз данных.
    - Основная задача — установление соединения с базой данных.
    - Пример:
      ```java
      Connection connection = DriverManager.getConnection(url, username, password);
      ```

2. **`Connection`**:
    - Интерфейс, представляющий соединение с базой данных.
    - Используется для выполнения SQL-запросов, а также для управления транзакциями.
    - Основные методы:
        - `createStatement()`: создаёт объект `Statement` для выполнения SQL-запросов.
        - `prepareStatement(String sql)`: создаёт объект `PreparedStatement` для предварительно скомпилированных SQL-запросов.
        - `commit()`, `rollback()`: для управления транзакциями.
    - Пример:
      ```java
      Connection connection = DriverManager.getConnection(url, username, password);
      ```

3. **`Statement`**:
    - Интерфейс для выполнения простых SQL-запросов (без параметров).
    - Используется для выполнения `SELECT`, `INSERT`, `UPDATE` и `DELETE` операций.
    - Основные методы:
        - `executeQuery(String sql)`: выполняет запросы типа SELECT.
        - `executeUpdate(String sql)`: выполняет запросы типа INSERT, UPDATE или DELETE.
    - Пример:
      ```java
      Statement statement = connection.createStatement();
      ResultSet resultSet = statement.executeQuery("SELECT * FROM users");
      ```

4. **`PreparedStatement`**:
    - Интерфейс для выполнения SQL-запросов с параметрами.
    - Позволяет защищаться от SQL-инъекций и повторно использовать запросы с разными параметрами.
    - Пример:
      ```java
      PreparedStatement pstmt = connection.prepareStatement("SELECT * FROM users WHERE id = ?");
      pstmt.setInt(1, 10);
      ResultSet rs = pstmt.executeQuery();
      ```

5. **`ResultSet`**:
    - Интерфейс, представляющий результат выполнения запроса `SELECT`.
    - Используется для чтения строк результата.
    - Основные методы:
        - `next()`: перемещает указатель к следующей строке результата.
        - `getString()`, `getInt()`, `getDate()` и другие методы для получения данных из текущей строки.
    - Пример:
      ```java
      ResultSet resultSet = statement.executeQuery("SELECT * FROM users");
      while (resultSet.next()) {
          String name = resultSet.getString("name");
          int age = resultSet.getInt("age");
          System.out.println("Name: " + name + ", Age: " + age);
      }
      ```

6. **`CallableStatement`**:
    - Интерфейс для вызова хранимых процедур в базе данных.
    - Позволяет передавать входные параметры и получать выходные параметры.
    - Пример:
      ```java
      CallableStatement cstmt = connection.prepareCall("{call getUserById(?)}");
      cstmt.setInt(1, 10);
      ResultSet rs = cstmt.executeQuery();
      ```

7. **`SQLException`**:
    - Это исключение, которое выбрасывается в случае ошибки при работе с базой данных.
    - Используется для обработки ошибок JDBC.
    - Пример:
      ```java
      try {
          Connection connection = DriverManager.getConnection(url, username, password);
      } catch (SQLException e) {
          e.printStackTrace();
      }
      ```

### Общий процесс работы с JDBC:
1. Загрузка драйвера базы данных.
2. Создание соединения с базой данных (`Connection`).
3. Создание объекта для выполнения SQL-запросов (`Statement`, `PreparedStatement`, `CallableStatement`).
4. Выполнение SQL-запроса.
5. Обработка результатов (`ResultSet`).
6. Закрытие ресурсов (`Connection`, `Statement`, `ResultSet`).

### Пример использования JDBC:
```java
import java.sql.*;

public class JdbcExample {
    public static void main(String[] args) {
        String url = "jdbc:postgresql://localhost:5432/mydb";
        String username = "user";
        String password = "password";

        try (Connection connection = DriverManager.getConnection(url, username, password)) {
            // Создаем запрос
            Statement statement = connection.createStatement();
            ResultSet resultSet = statement.executeQuery("SELECT * FROM users");

            // Обрабатываем результат
            while (resultSet.next()) {
                String name = resultSet.getString("name");
                int age = resultSet.getInt("age");
                System.out.println("Name: " + name + ", Age: " + age);
            }

        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
}
```

Этот пример показывает базовые шаги работы с JDBC: создание соединения, выполнение запроса и обработка результатов.