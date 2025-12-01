# Проблема 1: Аутентифікація PostgreSQL

``` FATAL: password authentication failed for user "postgres" ```

Причина:
Виник конфлікт пріоритетів між файлами .env та .env.docker, і  додаток отримував один пароль (SET), тоді як контейнер PostgreSQL очікував інший. Крім того, PostgreSQL зберігає пароль у томі даних.

Виправлення:
Синхронізація конфігурації: У файлах .env та .env.docker паролі POSTGRES_PASSWORD та DB_PASSWORD були встановлені на єдине узгоджене значення (наприклад, root).
Видалення тому даних: Використано команду docker-compose down -v для видалення старого тому (pgdata), що дозволило PostgreSQL створити нову базу даних з коректним паролем.

# Проблема 2: Підключення до Redis (Connection Refused)

``` redis.exceptions.ConnectionError: Error 111 connecting to localhost:6379. Connection refused. ```

Причина:
У мережі Docker Compose контейнер api не знає, що таке localhost для Redis. Він мав використовувати назву сервісу (redis). Початковий код ініціалізації Redis використовував redis://localhost:6379 за замовчуванням.

Виправлення (Файл main.py):
Логіка ініціалізації клієнта Redis була змінена, щоб динамічно використовувати змінні оточення REDIS_HOST та REDIS_PORT, які в docker-compose коректно вказують на сервіс redis.

 У файлі main.py:
``` REDIS_HOST = os.getenv("REDIS_HOST", "localhost")```
```REDIS_PORT = os.getenv("REDIS_PORT", "6379")```

 Тепер це формує коректний URL: redis://redis:6379
```REDIS_URL = f"redis://{REDIS_HOST}:{REDIS_PORT}" ```

```redis_client = redis.from_url(REDIS_URL, decode_responses=True)```


docker-compose.yml

помилка YAML, неправильна структура env, дубльовані порти

— прибрали дубльований ports
— додали POSTGRES_PASSWORD
— привели YAML до коректного вигляду
