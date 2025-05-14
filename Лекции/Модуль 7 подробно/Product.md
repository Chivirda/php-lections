
## 🔹 2-й академический час — 0:30–0:40

### **Живой пример: класс `Product`, статический НДС, метод `getPriceWithVat()`. Подключаем через autoload**

---

### 📌 Цель блока:

Закрепить всё, что студенты узнали ранее — классы, свойства, методы, `static`, пространства имён, автозагрузку через Composer — в одном компактном, но реальном примере. Это типичный код бизнес-логики, который можно встретить в интернет-магазине.

---

### 📖 Постановка задачи

Создаём класс `Product`, у которого:

- есть свойства: `name`, `price`;
- есть **статическое поле** `VAT = 0.2` (20%);
- есть метод `getPriceWithVat()` — возвращает цену с учётом НДС;
- подключается через Composer autoload.

---

### 🛠 Шаг 1: Создаём файл класса

Путь: `src/Models/Product.php`

```php
<?php

namespace App\Models;

class Product
{
    public static float $vat = 0.2;

    public function __construct(
        public string $name,
        public float $price
    ) {}

    public function getPriceWithVat(): float
    {
        return $this->price * (1 + self::$vat);
    }
}
```

---

### 🛠 Шаг 2: Убедитесь, что в `composer.json` есть:

```json
"autoload": {
    "psr-4": {
        "App\\": "src/"
    }
}
```

Если добавили это только что — выполните:

```bash
composer dump-autoload
```

---

### 🛠 Шаг 3: Пишем `index.php`

```php
<?php

require __DIR__ . '/vendor/autoload.php';

use App\Models\Product;

$product = new Product("Книга", 1000.0);

echo "Цена без НДС: {$product->price} ₽\n";
echo "Цена с НДС: {$product->getPriceWithVat()} ₽";
```

**Ожидаемый результат:**

```
Цена без НДС: 1000 ₽
Цена с НДС: 1200 ₽
```

---

### 🧪 Проверка

```bash
php index.php
```

Если всё работает — вы настроили:

- ООП с конструктором,
- использование `static`,
- подключение классов по PSR-4 через Composer.

---

### 🔍 Обсуждение и пояснения:

|Особенность|Как реализована|
|---|---|
|Инкапсуляция|Всё `public` для упрощения (можно усилить)|
|`static $vat`|Общий НДС для всех продуктов|
|`self::`|Обращение к статическому полю|
|Autoload|Работает через `vendor/autoload.php`|
|Пространство имён|`App\Models\Product`|

---

### 👨‍🏫 Интерфейс взаимодействия:

#### Вопросы студентам:

- Что произойдёт, если `Product::$vat` изменить на 0.25? _(Все товары изменятся)_
- Можно ли сделать `$vat` нестатическим? _(Да, но тогда НДС будет индивидуален для каждого товара)_
- Почему мы используем `self::` а не `$this->` в `getPriceWithVat()`?

---

### 🧠 Расширенное задание (по желанию):

Добавить метод:

```php
public static function setVat(float $vat): void {
    self::$vat = $vat;
}
```

И использовать:

```php
Product::setVat(0.1); // Изменили НДС на 10%
```

---
