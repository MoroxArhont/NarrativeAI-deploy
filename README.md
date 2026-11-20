# NarrativeAI — Deploy

Этот репозиторий содержит конфигурацию запуска готового Docker-образа. Исходный код приложения и CI находятся в основном репозитории [NarrativeAI](https://github.com/MoroxArhont/NarrativeAI).

## Требования

- Docker Engine и Docker Compose plugin.
- Доступ к образу `ghcr.io/moroxarhont/narrative-ai:latest` из GitHub Container Registry.
- Активный проект Supabase с применённой миграцией из основного репозитория.

## Первый запуск

1. Склонируйте этот репозиторий на сервер.
2. Создайте локальный `.env` из шаблона:

   ```bash
   cp .env.example .env
   ```

3. Заполните `SUPABASE_PUBLISHABLE_KEY`, `APP_URL` и `APP_ENCRYPTION_KEY`. Сгенерируйте `APP_ENCRYPTION_KEY` командой из `.env.example`; сохраните его в надёжном месте и не меняйте после того, как пользователи сохранят API-токены. Не коммитьте `.env`.
4. В Supabase Dashboard → Authentication → URL Configuration задайте Site URL равным `APP_URL` и добавьте этот адрес в Redirect URLs.
5. Пользователи смогут добавить собственные API-токены в «Настройках ИИ»; ключи шифруются на сервере и хранятся отдельно для каждого аккаунта.
6. Запустите контейнер:

   ```bash
   docker compose pull
   docker compose up -d
   ```

Приложение будет доступно на порту `3000`.

## Обновление

После публикации нового образа из основного репозитория выполните:

```bash
docker compose pull
docker compose up -d
```

## Остановка

```bash
docker compose down
```

## Переменные окружения

| Переменная | Назначение |
| --- | --- |
| `SUPABASE_URL` | URL проекта Supabase. |
| `SUPABASE_PUBLISHABLE_KEY` | Публичный publishable key; не service-role ключ. |
| `APP_URL` | Публичный HTTPS-адрес приложения для ссылок подтверждения почты. |
| `APP_ENCRYPTION_KEY` | Постоянный 32-байтовый ключ AES-256-GCM в hex (64 символа); нужен для шифрования пользовательских API-токенов. |
| `AI_API_KEY` | Необязательный общий fallback-токен; для публичного сервиса предпочтительны личные токены пользователей. |
| `PORT` | Порт приложения (по умолчанию 3000). |
| `AI_TIMEOUT_MS` | Тайм-аут запроса к модели в миллисекундах. |

## Важно

- Файл `.env` содержит секреты и исключён из Git.
- Не используйте `sb_secret_...`, `service_role` или пароль БД в клиентском приложении.
- Если пакет GHCR приватный, серверу потребуется авторизация в GitHub Container Registry (`docker login ghcr.io`).
- Убедитесь, что GitHub Actions в основном репозитории успешно собрал и опубликовал образ перед первым запуском.
- `APP_ENCRYPTION_KEY` нельзя терять или менять: без него ранее сохранённые пользовательские API-токены невозможно расшифровать.
