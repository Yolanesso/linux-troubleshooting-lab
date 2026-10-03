# Разбор: Nginx 502 из-за неверного upstream

## Причина

В `nginx.conf` указан хост `api_backend`, а сервис в Compose называется `api`. Docker Compose предоставляет DNS-имена по именам сервисов, поэтому Nginx не может разрешить `api_backend`. В error log обычно появляется `api_backend could not be resolved`. Имя разрешается при запросе благодаря переменной в proxy_pass, поэтому сам Nginx запускается, а запрос получает 502.

## Исправление

Замени имя backend в `nginx.conf`:

```nginx
set $backend api:8000;
```

Перезапусти Nginx, чтобы он перечитал конфигурацию:

```bash
docker compose restart proxy
```

## Проверка

```bash
docker compose exec proxy getent hosts api
docker compose exec proxy nginx -t
curl -i http://localhost:8080/
curl -fsS http://localhost:8080/health
```

DNS-команда должна вернуть адрес контейнера API, `nginx -t` — подтвердить корректность конфигурации, а `curl` — получить HTTP 200 и ожидаемое тело ответа.

## Почему это хороший порядок

Сначала состояние и логи показывают, какой компонент отказал. Затем проверяется конкретная гипотеза — может ли прокси разрешить адрес backend. После минимального исправления конфиг проверяется и запрос повторяется снаружи через тот же маршрут, которым пользуется клиент.

