# Сценарий 01: Nginx возвращает 502

## Ситуация

После обновления конфигурации пользователи получают `502 Bad Gateway`. Контейнеры запущены, но приложение через Nginx недоступно. Найди причину, опираясь на наблюдения.

## Запуск

```bash
docker compose up -d --build
curl -i http://localhost:8080/
```

Ожидаемый результат на старте: HTTP `502`.

## Твоя задача

1. Проверь состояние контейнеров и их логи.
2. Посмотри, куда Nginx отправляет запросы.
3. Проверь доступность API из сети Compose.
4. Исправь причину и проверь ответ через Nginx.
5. Запиши короткий отчёт по шаблону в `../../templates/incident-report.md`.

Подсказка: сравни имя сервиса в `proxy_pass` с именем сервиса в `compose.yaml`.

## Диагностические команды

```bash
docker compose ps
docker compose logs --tail=50 proxy
docker compose logs --tail=50 api
docker compose exec proxy nginx -T
docker compose exec proxy getent hosts api
```

После исправления проверь:

```bash
curl -i http://localhost:8080/
curl -fsS http://localhost:8080/health
```

Ожидается `HTTP/1.1 200 OK`, текст `hello from api` и `ok` с `/health`.

Разбор решения: [SOLUTION.md](SOLUTION.md).

