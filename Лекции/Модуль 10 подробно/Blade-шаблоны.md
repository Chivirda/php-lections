
## 🔹 0:35–0:45 — Blade-шаблоны

### 🎯 **Цель блока**

Показать, как Laravel использует Blade — встроенный шаблонизатор, позволяющий писать читаемый, безопасный и модульный HTML с динамическими вставками PHP.

---

## 🧩 Что такое Blade?

Blade — это шаблонизатор Laravel, который:

- компилируется в обычный PHP-код;
- **поддерживает наследование шаблонов**, секции, макеты;
- **автоматически экранирует** переменные (безопасность от XSS);
- позволяет использовать `@директивы` вместо сырого PHP (`@if`, `@foreach`, `@csrf` и т.д.).

---

## 📂 Где находятся шаблоны?

По умолчанию в:

```
resources/views/
```

Файл `resources/views/hello.blade.php` будет вызываться так:

```php
return view('hello'); // без .blade.php
```

---

## 📋 Пример простого шаблона `hi.blade.php`

```blade
@extends('layouts.app')

@section('content')
    <h1>Hello, {{ $name }}</h1>

    <form method="POST" action="/contact">
        @csrf
        <label>Email:</label>
        <input name="email" type="email">
        <button type="submit">Send</button>
    </form>
@endsection
```

---

## 🔐 Автоэкранирование

```blade
{{ $title }}          // экранируется (htmlspecialchars)
{!! $rawHtml !!}      // не экранируется (ОПАСНО!)
```

> Это защищает от **XSS-атак** (подстановка `<script>` в поле формы и т.д.)

---

## 🧱 Расширение макета (`@extends`)

Пример базового шаблона `layouts/app.blade.php`:

```blade
<html>
  <head>
    <title>@yield('title', 'MyApp')</title>
  </head>
  <body>
    <div class="container">
      @yield('content')
    </div>
  </body>
</html>
```

> А в дочернем шаблоне:

```blade
@extends('layouts.app')

@section('title', 'Hello Page')

@section('content')
    <h1>Hello, {{ $name }}</h1>
@endsection
```

---

## 💡 Популярные Blade-директивы

|Директива|Описание|
|---|---|
|`@extends`|Наследование макета|
|`@section` / `@yield`|Объявление и вставка секций|
|`@if`, `@elseif`, `@else`, `@endif`|Условия|
|`@foreach`, `@for`, `@while`|Циклы|
|`@csrf`|Вставка CSRF-токена|
|`@include('partials.header')`|Вставка другого шаблона|
|`@auth`, `@guest`|Проверка авторизации|

---

## 🧠 Объяснение: зачем Blade?

- Отделение логики от отображения.
- Безопасность (`{{ }}` экранирует HTML).
- Расширяемость (макеты, частичные шаблоны).
- Читабельность: `@if` vs `<?php if (...) ?>`.

---

## 🧪 Интерактивное задание:

> "Создайте Blade-шаблон `hi.blade.php`, который приветствует пользователя по имени и отображает форму с защитой CSRF."

```blade
<!-- resources/views/hi.blade.php -->
@extends('layouts.app')

@section('content')
    <h1>Привет, {{ $name }}!</h1>

    <form method="POST" action="/hello">
        @csrf
        <label>Сообщение:</label>
        <input name="message">
        <button type="submit">Отправить</button>
    </form>
@endsection
```

---

## 🔄 Рендеринг из маршрута

```php
Route::get('/hello/{name}', function ($name) {
    return view('hi', ['name' => $name]);
});
```

---

