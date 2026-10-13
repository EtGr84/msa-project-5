# Комментарии

---

## POC DAG for Task 1 — пакетная обработка маркетинговых данных.

Пайплайн:
  1. read_csv_deliveries       — чтение CSV-файла со статусами доставок.
  2. read_postgres_orders      — чтение заказов из PostgreSQL.
  3. analyze_and_branch        — анализ объединённых данных и ветвление.
  4a. process_high_volume      — ветка для «большого» объёма.
  4b. process_low_volume       — ветка для «малого» объёма.
  5. send_success_email        — email при успехе (через EmailOperator).
  Failure-callback             — email при неуспехе.

На шаге analyze_and_branch намеренно реализована нестабильная логика для имитации retry.

---
