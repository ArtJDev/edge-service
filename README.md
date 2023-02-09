# edge-service
Сервис API Gateway для микросервисов [product-service](https://github.com/ArtJDev/product-service) и [order-service](https://github.com/ArtJDev/order-service), входящих в систему ITBooks

**Стек: Java 17, Spring Boot 3.0, Spring Cloud Gateway, Spring Cloud Circuit Breaker, Resilence4J, Redis, Testcontainers, Gradle, Docker.**
## Описание
Проект представляет собой сервис API Gateway для маршрутизации запросов к микросервисам [product-service](https://github.com/ArtJDev/product-service) 
и [order-service](https://github.com/ArtJDev/order-service)
Сервис использует следующие фильтры (в порядке приоритета):
1. RequestRateLimiter для ограничения количества входящих запросов (Server-side)
2. Таймаут httpclient, который используется Netty для проброски запросов нижележащим сервисам
3. Фильтр CircuitBreaker, для ограничения запросов к недоступным сервисам и перенаправления трафика на другие эндпоинты.
4. Фильтр Retry, для установления количества повторных GET запросов к сервисам, которые в текущий момент могут быть вдруг недоступны

Счетчик ограничений RequestRateLimiter хранится в сессии, которая в свою очередь хранится в базе Redis. 
Настройки ограничений CircuitBreaker выполнены с помощью Resilence4J.
Написан интеграционный тест загрузки контекста с использованием Testcontainers.
## Запуск
Для работы сервиса необходимо сначала развернуть и запустить базу данных Redis в контейнере Docker. Для этого необходимо на компьютере иметь запущенное приложение Docker.
Cоздать папку на своем компьютере, скачать и положить в неё из [этого](https://github.com/ArtJDev/itbooks-deployment/tree/main/docker) репозитория скрипт docker-compose.yml.
Открыть окно терминала, перейти в созданную папку со скриптом Docker и выполнить команду `docker-compose up -d itbooks-redis`. 
Docker подтянет образ Redis и запустит его в контейнере "itbooks-redis".
Для проверки, что все работает необходимо также запустить сервисы [product-service](https://github.com/ArtJDev/product-service)
и [order-service](https://github.com/ArtJDev/order-service).
Запуск приложения осуществляется командой `./gradlew bootRun`. Эндпоинты для GET запросов: - `http://localhost:9000/books`, `http://localhost:9000/orders`
