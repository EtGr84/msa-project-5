# Task 4 

## Краткое описание POC

Spring Boot 3 + Spring Batch 5:
- `FlatFileItemReader` читает `product-data.csv`.
- `ProductItemProcessor` обогащает поле `productData` значением из `loyality_data` по `productSku`.
- `JdbcBatchItemWriter` UPSERT-ит результат в `products`.
- Chunk = 10, retry=3 (TransientDataAccessException), skip=5 (FlatFileParseException).
- JobRepository хранится в том же PostgreSQL (`BATCH_*` таблицы) — поддерживает restart.
