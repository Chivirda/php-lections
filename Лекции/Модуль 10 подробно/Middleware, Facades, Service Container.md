
## 🔹 2-й акад-час (0:30–0:38) — Middleware, Facades, Service Container

### 🎯 **Цель блока**

Познакомить студентов с фундаментальными механизмами Laravel: middleware, фасадами и контейнером сервисов. Показать, как Laravel связывает компоненты приложения.

---

## ✅ 1. Middleware (Промежуточные обработчики)

### 📋 Что такое middleware?

- Это "слой", который обрабатывает HTTP-запрос **до** и **после** контроллера.
- Примеры: аутентификация, проверка CSRF, логирование, CORS.

### 📦 Пример встроенных:

- `web` — включает сессии, CSRF, cookies.
- `auth` — проверяет авторизацию.
- `throttle` — ограничивает частоту запросов.

### 📄 Как использовать middleware?

```php
Route::get('/dashboard', [DashboardController::class, 'index'])
    ->middleware('auth');
```

### 📦 Создание кастомного middleware:

```bash
php artisan make:middleware CheckAdmin
```

Пример реализации:

```php
public function handle($request, Closure $next)
{
    if (auth()->user()?->is_admin) {
        return $next($request);
    }

    abort(403, 'Access denied');
}
```

Регистрация в `app/Http/Kernel.php`:

```php
'admin' => \App\Http\Middleware\CheckAdmin::class,
```

---

## ✅ 2. Facades (Фасады)

### 📋 Что такое Facade в Laravel?

- Это статическая оболочка над классом в контейнере сервисов.
- Дают удобный статичный интерфейс, но под капотом используют DI.

### 📦 Примеры фасадов:

|Фасад|Назначение|
|---|---|
|`Cache::`|Работа с кэшом|
|`Auth::`|Аутентификация|
|`Log::`|Логирование|
|`Route::`|Работа с маршрутами|
|`Config::`|Работа с конфигами|

### 📄 Пример Cache:

```php
use Illuminate\Support\Facades\Cache;

$posts = Cache::remember('posts.all', 60, function () {
    return Post::all();
});
```

- `remember()` проверяет кэш по ключу, если нет — выполнит callback и сохранит результат.

---

## ✅ 3. Service Container (Контейнер зависимостей)

### 📋 Что это?

- Это ядро Laravel, которое управляет созданием и связыванием классов.
- Обеспечивает **Dependency Injection (DI)**.

### 📦 Пример внедрения зависимости:

```php
use Psr\Log\LoggerInterface;

class PostController extends Controller
{
    public function __construct(protected LoggerInterface $logger) {}

    public function index()
    {
        $this->logger->info('Post index accessed');
        return view('posts.index');
    }
}
```

- Laravel автоматически найдёт и передаст реализацию `LoggerInterface` из контейнера.
    

### 📄 Регистрация singleton-сервиса:

```php
app()->singleton('MyService', function() {
    return new \App\Services\MyService();
});
```

Или с типизированным DI:

```php
public function __construct(protected \App\Services\MyService $myService) {}
```

---

## ✅ Как это связывается вместе?

|Компонент|Роль|
|---|---|
|Middleware|Фильтруют запросы до/после контроллера|
|Facades|Упрощают доступ к сервисам через статический синтаксис|
|Service Container|Управляет зависимостями и связывает всё приложение|

---

## 🧠 Почему это важно?

- **Middleware** разгружает контроллеры от "сквозной" логики.
- **Facades** дают удобный API для работы с сервисами.
- **Service Container** обеспечивает масштабируемость и слабую связанность компонентов.

---

## 🧪 Интерактивное задание:

> "Создайте middleware `CheckAdmin`, который запрещает доступ к маршруту `/admin` всем, кроме пользователей с флагом `is_admin`. Используйте фасад Auth."

```php
public function handle($request, Closure $next)
{
    if (!Auth::user() || !Auth::user()->is_admin) {
        abort(403, 'Access denied');
    }

    return $next($request);
}
```

```php
Route::get('/admin', function () {
    return 'Admin panel';
})->middleware('admin');
```

---
