# Task 3.  Инструкции

# Запуск демо

---

```bash
cd task-3/results
chmod +x run-demo.sh
./run-demo.sh
```
---

# Описание работы скрипта: 

1. поднимает MiniKube (если не запущен);
2. собирает Docker-образ `shipments-exporter:v1.0.0` **внутри docker-демона MiniKube** (без registry);
3. применяет все манифесты;
4. дожидается готовности Postgres (init.sql с 5000 строк применяется автоматически);
5. удаляет предыдущий ручной Job (если был) и запускает новый;

# Состав артефактов

- README.md                       /этот файл
- run-demo.sh                    / «один скрипт — всё развёрнуто и запущено»
- Dockerfile                     / образ python:3.13-slim, non-root
- requirements.txt               / psycopg[binary]
- export.py                  / экспортёр: SELECT * FROM shipments → CSV
- namespace.yaml
- secret.yaml                / креды к Postgres
- postgres-configmap.yaml    / init.sql, монтируется в Postgres
- postgres-pvc.yaml          / PVC для данных Postgres
- postgres-deployment.yaml
- exporter-cronjob.yaml      / CronJob: 0 20 * * *, Europe/Moscow
- exporter-manual-job.yaml   / ручной Job для демонстрации
