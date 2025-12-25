# Amazon Athena

- Amazon Athena is a serverless, interactive query service
- makes it easy to analyze large amounts of data stored directly in Amazon S3 using standard SQL
- Cost: $5 per 1TB of data scanned

# Scaling with Athena

- Concurrency is limited in Athena per account per region. 
    - Default limit is 20 parallel queries. 
    - This is a soft limit and can be increased by requesting a limit increase from AWS.

## Best Practices for Scaling

- Use partitioning to reduce the amount of data scanned
- Use columnar storage formats like Parquet or ORC for better performance
- Monitor query execution time and optimize queries for better efficiency
- Capacity reservations can help ensure consistent performance.