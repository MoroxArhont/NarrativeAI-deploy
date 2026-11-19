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

3. Заполните `SUPABASE_PUBLISHABLE_KEY` и `AI_API_KEY`. Не коммитьте `.env`.
4. Запустите контейнер:

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
| `AI_API_KEY` | Приватный токен внешнего провайдера ИИ. Не публикуйте его. |
| `PORT` | Порт приложения (по умолчанию 3000). |
| `AI_TIMEOUT_MS` | Тайм-аут запроса к модели в миллисекундах. |

## Важно

- Файл `.env` содержит секреты и исключён из Git.
- Не используйте `sb_secret_...`, `service_role` или пароль БД в клиентском приложении.
- Если пакет GHCR приватный, серверу потребуется авторизация в GitHub Container Registry (`docker login ghcr.io`).
- Убедитесь, что GitHub Actions в основном репозитории успешно собрал и опубликовал образ перед первым запуском.
