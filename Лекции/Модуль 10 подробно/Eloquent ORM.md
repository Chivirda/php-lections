
## 🔹 2-й академический час (0:00–0:12) — Eloquent ORM: модель, миграция, массовое назначение

### 🎯 **Цель блока**

Научиться работать с **Eloquent ORM**: создать модель и таблицу, научиться выполнять операции чтения и записи, правильно использовать массовое назначение (`$fillable`).

---

## 🧩 Что такое Eloquent ORM?

- **ORM (Object-Relational Mapping)** — способ работы с БД через объекты PHP.
- В Laravel за это отвечает **Eloquent**.
- Каждая модель соответствует таблице БД.
- Вместо SQL-запросов мы работаем с экземплярами классов.

---

## ✅ Шаг 1: Создание модели и миграции

```bash
php artisan make:model Post -m
```

- создаст модель `app/Models/Post.php`
- добавит миграцию: `database/migrations/202x_xx_xx_create_posts_table.php`

---

## ✅ Шаг 2: Миграция таблицы

Пример миграции для таблицы `posts`:

```php
use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration {
    public function up(): void
    {
        Schema::create('posts', function (Blueprint $table) {
            $table->id();
            $table->string('title');
            $table->text('body');
            $table->timestamps();  // created_at, updated_at
        });
    }

    public function down(): void
    {
        Schema::dropIfExists('posts');
    }
};
```

Применение миграции:

```bash
php artisan migrate
```

> В БД появится таблица `posts`.

---

## ✅ Шаг 3: Работа с моделью Post

Пример модели:

```php
namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;

class Post extends Model
{
    use HasFactory;

    protected $fillable = ['title', 'body'];
}
```

### 🔐 Зачем нужен `$fillable`?

- Laravel защищает от **mass-assignment vulnerabilities**.
- Только поля, перечисленные в `$fillable`, можно массово назначать через `create()` и `update()`.

---

## ✅ Шаг 4: CRUD-операции через Eloquent

### 📥 Создание записи:

```php
Post::create([
    'title' => 'First Post',
    'body' => 'This is a post created via Eloquent.',
]);
```

### 📤 Получение всех записей:

```php
$posts = Post::all();

foreach ($posts as $post) {
    echo $post->title;
}
```

### 📝 Обновление:

```php
$post = Post::find(1);
$post->update(['title' => 'Updated Title']);
```

### 🗑 Удаление:

```php
Post::destroy(1);
```

---

## 🧠 Важные моменты:

|Пункт|Объяснение|
|---|---|
|`Post::all()`|Получить все записи|
|`Post::find($id)`|Найти по id|
|`Post::create([...])`|Массовое создание записи|
|`$post->update([...])`|Массовое обновление|
|`$fillable`|Разрешённые для массового заполнения поля|
|`timestamps`|Автообновляемые поля `created_at` и `updated_at`|

---

## 🧪 Интерактивное задание:

> "Создайте таблицу `posts`, добавьте 2 записи через Eloquent, выведите их на экран через Blade-шаблон."

1. Создать модель и миграцию.
2. Выполнить `php artisan migrate`.
3. В контроллере или маршруте:

```php
Post::create(['title' => 'Post 1', 'body' => 'Content 1']);
Post::create(['title' => 'Post 2', 'body' => 'Content 2']);

$posts = Post::all();

return view('posts.index', compact('posts'));
```

4. Шаблон `resources/views/posts/index.blade.php`: 

```blade
@foreach ($posts as $post)
    <h2>{{ $post->title }}</h2>
    <p>{{ $post->body }}</p>
@endforeach
```

---

## 📝 Почему Eloquent удобен?

- Единый подход для CRUD.
- Мягко оборачивает SQL.
- Легко тестируется.
- Поддерживает связи (`hasOne`, `hasMany`, `belongsTo` и т.д.).

---
