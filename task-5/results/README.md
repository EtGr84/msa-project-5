# TradeWare Stock ETL — Monitoring, Logging & Alerting

## Используемый стек

- **Spring Batch + Micrometer** — генерация метрик
- **Prometheus** — сбор метрик
- **Grafana** — дашборды и алерты
- **Filebeat → Logstash → Elasticsearch → Kibana (ELK)** — централизованное логирование
- **Docker Compose** — локальное развёртывание

---

## Запуск проекта

```bash
docker compose up --build
```

---

## Запуск ETL job

```bash
docker compose up app
```

## Просмотр метрик (Prometheus)

http://localhost:9090
