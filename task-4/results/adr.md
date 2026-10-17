# Внедрение Spring Batch для обработки отчётов по остаткам

## Выбор решения ([оценка] - от 5 до 1, где 5 максимальная)

| Критерий / Решение | **Spring Batch** | Apache Airflow | K8s Job + plain Java | Apache Spark | Apache NiFi |
|---|---|---|---|---|---|
| Chunk-based обработка из коробки | [5] | [1] (оркестратор) | [1] | [5] | [5] |
| Restart / skip / retry on failure | [5] native | [3] task-level | [1] | [3] | [5] |
| Интеграция с Java / Spring-экосистемой TradeWare | [5] | [1] (Python) | [5] | [3] | [1] |
| Порог входа для команды (Java 11+) | [5] | [2] | [5] | [2] | [3] |
| Метрики Micrometer / Prometheus | [5] | [3] | [3] | [3] | [3] |
| Ресурсоёмкость | [5] | [3] | [4] | [1] | [3] |
| Подходит под ~400k строк/день | [5] | [5] (как оркестратор) | [5] | Избыточен | [5] |
| Горизонтальное масштабирование | [5] (partitioning / remote chunking) | [5] | [3] ручное | [5] | [5] |
| Готовность к облаку (GKE) | [5] (Spring Boot image) | [5] | [5] | [5] | [3] |
| ИТОГО | 45 | 27 | 37 | [5] | [3] |

## Р

Использовать **Spring Batch 5 (Spring Boot 3)** как ETL-движок:

- `FlatFileItemReader` — чтение CSV из GCS (локально — classpath);
- `ItemProcessor` — обогащение данными `loyality_data` из PostgreSQL;
- `JdbcBatchItemWriter` — батч-вставка в `products`;
- chunk size = 10 (настраивается), `faultTolerant().retry(...).skip(...)`;
- JobRepository в PostgreSQL (таблицы `BATCH_*`) для restart/idempotency;
- запуск как Spring Boot контейнер в Kubernetes (CronJob / Job);
- метрики через Micrometer → Prometheus, логи → ELK.

## Последствия

**Плюсы:**
- Команда Java умеет поддерживать Spring-стек — минимум onboarding.
- Chunk + transaction boundary решают проблему построчных UPDATE.
- Restart «с места падения» — за счёт JobRepository.
- Легко перенести в GKE: Spring Boot uber-jar + Dockerfile уже есть.
- Готов к расширению: partitioned step для x2–x3 пика.

**Минусы / риски:**
- Для действительно «больших» данных (десятки млн) потребуется remote partitioning.
- Spring Batch не является оркестратором — расписание остаётся за k8s CronJob / Spring Cloud Data Flow.
- Мониторинг нужно настраивать отдельно (Micrometer + Prometheus).

## План имплементации (high-level)

1. POC-приложение (настоящее задание): CSV → enrich → Postgres.
2. Вынесение Job в отдельный Spring Boot сервис.
3. Образ в GCR, CronJob в GKE, креды через Secret Manager.
4. Metrics endpoint `/actuator/prometheus`, логи → stdout → ELK.
5. Масштабирование: partitioned step по диапазонам `productSku`.
