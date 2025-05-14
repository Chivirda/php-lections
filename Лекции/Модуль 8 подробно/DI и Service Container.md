
## 🔷 0:18–0:28 — **DI и Service Container (контейнер зависимостей)**

**Цель:**  
Научить понимать и применять **внедрение зависимостей (DI)** и **контейнер сервисов**, используя реальные PHP и Laravel-примеры.

---

### 🔹 Что такое Dependency Injection (DI)?

**Dependency Injection (внедрение зависимостей)** — это способ **передать в объект его зависимости снаружи**, а не создавать их внутри.

---

### ❌ Пример плохого кода (жёсткая связь):

```php
class ReportService {
    private $logger;

    public function __construct() {
        $this->logger = new FileLogger(); // ← здесь создаём зависимость сами
    }

    public function create() {
        $this->logger->log("Создан отчёт");
    }
}
```

- Такой код **жёстко привязан** к `FileLogger`.
- Его **трудно тестировать** и расширять (например, если захотим `DatabaseLogger`).

---

### ✅ Пример с внедрением через конструктор:

```php
interface LoggerInterface {
    public function log($message);
}

class FileLogger implements LoggerInterface {
    public function log($message) {
        echo "Log to file: $message";
    }
}

class ReportService {
    private $logger;

    public function __construct(LoggerInterface $logger) {
        $this->logger = $logger;
    }

    public function create() {
        $this->logger->log("Создан отчёт");
    }
}
```

Теперь можно **легко менять поведение**, не меняя `ReportService`.

---

### 🧠 Виды DI:

- **Constructor injection** (наиболее предпочтительный)
- Setter injection (через метод)
- Interface injection (редко используется)

---

## 🔸 Service Container (Контейнер сервисов) в Laravel

Laravel поставляется с **встроенным IoC-контейнером** (Inversion of Control Container), который:
- Автоматически **создаёт** и **внедряет зависимости**
- Умеет **разрешать интерфейсы** в реализации
- Позволяет **регистрировать singletons, factories** и т.д. 

---

### ✅ Пример Laravel:

```php
class ReportService {
    public function __construct(LoggerInterface $logger) {
        $this->logger = $logger;
    }
}
```

Если в `AppServiceProvider` указать:

```php
$this->app->bind(LoggerInterface::class, FileLogger::class);
```

То Laravel при вызове `app(ReportService::class)` **автоматически внедрит** `FileLogger`.

---

### 🔄 Аналогия: "Контейнер как фабрика на стероидах"

Представьте, что вы пришли в Laravel-контейнер и сказали:

🧑: "Дай мне `ReportService`, пожалуйста"  
🤖: "Окей, сначала найду `LoggerInterface`, он у меня связан с `FileLogger`. Создам `FileLogger`, передам его в `ReportService`. Готово!"

---

## 🔧 Интерактивное упражнение: **"Куда деть Logger?"**

**Условие:**  
Вы пишете сервис для генерации PDF-отчётов:

```php
class PdfReport {
    public function __construct() {
        $this->logger = new FileLogger();
    }

    public function generate() {
        $this->logger->log("PDF сгенерирован");
    }
}
```

**Вопрос студенту:** 
- Что не так?
- Как использовать интерфейс и DI?
- Как зарегистрировать это в Laravel-контейнере?

---

### ✅ Ответ:

- Вынести `FileLogger` из конструктора — использовать `LoggerInterface` 
- Передавать зависимость через `__construct(LoggerInterface $logger)` 
- Зарегистрировать в `AppServiceProvider`: 
```php
$this->app->bind(LoggerInterface::class, FileLogger::class);
```


---

### 💡 Вывод:

**DI + Контейнер →** мощный инструмент для:
- отказа от жёстких связей (low coupling)
- удобного тестирования (можно подменять зависимости)
- гибкой архитектуры

---

Вот интерактивное **практическое задание для студентов** по теме **Dependency Injection и Service Container**, в стиле «код-рыба» — они должны исправить и дописать код.

---

## 🧪 Практика: Внедрение зависимостей и контейнер Laravel

### 🎯 Цель:

Исправить код с жёсткими зависимостями и подключить его к **контейнеру зависимостей Laravel**, используя интерфейсы.

---

### 📂 Условие:

У вас есть класс, который сохраняет заказы в файл:

```php
class FileSaver {
    public function save($data) {
        // сохраняем в файл
        file_put_contents('order.txt', $data);
    }
}

class OrderService {
    private $saver;

    public function __construct() {
        $this->saver = new FileSaver(); // ❌ Прямая зависимость
    }

    public function createOrder($data) {
        $this->saver->save($data);
    }
}
```

### 📌 Задача:

1. Выделите **интерфейс `SaverInterface`** 
2. Реализуйте `FileSaver` как реализацию интерфейса 
3. Переделайте `OrderService`, чтобы он принимал зависимость через `__construct()` 
4. Зарегистрируйте зависимость в `AppServiceProvider`  
5. Вызовите `OrderService` через контейнер Laravel  

---

### ✅ Ожидаемый результат:

```php
// 1. Интерфейс
interface SaverInterface {
    public function save($data);
}

// 2. Реализация
class FileSaver implements SaverInterface {
    public function save($data) {
        file_put_contents('order.txt', $data);
    }
}

// 3. Внедрение зависимости
class OrderService {
    private $saver;

    public function __construct(SaverInterface $saver) {
        $this->saver = $saver;
    }

    public function createOrder($data) {
        $this->saver->save($data);
    }
}
```

---

### ⚙️ 4. Регистрация в `AppServiceProvider`

```php
// app/Providers/AppServiceProvider.php

public function register()
{
    $this->app->bind(SaverInterface::class, FileSaver::class);
}
```

---

### 🧪 5. Использование в Laravel-контроллере

```php
public function store(OrderService $orderService)
{
    $orderService->createOrder('Order: #1234');
    return 'OK';
}
```

---

### 💬 Доп. вопросы к практике:

- Как бы вы протестировали `OrderService`, не записывая в файл?
- Что произойдёт, если добавить `DatabaseSaver`? Нужно ли менять `OrderService`?

---
