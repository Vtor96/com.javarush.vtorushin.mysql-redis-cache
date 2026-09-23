# Redis Caching - MySQL vs Redis Performance (Module 4: Database)

Финальный проект 4 Модуля «Работа с БД» для курса JavaRush. Кэширование часто запрашиваемых данных из MySQL в Redis и сравнение скорости чтения. База `world` (страны, города, языки) разворачивается в Docker, данные трансформируются в DTO и переносятся в Redis, затем замеряется время выборки одинаковых записей из обеих БД.

## Что сделано

- 3 JPA-сущности: `City`, `Country`, `CountryLanguage` с маппингом на схему `world`.
- Оптимизация запросов Hibernate: `FetchType.LAZY` для `@OneToOne`, `join fetch` для языков — с 249 запросов до 11.
- Трансформация данных: `City` + `Country` + `CountryLanguage` → `CityCountry` (DTO с только нужными полями).
- Запись в Redis через Lettuce-клиент: ключ — id города, значение — JSON.
- Чтение из Redis (десериализация через Jackson) и MySQL (через Hibernate).
- Сравнение времени: Redis быстрее MySQL примерно в 1.5 раза.
- P6Spy — логирование SQL-запросов с параметрами.
- Оба хранилища — в Docker-контейнерах.

## Стек

- Java 18
- Hibernate 5.6 (hibernate-core-jakarta)
- MySQL 8 (Docker)
- Redis (Docker, redis-stack)
- Lettuce 6.2 (Redis-клиент)
- Jackson 2.16 (JSON-сериализация)
- P6Spy 3.9 (логирование SQL)
- Maven 3

## Требования

- JDK 18+
- Maven 3.6+
- Docker
- Свободные порты 3306 (MySQL) и 6379 (Redis)

## Как запустить

### 1. Поднять MySQL в Docker

```bash
docker run --name mysql -d -p 3306:3306 \
  -e MYSQL_ROOT_PASSWORD=root \
  --restart unless-stopped \
  -v mysql:/var/lib/mysql mysql:8
```

### 2. Развернуть дамп БД world
Импортировать дамп через Workbench, DBeaver или psql (схема включена в файл).

### 3. Поднять Redis в Docker
# С redis-insight (веб-интерфейс на порту 8001)
docker run -d --name redis-stack -p 6379:6379 -p 8001:8001 redis/redis-stack:latest

# Или без него
docker run -d --name redis -p 6379:6379 redis:latest

### 4. Настроить подключение
В классе Main в методе prepareRelationalDb() укажите свои данные:
properties.put(Environment.USER, "root");
properties.put(Environment.PASS, "root");

### 5. Запустить
mvn clean compile exec:java -Dexec.mainClass="Main"
