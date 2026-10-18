# Изменения относительно task-5

- `spring.batch.job.enabled=false` — Job больше не запускается при старте.
- REST endpoint `POST /api/jobs/import-products?fileName=...` для запуска Job.
- Подключён Micrometer Tracing (Brave) — `traceId` и `spanId` автоматически
  попадают в MDC.
- `MdcUriFilter` кладёт URI запроса в MDC под ключом `uri`.
- `logback-spring.xml` пишет JSON-логи с полями `traceId`, `spanId`, `uri`,
  которые через Filebeat → Logstash → Elasticsearch попадают в Kibana.
