# Kafka Flow design 
## Workflow overview:
### Scraping code -> Fetcher (producer) inserts hash into Postgres (unique, status=seen) -> if insert is new, publishes to **raw-jobs** topic -> **Filter** (consumer/producer) reads, matches criteria, publishes to **matched-jobs** topic -> **Notifier** (consumer) reads, sends notification, updates status to sent in Postgres

## topics:
- **raw-jobs** (key = job_hash, partitions = 1, producer = Fetcher, consumer group = filter-group): Raw jobs published by Fetcher after successful dedupe insert
- **matched-jobs** (key = job_hash, partitions = 1, producer = Filter, consumer group = notifier-group): Jobs matching CV criteria, produced by Filter, consumed by Notifier