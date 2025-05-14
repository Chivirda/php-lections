
## 🔹 2-й акад-час (0:22–0:30) — Валидатор & Form Request

### 🎯 **Цель блока**

Научиться валидировать входящие данные в Laravel с помощью встроенного валидатора и вынесенного Form Request класса для чистого кода и соблюдения SRP.

---

## 🧩 Что такое валидация?

Валидация — это проверка данных перед сохранением в БД или перед дальнейшей обработкой. Например:

- Поля обязательные?
- Корректный email?
- Минимальная длина пароля?

Laravel предоставляет удобный инструмент для этого.

---

## ✅ Шаг 1: Валидация "вручную" (в контроллере)

```php
use Illuminate\Http\Request;

public function store(Request $request)
{
    $validated = $request->validate([
        'title' => 'required|string|max:255',
        'body' => 'required|string|min:10',
    ]);

    Post::create($validated);

    return redirect()->route('posts.index')->with('success', 'Post created!');
}
```

### 🔎 Как это работает?

- Метод `validate()` автоматически вернёт ошибки валидации в сессию.
- При ошибке происходит редирект обратно с сообщением об ошибке.
- В Blade-шаблоне можно вывести ошибки через:

```blade
@if ($errors->any())
    <div class="alert alert-danger">
        <ul>
            @foreach ($errors->all() as $error)
                <li>{{ $error }}</li>
            @endforeach
        </ul>
    </div>
@endif
```

---

## ✅ Шаг 2: Form Request — вынесение правил в отдельный класс

Чтобы разгрузить контроллер и соблюдать SRP.

### 📦 Создание Form Request:

```bash
php artisan make:request StorePostRequest
```

### 📋 Пример StorePostRequest:

```php
namespace App\Http\Requests;

use Illuminate\Foundation\Http\FormRequest;

class StorePostRequest extends FormRequest
{
    public function authorize(): bool
    {
        return true; // здесь можно проверять права
    }

    public function rules(): array
    {
        return [
            'title' => 'required|string|max:255',
            'body' => 'required|string|min:10',
        ];
    }

    public function messages(): array
    {
        return [
            'title.required' => 'Поле "Заголовок" обязательно.',
            'body.required' => 'Поле "Текст" обязательно.',
        ];
    }
}
```

---

## ✅ Шаг 3: Использование Form Request в контроллере

```php
use App\Http\Requests\StorePostRequest;

public function store(StorePostRequest $request)
{
    Post::create($request->validated());

    return redirect()->route('posts.index')->with('success', 'Post created!');
}
```

### 🔎 Как это работает?

- Laravel автоматически инжектит `StorePostRequest`.
- Выполняется валидация перед входом в метод.
- В случае ошибки Laravel делает редирект обратно с $errors.
- Метод `validated()` возвращает только прошедшие проверку данные.

---

## 🧠 Почему лучше выносить валидацию в Form Request?

|В контроллере|В Form Request|
|---|---|
|Код разрастается|Чистый контроллер|
|Мешается логика запроса и валидации|Чёткое разделение ответственности|
|Сложно переиспользовать правила|Правила централизованы|

Дополнительно:

- можно кастомизировать сообщения об ошибках;
- можно добавлять авторизацию запроса (authorize());
- легко тестировать.

---

## 🧪 Интерактивное задание:

> "Создайте Form Request `StorePostRequest` с валидацией полей `title` и `body`. Используйте его в методе store() контроллера PostController. При ошибке выведите их в шаблон."

---

## ✅ Валидация — часто используемые правила:

|Правило|Описание|
|---|---|
|`required`|Обязательное поле|
|`string`, `integer`, `email`|Типы данных|
|`min:X`, `max:X`|Ограничение по длине|
|`unique:posts,title`|Уникальность|
|`exists:categories,id`|Проверка наличия в БД|
|`confirmed`|Для паролей с повтором|

---

## ✅ Полезные методы:

|Метод|Описание|
|---|---|
|`$request->validated()`|Возвращает только валидные данные|
|`$request->all()`|Все данные запроса (без валидации)|
|`$request->input('title')`|Получение отдельного параметра|

---
