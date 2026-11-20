# NarrativeAI — Deploy

Этот репозиторий содержит конфигурацию публичного развёртывания готового Docker-образа. Исходный код и CI находятся в [основном репозитории NarrativeAI](https://github.com/MoroxArhont/NarrativeAI).

## Структура

```text
NarrativeAI-deploy/
├── certs/                 # TLS-файлы на сервере (не коммитить)
├── nginx/
│   └── nginx.conf
├── .gitignore
├── docker-compose.yml
├── .env.example
└── README.md
```

## Требования

- Linux-сервер с Docker Engine и Docker Compose plugin.
- Домен, направленный на сервер.
- TLS-сертификат и приватный ключ для домена.
- Доступ к образу `ghcr.io/moroxarhont/narrative-ai:latest`.

## Подготовка

1. Склонируйте репозиторий на сервер.
2. Создайте локальный файл окружения: `cp .env.example .env`.
3. Заполните `.env`: `APP_URL` должен быть публичным HTTPS-адресом, а `APP_ENCRYPTION_KEY` — постоянным 64-символьным hex-ключом.
4. В Supabase Dashboard → Authentication → URL Configuration задайте Site URL равным `APP_URL` и добавьте этот адрес в Redirect URLs.
5. Поместите сертификат и ключ в локальную папку `certs/`:
   - `fullchain.pem` — сертификат и цепочка;
   - `privkey.pem` — приватный ключ.

   Эти файлы исключены из Git. Никогда не коммитьте приватный TLS-ключ.
6. Если используете другие имена файлов, измените пути в `nginx/nginx.conf`.

## Запуск и обновление

Убедитесь, что TLS-файлы и `.env` созданы, затем:

```bash
docker compose pull
docker compose up -d
docker compose ps
```

Nginx принимает HTTP на порту 80 и перенаправляет на HTTPS (443). Порт приложения 3000 доступен только внутри Docker-сети.

Для обновления повторите `docker compose pull && docker compose up -d`. Для остановки: `docker compose down`.

## Переменные окружения

| Переменная | Назначение |
| --- | --- |
| `SUPABASE_URL` | URL единого проекта Supabase владельца приложения. |
| `SUPABASE_PUBLISHABLE_KEY` | Публичный publishable key, не service-role ключ. |
| `APP_URL` | Публичный HTTPS-адрес приложения. |
| `APP_ENCRYPTION_KEY` | Постоянный AES-256-GCM ключ: 32 случайных байта в hex (64 символа). |
| `AI_API_KEY` | Необязательный общий fallback-токен. |
| `PORT` | Порт приложения (по умолчанию 3000). |
| `AI_TIMEOUT_MS` | Тайм-аут запросов к модели в миллисекундах. |

Сгенерируйте ключ один раз: `node -e "console.log(require('crypto').randomBytes(32).toString('hex'))"`. Сохраните резервную копию. Не меняйте и не теряйте его после сохранения пользовательских API-токенов.

## Безопасность

- `.env`, TLS-сертификаты и приватные ключи исключены из Git.
- Не используйте `sb_secret_...`, `service_role` или пароль БД в клиентском приложении.
- Если пакет GHCR приватный, выполните `docker login ghcr.io`.
- Для продакшена нужен действительный сертификат доверенного центра сертификации. Сертификат localhost подходит только для локального тестирования.
