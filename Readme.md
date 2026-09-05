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
