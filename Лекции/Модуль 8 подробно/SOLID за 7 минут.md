
## 🔹 0:00–0:08 — SOLID за 7 минут

**Цель:** кратко, но наглядно объяснить каждый из 5 принципов SOLID с простыми PHP-примерами.

---

### 🧩 1. **S — Single Responsibility Principle (Принцип единственной ответственности)**

Каждый класс должен иметь **только одну причину для изменения**.

**Плохо:**

```php
class Report {
    public function generate() { /* генерирует отчёт */ }
    public function saveToFile() { /* сохраняет в файл */ }
}
```

**Хорошо:**

```php
class ReportGenerator {
    public function generate() { ... }
}

class ReportSaver {
    public function saveToFile(Report $report) { ... }
}
```

**Обсуждение:**  
`Report` занимается и логикой, и сохранением — это две ответственности. Разделяем по назначению.

---

### 🧩 2. **O — Open/Closed Principle (Открыт для расширения, закрыт для изменения)**

Поведение объекта можно **расширить**, не меняя его исходный код.

**Плохо:**

```php
class Payment {
    public function pay($method) {
        if ($method === 'paypal') { ... }
        elseif ($method === 'card') { ... }
    }
}
```

**Хорошо:**

```php
interface PaymentMethod {
    public function pay();
}

class PaypalPayment implements PaymentMethod {
    public function pay() { ... }
}

class CardPayment implements PaymentMethod {
    public function pay() { ... }
}

class Payment {
    public function process(PaymentMethod $method) {
        $method->pay();
    }
}
```

**Обсуждение:**  
Теперь можно легко добавить `ApplePayPayment` без изменения класса `Payment`.

---

### 🧩 3. **L — Liskov Substitution Principle (Принцип подстановки Барбары Лисков)**

Классы-наследники должны **заменять** базовые классы без ошибок.

**Плохо:**

```php
class Bird {
    public function fly() { ... }
}

class Penguin extends Bird {
    public function fly() {
        throw new Exception("I can't fly!");
    }
}
```

**Хорошо:**

```php
interface Bird {}
interface FlyingBird extends Bird {
    public function fly();
}

class Sparrow implements FlyingBird {
    public function fly() { ... }
}

class Penguin implements Bird {
    // не умеет летать — и это нормально
}
```

**Обсуждение:**  
`Penguin` — не `FlyingBird`. Интерфейс помог разделить поведение корректно.

---

### 🧩 4. **I — Interface Segregation Principle (Принцип разделения интерфейса)**

Не заставляй класс **реализовывать ненужные методы**.

**Плохо:**

```php
interface Worker {
    public function work();
    public function manageTeam();
}

class Developer implements Worker {
    public function work() { ... }
    public function manageTeam() {
        throw new Exception("I don't manage anyone!");
    }
}
```

**Хорошо:**

```php
interface Workable {
    public function work();
}

interface Manageable {
    public function manageTeam();
}

class Developer implements Workable {
    public function work() { ... }
}
```

**Обсуждение:**  
Разделённый интерфейс делает классы чище и понятнее.

---

### 🧩 5. **D — Dependency Inversion Principle (Принцип инверсии зависимостей)**

Зависеть следует от **абстракций**, а не от конкретных классов.

**Плохо:**

```php
class Notification {
    private $emailSender;

    public function __construct() {
        $this->emailSender = new EmailSender(); // жёсткая привязка
    }

    public function send($message) {
        $this->emailSender->send($message);
    }
}
```

**Хорошо:**

```php
interface MessageSender {
    public function send($message);
}

class EmailSender implements MessageSender {
    public function send($message) { ... }
}

class Notification {
    private $sender;

    public function __construct(MessageSender $sender) {
        $this->sender = $sender;
    }

    public function send($message) {
        $this->sender->send($message);
    }
}

$emailSender = new EmailSender();
$notification = new Notification($emailSender);
$notification->send('Welcome');
```

**Обсуждение:**  
Теперь можно внедрить `SmsSender`, `PushSender`, и не менять код `Notification`.

---

### ✅ Заключение

SOLID — основа гибкой и поддерживаемой архитектуры. Эти принципы помогают:

- избегать хаоса в коде;
- упрощать тестирование;
- повышать повторное использование кода.

---

Вот интерактивные **задания по всем пяти принципам SOLID**. Формат: краткое условие + ожидаемый ответ. Подходят для обсуждения, домашних заданий или мини-теста на паре.

---

## 🧩 **S — Single Responsibility Principle**

### 🧪 Задание:

Вот класс, который пишет лог ошибок в файл:

```php
class ErrorLogger {
    public function log($message) {
        $this->saveToDatabase($message);
        $this->sendEmail($message);
    }

    private function saveToDatabase($msg) { ... }
    private function sendEmail($msg) { ... }
}
```

**Вопрос:**  
Что не так и как это исправить?

---

### ✅ Ожидаемый ответ:

- Нарушено SRP: один класс отвечает **и за сохранение, и за уведомление**.
- Нужно разделить обязанности:

```php
class DatabaseLogger {
    public function log($message) { ... }
}

class EmailNotifier {
    public function notify($message) { ... }
}
```

---

## 🧩 **O — Open/Closed Principle**

### 🧪 Задание:

Класс для отображения товаров:

```php
class ProductRenderer {
    public function render(Product $product, $type) {
        if ($type === 'json') {
            return json_encode($product->toArray());
        } elseif ($type === 'html') {
            return "<p>{$product->name}</p>";
        }
    }
}
```

**Вопрос:**  
Как этот код можно расширить без изменения?

---

### ✅ Ожидаемый ответ:

- Нарушено OCP: при добавлении нового типа (например, XML) — **нужно менять код**.
- Нужно использовать абстракцию:

```php
interface ProductPresenter {
    public function render(Product $product);
}

class JsonPresenter implements ProductPresenter {
    public function render(Product $product) {
        return json_encode($product->toArray());
    }
}

class HtmlPresenter implements ProductPresenter {
    public function render(Product $product) {
        return "<p>{$product->name}</p>";
    }
}
```

---

## 🧩 **L — Liskov Substitution Principle**

### 🧪 Задание:

Есть абстрактный класс:

```php
class File {
    public function open() { ... }
}

class ReadOnlyFile extends File {
    public function open() {
        throw new Exception("Can't open for writing!");
    }
}
```

**Вопрос:**  
Нарушает ли `ReadOnlyFile` принцип L и почему?

---

### ✅ Ожидаемый ответ:

- Да, нарушает.
- Подтип `ReadOnlyFile` **не может использоваться вместо `File`** — поведение меняется.
- Нужно разделить интерфейсы или использовать композицию.

---

## 🧩 **I — Interface Segregation Principle**

### 🧪 Задание:

Интерфейс сотрудника:

```php
interface Employee {
    public function work();
    public function attendMeeting();
    public function manageProject();
}
```

**Вопрос:**  
Почему этот интерфейс нарушает ISP и как это исправить?

---

### ✅ Ожидаемый ответ:

- Интерфейс **слишком жирный** — не все работники управляют проектами.
- Разделить:

```php
interface Workable {
    public function work();
}

interface MeetingAttendable {
    public function attendMeeting();
}

interface ProjectManageable {
    public function manageProject();
}
```

---

## 🧩 **D — Dependency Inversion Principle**


---

## 🧪 Задание: «Куда вставить зависимость?»

**Условие:**

У вас есть класс, отвечающий за оплату заказа:

```php
class Checkout {
    public function pay($amount) {
        $gateway = new PaypalGateway();
        $gateway->charge($amount);
    }
}
```

А вот интерфейс и реализация:

```php
interface PaymentGateway {
    public function charge($amount);
}

class PaypalGateway implements PaymentGateway {
    public function charge($amount) {
        echo "Paid $amount via PayPal";
    }
}
```

**Вопрос:**

1. Почему этот код нарушает принцип D?
2. Как его переписать правильно?
3. Как вы протестируете `Checkout` без реального `PaypalGateway`?

---

## ✅ Ожидаемый ответ студента:

### 1. Нарушение:

- Класс `Checkout` **жёстко зависит от конкретной реализации** `PaypalGateway`.
- Он **создаёт зависимость сам**, вместо того чтобы получить её снаружи.

---

### 2. Решение:

**Правильно:**

```php
class Checkout {
    private $gateway;

    public function __construct(PaymentGateway $gateway) {
        $this->gateway = $gateway;
    }

    public function pay($amount) {
        $this->gateway->charge($amount);
    }
}
```

**Использование:**

```php
$checkout = new Checkout(new PaypalGateway());
$checkout->pay(100);
```

---

### 3. Тестирование (пример через фейковую реализацию):

```php
class FakeGateway implements PaymentGateway {
    public $chargedAmount = 0;

    public function charge($amount) {
        $this->chargedAmount = $amount;
    }
}

$fake = new FakeGateway();
$checkout = new Checkout($fake);
$checkout->pay(50);

assert($fake->chargedAmount === 50);
```

---

## 🧠 Дополнительные мини-вопросы (для устной проверки или теста):

- Почему не стоит создавать зависимости внутри класса?
- Чем интерфейс полезнее конкретной реализации?
- Как Laravel помогает автоматически внедрять зависимости?

---

