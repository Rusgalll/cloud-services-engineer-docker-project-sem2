## Backend

### Сборка
```bash
docker build -t momo-backend:1.0 ./backend
```

Версии базовых образов можно передать через аргументы сборки:
```bash
docker build --build-arg GO_VERSION=1.26.7 --build-arg ALPINE_VERSION=3.24.1 -t momo-backend:1.0 ./backend
```

Аргументы применяются во время сборки. Порт backend задан в исходном коде как `8081`; переменная окружения для его изменения не реализована.

### Запуск
```bash
docker run -d --name momo-backend -p 8081:8081 momo-backend:1.0
```

Backend доступен по адресу [http://localhost:8081](http://localhost:8081).

## Frontend

### Сборка

Команды выполняются из корня репозитория.

```bash
docker build -t momo-frontend:1.0 ./frontend
```

Версии базовых образов и адрес backend можно передать через аргументы сборки:

```bash
docker build --build-arg NODE_VERSION=22 --build-arg NGINX_TAG=stable-alpine --build-arg VUE_APP_API_URL=http://localhost:8081 -t momo-frontend:1.0 ./frontend
```

### Запуск

Сначала нужно запустить backend.

```bash
docker run -d --name momo-frontend -p 80:8080 momo-frontend:1.0
```

Frontend доступен по адресу [http://localhost/momo-store/](http://localhost/momo-store/). Запрос к [http://localhost](http://localhost) перенаправляется на этот путь. Снаружи используется порт `80`, внутри контейнера Nginx слушает `8080`.